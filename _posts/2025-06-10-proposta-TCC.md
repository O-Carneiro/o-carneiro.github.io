---
layout: post
title: Coleta centralizada de telemetria de uso de software; Uma Aplição para o Kernel Workflow (kw)
date: 2026-04-21 20:17 -0300
---

# 1. Introdução

O desenvolvimento do Kernel Linux, embora fundamental para a infraestrutura tecnológica moderna, possui uma curva de aprendizado íngreme. O fluxo de contribuição exige adaptação a processos específicos, como o gerenciamento de *patches* e o uso intenso de listas de e-mail. Para reduzir essa barreira de entrada e otimizar o tempo dos desenvolvedores, a ferramenta *Kernel Workflow* (`kw`) foi criada para automatizar e unificar diversas rotinas do fluxo de trabalho do kernel.

Como o `kw` é um projeto em contínuo desenvolvimento e objeto ativo de pesquisa no IME-USP, compreender como os usuários interagem com a ferramenta é essencial para guiar novas melhorias e identificar gargalos. Atualmente, o registro de métricas de uso ocorre de forma estritamente local, permitindo a extração simples desses dados por meio do comando `kw report`. Para conduzir estudos quantitativos, seria necessário organizar campanhas para que os usuários extraiam e enviem esses relatórios manualmente, uma abordagem ineficiente, pouco escalável e que dificulta análises globais. Vale ressaltar que a definição exata de quais dados e métricas serão coletados não compõe o escopo deste trabalho, que foca exclusivamente na construção da infraestrutura técnica necessária para viabilizar essa coleta.

Para suprir essa lacuna, este trabalho propõe o desenvolvimento de um servidor de telemetria centralizado para o `kw`. A infraestrutura automatizará o recebimento e o armazenamento dos dados de execução, sem exigir ação direta contínua dos usuários, respeitando a privacidade através de uma política de consentimento prévio (*opt-in*). A arquitetura basear-se-á em padrões abertos de observabilidade, adotando especificações como o **OpenTelemetry**, com o objetivo de fornecer uma base de dados sólida e escalável para subsidiar as pesquisas futuras em engenharia de software.

# 2. Objetivos

- *Seleção do padrão de coleta de dados*: Avaliar e definir o protocolo e o formato de transmissão mais adequados para a arquitetura, analisando alternativas como OpenTelemetry, Prometheus ou arquiteturas baseadas em eventos (ex: Fluentd).

- *Implementação do mecanismo de consentimento (opt-in)*: Desenvolver a interface de autorização no kw para que o usuário possa permitir ou revogar a coleta de métricas, garantindo transparência e respeito à privacidade.

- *Desenvolvimento do processo de upload no kw*: Implementar o módulo no lado do cliente (na ferramenta de linha de comando) responsável por empacotar e transmitir os dados de execução para a infraestrutura centralizada.

- *Desenvolvimento do servidor centralizado*: Construir a aplicação de backend que atuará como receptora dos dados de telemetria enviados pelos usuários ativos do kw.

- *Armazenamento seguro e anonimizado*: Estruturar a persistência de dados no servidor garantindo a segurança da informação e a remoção ou ofuscação de qualquer dado que possa identificar o usuário diretamente.

- *Desenvolvimento de um sistema de consultas*: Criar endpoints ou uma interface simplificada no servidor para que pesquisadores possam extrair, filtrar e analisar a base de dados coletada para estudos futuros.

# 3. Cronograma

As atividades planejadas estão divididas em três períodos (T1, T2 e T3), abrangendo o ciclo de 9 meses até dezembro.

| Atividade | T1 (Meses 1-3) | T2 (Meses 4-6) | T3 (Meses 7-9) |
| :--- | :---: | :---: | :---: |
| 1. Estudos exploratórios e definição dos requisitos | ● | | |
| 2. Desenvolvimento do servidor | ● | ● | ● |
| 3. Desenvolvimento do cliente (integração no `kw`) | | ● | ● |
| 4. Apresentações (Pitch e Defesa) | ● | | ● |
| 5. Preparação do relatório final | | | ● |

### Descrição das Etapas:

* **T1 (Abril – Junho):** Foco na fundamentação teórica, escolha do padrão (OpenTelemetry vs outros) e início da arquitetura do servidor. Inclui também a entrega/apresentação da proposta inicial ou qualificação.
* **T2 (Julho – Setembro):** Fase intensiva de codificação, onde o servidor começa a receber os primeiros dados e o desenvolvimento do módulo de *upload* no `kw` é iniciado.
* **T3 (Outubro – Dezembro):** Refinamento final das ferramentas, conclusão do sistema de consultas para pesquisadores, redação final da monografia e defesa do TCC.
