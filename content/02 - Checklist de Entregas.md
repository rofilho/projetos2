
# ✅ Checklist de Entregas — Projetos Integrados 2

> 📚 **Disciplina:** VIA231 — Projetos Integrados 2 👨‍🏫 **Professor:** Romualdo Mathias Filho 🏫 **Semestre:** 2026/2 — Uniube
> 

---

# **📌 Como funciona a avaliação?**

Cada Checkpoint (CP) vale **10 pontos**, distribuídos em 5 critérios:

| **Critério** | **O que é avaliado** | **Peso** |
| --- | --- | --- |
| 🎤 Apresentação | Demonstração funcional do marco, clareza, participação de todos | 2,5 |
| 🐙 GitHub | Commits frequentes, mensagens claras, branches, estrutura, comentário a cada deploy | 2,5 |
| 📝 Atas de reunião | Registro das reuniões versionado em `/docs` | 1,5 |
| 💻 Tecnologia | Adequação e domínio das escolhas técnicas | 1,5 |
| 🔴 Live Code | Um aluno sorteado na hora modifica o código ao vivo | 2,0 |

> ⚠️ **ATENÇÃO:** Todo membro do grupo deve estar preparado para o Live Code! O aluno será sorteado aleatoriamente e deverá demonstrar domínio real do código. Não saber explicar ou modificar o código = nota baixa para o grupo inteiro.
> 

## **🛑 Protocolo de Live Coding, Política Anti-IA**

Proibido durante o Live Code: ChatGPT, Copilot ou qualquer IA generativa.
Permitido: editor de texto, navegador e documentação oficial.

---

# **🗓️ Cronograma dos Checkpoints**

| **CP** | **Semana** | **Data** | **Marco** |
| --- | --- | --- | --- |
| CP-1 | S4 | 03/09 | 🗄️ Banco de Dados |
| CP-2 | S6 | 17/09 | ⚙️ Backend — API CRUD |
| CP-3 | S8 | 01/10 | 🔗 Integração Frontend + API |
| CP-4 | S10 | 15/10 | 🚀 MVP Completo |
| CP-5 | S12 | 29/10 | 🌐 Hospedagem + README Final |

> Cronograma completo do semestre, com mentorias e AMOSTRATEC: [[01 - Informacoes da Turma]]
> 

---

# **📂 Documentação de Gestão Obrigatória**

Além do código, cada grupo **deve manter documentação de gestão** no repositório GitHub, na pasta `/docs`.

**Organização GitHub da turma:** https://github.com/uniube-pi2-2026-2

## **Estrutura esperada no repositório**

```

docs/
├── atas/
│   ├── ata_S03_2026-08-27.md
│   ├── ata_S04_2026-09-03.md
│   ├── ata_S05_2026-09-10.md
│   └── ...
├── termo_abertura.md
├── cronograma.md
└── relatorio_final.md
```

## **📝 Ata de Reunião**

Registro formal das decisões de cada reunião do grupo.

**Mínimo de 12 atas ao longo do semestre** (≈ 1 por semana).

## **Template — Ata de Reunião**

```

# Ata de Reunião — Semana SXX

Data: DD/MM/AAAA
Horário: HH:MM – HH:MM
Local/Plataforma: Presencial / Discord / Meet
Grupo: [Nome do Grupo]

## Participantes
| Nome | Presente |
|------|----------|
| Nome | ✅ / ❌  |
| Nome | ✅ / ❌  |

## Pauta
1. [Tópico 1]
2. [Tópico 2]

## Decisões Tomadas
- Decisão 1
- Decisão 2

## Tarefas Definidas
| Tarefa | Responsável | Prazo |
|--------|------------|-------|
| Tarefa X | Nome | DD/MM |
| Tarefa Y | Nome | DD/MM |

## Pendências Anteriores
- [x] Tarefa concluída
- [ ] Tarefa pendente

## Observações
Anotações relevantes.
```

## **📋 Documentos obrigatórios**

| **Documento** | **Quando** | **Descrição** |
| --- | --- | --- |
| 📄 Termo de Abertura | CP-1 | Nome, problema, objetivo, escopo, stack, membros |
| 📅 Cronograma | CP-1 → atualizar a cada CP | Planejamento de entregas por semana |
| 📊 Board/Kanban | CP-1 em diante | Issues no GitHub com labels e responsáveis |
| 📝 Atas de Reunião | A cada reunião | Mínimo 12 no semestre |
| 📑 Relatório Final | CP-5 | 1 página: planejado vs. entregue + lições |

> 💡 **Dica:** Usem o GitHub Projects! Criem Issues para cada tarefa, atribuam responsáveis, usem labels (`feature`, `bug`, `docs`, `database`) e movam entre colunas (To Do → Doing → Done). Isso conta no critério GitHub!
> 

