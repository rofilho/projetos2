---
title: "🟢 Aula S1: Abertura do Semestre 2026/2"
anatomia: nao
---

# 🟢 Aula S1: Abertura do Semestre — Regras, Avaliação e Formação de Grupos

**Disciplina:** Projetos Integrados 2 (Cód. VIA231)<br>
**Instituição:** Uniube, Uberlândia<br>
**Semana:** 1 | Quinta-feira, 13/08/2026<br>
**Professor:** Romualdo Mathias Filho<br>
**Local:** Laboratório de Informática, presencial<br>
**Tópicos:** Filosofia da disciplina (MVP e PBL) · Sistema de avaliação · Checkpoints · Live Coding e política anti-IA · GitHub · Formação de grupos

---

## 🎯 Objetivo da Aula

Ao final desta aula, os alunos serão capazes de:
- Explicar o que é um MVP e o que a disciplina cobra como entrega final
- Descrever como a nota é composta: os 5 checkpoints (N1) e a AMOSTRATEC (N2)
- Identificar os 5 critérios avaliados em cada checkpoint e o que precisa estar pronto em cada um
- Estar em um grupo definido, com repositório no GitHub criado e estrutura de pastas no lugar

---

## 📌 1. O que é esta disciplina

A disciplina é a construção de um **MVP — Produto Mínimo Viável**: a versão funcional mais simples
capaz de resolver um problema real. Não é um protótipo de tela, não é um slide: é software que roda.

**Metodologia:** Aprendizagem Baseada em Problemas (PBL). O grupo escolhe o problema, escolhe a
stack e defende as escolhas.

**Stack:** livre — React, Node.js, Python, Java, PostgreSQL ou outra. A única exigência é saber
argumentar por que escolheu.

**Requisito final:** o sistema precisa estar **hospedado publicamente, acessível por uma URL
válida** (Vercel, Render, Heroku, AWS ou equivalente).

> 💡 O que separa a nota alta da nota baixa aqui não é a stack. É o sistema abrir na URL, o fluxo
> funcionar de ponta a ponta e o grupo conseguir explicar o próprio código.

---

## 📌 2. Como a nota é composta

| Avaliação | Composição | Pontos |
| --- | --- | --- |
| **N1** | 5 checkpoints quinzenais presenciais, 10 pts cada | 50 |
| **N2** | AMOSTRATEC — defesa pública do MVP | 50 |
| **Total** | | **100** |

Aprovação: **≥ 60 pontos**.

### Composição de cada checkpoint (10 pts)

| Critério | Pontos | O que é avaliado |
| --- | --- | --- |
| 🎤 Apresentação | 2,5 | Demonstração funcional do marco da etapa |
| 🐙 GitHub | 2,5 | Histórico de commits + **comentário obrigatório a cada deploy** |
| 📝 Atas de reunião | 1,5 | Registro das reuniões do grupo, versionado em `/docs` |
| 💻 Tecnologia | 1,5 | Adequação e domínio das escolhas técnicas |
| 🔴 Live Code | 2,0 | Defesa técnica com modificação ao vivo no código |

### Rubrica de referência

| Nível | Proporção | Quando se aplica |
| --- | --- | --- |
| 🟢 Excelente | 100% do critério | Supera expectativas, sem falhas relevantes |
| 🟡 Bom | 80% | Atende bem, pequenas falhas |
| 🟠 Regular | 60% | Atende parcialmente, falhas evidentes |
| 🔴 Insuficiente | 0–40% | Não atende ou muito incompleto |

### AMOSTRATEC (N2 = 50 pts)

Defesa pública do MVP funcional hospedado, com histórico completo de commits, diante de banca
formada pelo professor titular e convidados avaliadores.

| Critério | Pontos |
| --- | --- |
| Funcionamento end-to-end | 25 |
| Hospedagem estável e escalável | 10 |
| Qualidade técnica e defesa oral | 15 |

---

## 🛑 3. Live Coding e a política anti-IA

Em todo checkpoint, **um integrante do grupo é sorteado** e precisa fazer uma modificação lógica
no sistema ao vivo — por exemplo, adicionar um filtro em uma query SQL ou criar um novo endpoint
na API.

**Proibido durante o live coding:** ChatGPT, Copilot ou qualquer IA generativa.
**Permitido:** editor de texto, navegador e documentação oficial.

