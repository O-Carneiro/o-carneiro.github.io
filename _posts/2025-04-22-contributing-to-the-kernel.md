---
layout: post
title: First issue+patchset
date: 2025-04-22 23:30 -0300
---

Contributing to the kernel was at the same time easier an harder than
it would be. I am aware than me and me group were author of no "big 
changes", and it was exactly that was suprised me. How come such little
changes were suggested by a maitainer? It got me wondering how many 
of these little "odd jobs" are laying around the kernel waiting to be
done

### What did I actually do? <small> you may be asking yourself </small>

We picked the iio_device_claim_direct() suggestion task. It consists of
adding this function call in some cases, to prevent concurrent access 
of the device in differente modes. The sudden mode change can screw up
the data being read by injecting config bytes in the readings.

From what I understood, the function acts as some sort of "mutex" that 
locks the mode of the device and prevents this.

Here's an in-code example:

```c
static int ads131e08_debugfs_reg_access(struct iio_dev *indio_dev,
                                        bool readval, u8 reg, u8 writeval)
{
    struct ads131e08_state *st = iio_priv(indio_dev);

    if (!iio_device_claim_direct(indio_dev))
        return -EBUSY;

    if (readval) {
        int ret = ads131e08_read_reg(st, reg);
        *readval = ret;
        return ret;
    }

    iio_device_release_direct(indio_dev);
    return ads131e08_write_reg(st, reg, writeval);
}
```
It looks similar to a mutex clause in concurrent programming. 

The whoke patchest was basically this change in 2 more places: 
 - In ad4000.c write_raw_get_fmt()
 - In ads131e08.c ads131e08_trigger_handler(), but we're not so 
 sure about this change.

### Why was this harder and easier?

You see, the actual "coding" part (even though the concepts involved are
very complex) was very quick. Around 10 loc total. I would be lying if i 
said I 100% understood what I was doing, but after some gut feeling and 
"looks good to me"s, me and my partners confident enough to send the patchset.

This is when things got harder than I expected. Its not a simple:
```bash
git commmit -m "ft - adds device mode guard clause" 
git push
```

The whole processes of linting(?) the patchset and fixing got me using git rebase,
thinking about what a concise-yet-good-enough subject coulde be, writing a cover 
letter paragraph, figuring out smtp with gmail, etc. Very bureaucratic, harder than
I was used to. In hindsight, though, it makes sense that a project as complex as the 
linux kernel would be very organized and attentive to good communication overall.

Let's just pray it gets in.