---

# **🗄️ CP-1 — Banco de Dados**

> 📅 **Semana 4 · 03/09/2026** 🎯 **Objetivo:** Script SQL funcional + Modelo ER documentado
> 

## **💻 Entregáveis técnicos**

- Modelo Entidade-Relacionamento (ER) — diagrama visual (dbdiagram.io, draw.io, etc.)
- Script SQL de criação — DDL com tabelas, PKs e FKs
- Script SQL de dados iniciais — INSERTs com dados de exemplo
- Banco rodando — demonstrar banco ativo (local ou Docker)
- Documentação no README — explicar tabelas e relacionamentos

## **📝 Documentos de gestão**

- Termo de Abertura do Projeto na pasta `/docs`
- Cronograma do projeto (CP-1 a CP-5)
- Mínimo 2 atas de reunião (semanas S3 e S4)
- Board/Kanban configurado no GitHub (Issues + labels + responsáveis)

## **🐙 GitHub**

- Repositório criado e organizado (pastas `/database`, `/docs`, etc.)
- Commits descritivos — mínimo 1 por membro
- Todos os membros como colaboradores no repositório

## **🎤 Apresentação**

- Explicar o problema que o sistema resolve
- Mostrar o diagrama ER e justificar as tabelas
- Demonstrar o banco funcionando com queries de teste

---

# **⚙️ CP-2 — Backend / API CRUD**

> 📅 **Semana 6 · 17/09/2026** 🎯 **Objetivo:** API CRUD funcional conectada ao banco do CP-1
> 

## **💻 Entregáveis técnicos**

- Pelo menos 2 endpoints CRUD (Create, Read, Update, Delete)
- Conexão ao banco de dados do CP-1 funcionando
- Testes manuais demonstrados (Postman, Insomnia, Thunder Client)
- Tratamento básico de erros (status codes: 200, 201, 400, 404, 500)
- Documentação da API — rotas, métodos, parâmetros, respostas

## **📝 Documentos de gestão**

- Mínimo 2 atas novas (semanas S5 e S6)
- Board/Kanban atualizado — tarefas do CP-2 movidas para "Done"
- Cronograma atualizado — marcar concluído, ajustar pendente

## **🐙 GitHub**

- Código da API em pasta organizada (`/backend` ou `/api`)
- Commits semanais com mensagens descritivas
- `.gitignore` configurado (não commitar `node_modules`, `.env`)
- README atualizado com instruções de como rodar

## **🎤 Apresentação**

- Explicar a arquitetura da API (rotas, controllers, models)
- Demonstrar cada endpoint ao vivo (Postman ou similar)
- Mostrar integração com o banco de dados

---

# **🔗 CP-3 — Integração Frontend + API**

> 📅 **Semana 8 · 01/10/2026** 🎯 **Objetivo:** Frontend consumindo a API com operações funcionais
> 

## **💻 Entregáveis técnicos**

- Telas do frontend criadas — pelo menos as telas principais
- Consumo da API — requisições HTTP (fetch, axios, etc.)
- CRUD via interface — criar, visualizar, editar, deletar pela tela
- Feedback visual — mensagens de sucesso/erro para o usuário
- Navegação funcional — menu, rotas ou links entre telas

## **📝 Documentos de gestão**

- Mínimo 2 atas novas (semanas S7 e S8)
- Board/Kanban atualizado — tarefas do CP-3 organizadas
- Cronograma atualizado — progresso até metade do semestre
- Retrospectiva breve — o que deu certo, o que melhorar (pode ser na ata)

## **🐙 GitHub**

- Código do frontend no repositório (`/frontend` ou `/client`)
- Commits de todos os membros — distribuição equilibrada
- Issues ou Projects usados para organizar tarefas

## **🎤 Apresentação**

- Demonstrar fluxo completo: frontend → API → banco → tela
- Mostrar pelo menos 1 operação de cada tipo do CRUD
- Explicar a stack frontend escolhida e justificar

---

# **🚀 CP-4 — MVP Completo**

> 📅 **Semana 10 · 15/10/2026** 🎯 **Objetivo:** Fluxo completo end-to-end funcionando
> 

## **💻 Entregáveis técnicos**

- Fluxo principal funcionando — tarefa central do sistema OK
- Validações implementadas — campos obrigatórios, formatos, regras de negócio
- Autenticação/Autorização (se aplicável) — login, sessão, controle de acesso
- Interface polida — responsiva, sem telas quebradas
- Integração completa — Frontend + Backend + Banco 100% conectados

## **📝 Documentos de gestão**

- Mínimo 2 atas novas (semanas S9 e S10)
- Board/Kanban atualizado — tarefas finais antes do MVP
- Cronograma atualizado — status antes da reta final
- Lista de bugs/pendências — Issues abertas no GitHub