O objetivo é validar domínio real do código e compreensão da arquitetura. Como o aluno é sorteado,
todo mundo precisa entender o sistema inteiro — não dá para dividir o projeto em ilhas.

---

## 📌 4. Gestão técnica: GitHub

Todo o projeto precisa ser rastreável pelo repositório oficial. Cada grupo terá um repositório
próprio dentro da organização da turma no GitHub:

https://github.com/uniube-pi2-2026-2

### Regras de versionamento

- Commits **semanais** obrigatórios
- Mensagens descritivas e objetivas
- Histórico precisa comprovar evolução e autoria de cada integrante
- **Todo deploy é acompanhado de um comentário** registrando o que foi para o ar

### Estrutura obrigatória do repositório

```
/src
  Código-fonte principal, frontend e backend

/database
  Scripts SQL
  Modelagem ER
  Seeds

/docs
  Diagramas UML
  Fluxos
  Especificação de API
  Atas de reuniões

README.md
  Nome dos integrantes
  Problema escolhido
  Stack utilizada
  Guia de setup e execução
```

> ⚠️ O histórico do GitHub é a prova de autoria. Um projeto que aparece pronto em um commit único
> na véspera do checkpoint perde os pontos de GitHub e compromete o live coding.

---

## 📌 5. Formação dos grupos (atividade da aula)

Os grupos são definidos **hoje**, ainda em sala. Cada grupo sai daqui com:

1. Integrantes definidos e registrados
2. Repositório criado na organização do GitHub, com todos os integrantes adicionados
3. As quatro pastas da estrutura obrigatória criadas
4. `README.md` inicial com os nomes dos integrantes

O **escopo do projeto** — problema escolhido, stack e Termo de Abertura — não é entrega de hoje.
Fica para a próxima aula, **27/08**, dando ao grupo o intervalo para discutir e decidir.

---

## 📅 6. Cronograma 2026/2

| Data | Semana | Atividade |
| --- | --- | --- |
| 13/08 | S1 | Abertura: regras, avaliação, formação de grupos, setup GitHub |
| 20/08 | — | **Sem aula** |
| 27/08 | S3 | Apresentação do Escopo |
| 03/09 | S4 | **CP-1** — Banco de Dados: script SQL + modelo ER + Termo de Abertura |
| 10/09 | S5 | Mentoria em laboratório |
| 17/09 | S6 | **CP-2** — Backend: API CRUD funcional |
| 24/09 | S7 | Mentoria em laboratório |
| 01/10 | S8 | **CP-3** — Integração: frontend consumindo a API |
| 08/10 | S9 | Mentoria em laboratório |
| 15/10 | S10 | **CP-4** — MVP com fluxo completo end-to-end |
| 22/10 | S11 | Mentoria em laboratório |
| 29/10 | S12 | **CP-5** — Hospedagem ativa e deploy na nuvem |
| 05/11 | S13 | Mentoria em laboratório |
| 12/11 | S14 | Mentoria em laboratório |
| 19/11 | S15 | Hardening, troubleshooting e ensaio da defesa |
| 26/11 | S16 | Mentoria em laboratório |
| 03/12 | S17 | Mentoria final e ajustes |
| 10/12 | S18 | **AMOSTRATEC** — apresentação final (N2) |

Todo checkpoint inclui live coding com aluno sorteado.

---

## 📋 Resumo Estrutural

| Conceito | Definição em uma frase |
| --- | --- |
| MVP | Versão funcional mais simples capaz de resolver um problema real |
| PBL | Metodologia em que o aprendizado parte de um problema, não de um conteúdo |
| Checkpoint | Avaliação presencial quinzenal de 10 pts sobre o marco da etapa |
| Live Coding | Modificação no código feita ao vivo por um integrante sorteado, sem IA |
| AMOSTRATEC | Defesa pública final do MVP hospedado, valendo os 50 pts da N2 |

---

## 📚 Referências

- **Plano de Ensino VIA231** — diretrizes oficiais da Uniube para Projetos Integrados 2
- **Ementa do Curso de Computação** — competências multidisciplinares do semestre
- **Padrões IEEE/ACM** — documentação de software e engenharia de requisitos

---


*Última atualização: 2026-08-13 | Status: rascunho*
