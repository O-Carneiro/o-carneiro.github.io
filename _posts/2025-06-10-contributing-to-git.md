---
layout: post
title: Contributing to git
date: 2025-06-10 22:43 -0300
---

Contributing to Git was way better in relation to the kernel,  
at least to me, even though me and my colleagues were ghosted  
after our initial patches.

## What was done

### The issue

When reading instructions on how to contribute to Git, they provide  
a helpful list of what they call "microprojects" to get started  
on Git development. Naturally, we went on to one of those.

We chose the *disable the `-Wsign-compare` warning*, which is a warning  
to alert (obviously) the developers when two variables are being compared,  
but one of them is signed and the other isn’t — and therefore  
one may overflow sooner than the other, or just miscompare because a  
negative number is misunderstood. In other words: it warns you  
that you are making a faulty comparison.

I turned my attention to the `graph.c` file — the file that handles the  
`git log --graph` drawing — and had to deal with this comparison:

```c
static void graph_pad_horizontally(struct git_graph *graph, struct graph_line *line)
{
	/*
	 * Add additional spaces to the end of the strbuf, so that all
	 * lines for a particular commit have the same width.
	 *
	 * This way, fields printed to the right of the graph will remain
	 * aligned for the entire commit.
	 */
	if (line->width < graph->width)
		graph_line_addchars(line, ' ', graph->width - line->width);
}
````

The `if (line->width < graph->width)` was comparing `size_t` with `int`.
Given that `size_t` is unsigned, it triggers the warning.

### My fix

Here's the code that fixed it:

```c
struct graph_line {
	struct strbuf *buf;
	size_t width;
};
```

Yes, pretty underwhelming.

However, the discussion brought by it is... disproportionate, but cool.

Junio C. Hamano answered my patch and asked me a question:
Why did I change `graph_line->width`'s type, instead of `git_graph->width`'s?
Also, he said a very heartwarming phrase:

> "Your change doesn't make the code worse."

Oh yes.

Answering his question, I realized how stupid I was when coding the patch.
My reason was, basically: "git\_graph struct is bigger than graph\_line,
so I made small comply with big".

He also noted that he prefers to use `int` when `size_t` is not absolutely needed,
and that in this file's case, `int` won’t likely be a problem because the variable
counts columns in the Git graph drawing. He said:

> "`git log --graph` output is certainly something that should not exceed thousands for sane people"

So yeah, kind of on the fence about my change.
He never answered my response, and I never got to raising attention to it because of:

## My pal's contributions

My colleagues that were also contributing to Git attacked the same microproject.
One of them spawned a [huge 4-answer-long discussion](https://lore.kernel.org/git/xmqqsekx8yef.fsf@gitster.g/T/#t)
which ended up making the microproject removed from the microproject page...
Indirect win?

What happened was, the maintainer team had long thought this warning was kind of
ass. And then, in the span of a few days, 3 unknowns sent patches blindly fixing
this issue in not-so-great ways and lacking context. It made them fed up with dealing with this
and removing it.

That’s why I did not poke them about it again.

* [My contribution](https://lore.kernel.org/git/xmqqtt5d7e9z.fsf@gitster.g/T/#t)
* [Fernando's contribution](https://lore.kernel.org/git/xmqqsekx8yef.fsf@gitster.g/T/#t)
* [Lucas' contribution](https://lore.kernel.org/git/xmqqplg3gf9b.fsf@gitster.g/T/#t)

## Conclusion

Git is a cool community, and I hope to contribute further to it in the future.