## **🐙 GitHub**

- Histórico de commits consistente ao longo das semanas
- Uso de branches para features (`feature/login`, `feature/dashboard`)
- Pull requests documentados (diferencial)
- README completo: descrição, stack, como instalar e rodar

## **🎤 Apresentação**

- Demo do fluxo completo (cadastro → login → operação → resultado)
- Explicar decisões técnicas e desafios enfrentados
- Mostrar evolução do projeto desde o CP-1

---

# **🌐 CP-5 — Hospedagem + README Final**

> 📅 **Semana 12 · 29/10/2026** 🎯 **Objetivo:** Sistema hospedado online + documentação final
> 

## **💻 Entregáveis técnicos**

- Sistema hospedado e acessível via URL (Vercel, Render, Railway, etc.)
- Banco de dados em produção (Supabase, PlanetScale, ElephantSQL, etc.)
- URL funcionando — link direto para acessar o sistema
- README final completo contendo:
    - Nome do projeto e descrição
    - Problema que resolve
    - Stack utilizada
    - Instruções de instalação e execução local
    - Link para o sistema hospedado
    - Screenshots ou GIFs demonstrativos
    - Membros da equipe
- Código limpo — sem console.log de debug, sem comentários desnecessários

## **📝 Documentos de gestão**

- Mínimo 2 atas novas (semanas S11 e S12)
- Todas as atas do semestre compiladas em `/docs/atas`
- Board/Kanban finalizado — tudo em "Done" ou documentado como pendência
- Relatório final — 1 página: planejado vs. entregue + lições aprendidas
- Cronograma final — versão final: cumprido vs. planejado

## **🐙 GitHub**

- Repositório público ou compartilhado com o professor
- Tags ou releases marcando versões importantes
- Histórico limpo e organizado
- Pasta `/docs` completa com toda documentação do semestre

## **🎤 Apresentação**

- Acessar o sistema pela URL ao vivo
- Demo completa do sistema em produção
- Apresentar o README como documentação oficial

---

# **🏆 AMOSTRATEC — Avaliação Final (N2)**

> 🎓 A AMOSTRATEC é a avaliação final do semestre, separada dos checkpoints. **Data: 10/12/2026 (S18).**
> 

| **Critério** | **Peso** |
| --- | --- |
| 🟢 Funcionamento completo do sistema | 25 pts |
| 🌐 Hospedagem ativa e acessível | 10 pts |
| 🛡️ Qualidade do código + Defesa técnica | 15 pts |
| **Total** | **50 pts** |

## **O que precisa estar pronto**

- Sistema 100% funcional e hospedado
- Todos os membros preparados para defesa técnica
- Apresentação final organizada e profissional
- README completo e atualizado no GitHub

---

# **📊 Cálculo da Nota Final**

| **Componente** | **Valor** |
| --- | --- |
| N1 — Soma dos 5 Checkpoints | 5 × 10 = **50 pts** |
| N2 — AMOSTRATEC | **50 pts** |
| **NOTA FINAL** | **N1 + N2 = 100 pts** |

> ✅ **Aprovação:** ≥ 60 pontos
> 

---

# **⚡ Dicas para se dar bem**

> 🔥 **1. Commitem com frequência!** Commits semanais são avaliados. Não façam tudo na última hora.
> 

> 📝 **2. Mensagens de commit descritivas** ❌ `update` — ✅ `feat: adiciona endpoint de criação de usuário`
> 

> 👥 **3. Todos participam** Cada membro deve ter commits. Grupos com "carregadores" serão penalizados no critério GitHub.
> 

> 🎯 **4. Live Code — TODOS estudam tudo** Qualquer membro pode ser sorteado. Estudem o código do grupo inteiro, não só a "sua parte".
> 

> 📖 **5. Documentem o projeto** O README é a vitrine. Deve explicar tudo para alguém de fora entender e rodar.
> 

> 🌿 **6. Usem branches** Trabalhar direto na `main` é arriscado. Usem branches e façam merge.
> 

> 🌐 **7. Hospedagem desde cedo** Não deixem para hospedar só no CP-5. Quanto antes, menos problemas.
> 

> 📋 **8. Façam atas de reunião!** Ata não é burocracia — é registro de decisões, e vale 1,5 ponto em cada CP.
> 

> 📊 **9. Usem o GitHub Projects** Issues com labels (`bug`, `feature`, `docs`) + Kanban board = organização visível e nota no critério GitHub.
> 

> 📁 **10. Pasta `/docs` organizada** Atas, diagramas, cronogramas — tudo indexado. Bagunça = nota menor em Apresentação.
> 

---

> 📅 Atualizado em: 27/08/2026 📌 Projetos Integrados 2 (VIA231) — Uniube 2026/2
>
