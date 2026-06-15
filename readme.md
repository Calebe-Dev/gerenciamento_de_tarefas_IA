# 🤖 Organização de Projetos com IA — sem API Key

Coleção de guias + template para organizar e executar tasks de desenvolvimento usando **GitHub Projects** e três agentes de IA rodando **localmente, via assinatura**: **Claude Code**, **Codex CLI** e **Antigravity**. Nenhum deles depende de `ANTHROPIC_API_KEY`, `OPENAI_API_KEY` ou billing por token — tudo roda dentro do plano que você já paga (ou do preview gratuito).

---

## 📋 Sumário

- [Visão geral](#-visão-geral)
- [Os três agentes](#-os-três-agentes)
- [Conheça o Woz](#-conheça-o-woz)
- [Estrutura do repositório](#-estrutura-do-repositório)
- [Pré-requisitos](#-pré-requisitos)
- [Setup rápido](#-setup-rápido)
- [Qual agente usar em cada task](#-qual-agente-usar-em-cada-task)
- [Boas práticas](#-boas-práticas)
- [Licença](#-licença)

---

## 🎯 Visão geral

Esse repositório serve dois propósitos:

1. **Base de conhecimento** — guias de como instalar, autenticar (sem API key) e usar cada agente no dia a dia ([`guias/`](guias)).
2. **Template reutilizável** — estrutura para copiar em qualquer projeto: issue templates, `CLAUDE.md`, mapa de arquitetura e memória persistente entre sessões ([`template/`](template)).

O fluxo proposto (detalhado em [`fluxo-de-trabalho/`](fluxo-de-trabalho)):

- Backlog organizado num **GitHub Project** (board)
- Issues padronizadas (Refactor, Bug, Test, Feature)
- Você abre, **localmente**, o agente mais adequado para a task
- O agente lê `CLAUDE.md` + `docs/AGENTS.md` + `docs/PROGRESS.md`, implementa, testa, abre PR e atualiza o board/memória — tudo via `gh` CLI
- Você revisa e dá merge (nunca o agente)

**Vantagem principal:** zero custo de API e zero secrets para gerenciar — a autenticação é a mesma assinatura/conta que você já usa para abrir cada ferramenta.

---

## 🧰 Os três agentes

| | [Claude Code](guias/claude-code) | [Codex CLI](guias/codex) | [Antigravity](guias/antigravity) |
|---|---|---|---|
| Tipo | CLI (terminal) | CLI (terminal) | IDE (fork do VS Code) + CLI `agy` |
| Empresa | Anthropic | OpenAI | Google |
| Modelo(s) | Claude Sonnet/Opus 4.6 | GPT (via ChatGPT) | Gemini 3 Pro/Flash, Claude Sonnet/Opus 4.6, GPT-OSS-120B |
| Plano necessário | Claude Pro / Max | ChatGPT Plus / Pro / Business / Edu / Enterprise | Conta Google (preview público gratuito) |
| Login sem API key | `claude` → `/login` | `codex login` ("Sign in with ChatGPT") | `agy` → login Google OAuth |
| Ponto forte | Raciocínio profundo, review crítico | Implementação padrão a partir de specs claras | Múltiplos agentes em paralelo + verificação visual via browser embutido |

Detalhes de instalação e uso em cada guia: [`guias/claude-code/`](guias/claude-code), [`guias/codex/`](guias/codex), [`guias/antigravity/`](guias/antigravity).

---

## 🧠 Conheça o Woz

Todo projeto precisa de alguém cuidando do backlog — decidindo o que entra,
quebrando trabalho grande em pedaços do tamanho de uma tarde, e mantendo as
issues claras o suficiente para qualquer agente pegar e rodar. Aqui esse
papel tem nome: **Woz**, em homenagem a **Steve Wozniak** — o engenheiro que
prefere abrir o cofre e entender a máquina a escrever um relatório de 40
páginas sobre ela.

Woz não é uma quarta ferramenta. É uma **persona de Product Owner** que
qualquer um dos três agentes veste quando o trabalho é organizar o backlog em
vez de implementar:

```
Aja como o Woz (docs/WOZ.md) e [tarefa de backlog]
```

Na prática, o Woz refina issues vagas, quebra Epics (`type:epic`) em
sub-issues usando o suporte nativo de **sub-issues** do GitHub
(`gh issue create --parent`), sinaliza tasks `scope:L` para serem quebradas e
mantém o board organizado. Detalhes e prompts de exemplo em
[`template/docs/WOZ.md`](template/docs/WOZ.md).

---

## 📁 Estrutura do repositório

```
.
├── readme.md                          # este arquivo — visão geral e índice
├── instalacao/
│   └── README.md                      # prompt único para aplicar o template num projeto
├── guias/
│   ├── claude-code/README.md          # instalação, login, comandos, prompts
│   ├── codex/README.md
│   └── antigravity/README.md
├── fluxo-de-trabalho/
│   └── README.md                      # setup do GitHub (board/labels/branch protection)
│                                       # + fluxo passo a passo + cheat-sheet `gh`
└── template/                          # copie/adapte para o seu projeto
    ├── CLAUDE.md
    ├── docs/
    │   ├── AGENTS.md
    │   ├── PROGRESS.md
    │   └── WOZ.md                     # persona do PO (Woz) + quebra de Epics
    └── .github/
        └── ISSUE_TEMPLATE/
            ├── epic.yml
            ├── refactor.yml
            ├── bug.yml
            ├── test.yml
            └── feature.yml
```

---

## ✅ Pré-requisitos

- Conta no GitHub + [`gh` CLI](https://cli.github.com) instalado e autenticado (`gh auth login`)
- Pelo menos **uma** das assinaturas: Claude Pro/Max, ChatGPT Plus/Pro, ou conta Google (Antigravity)
- Repositório onde você tem permissões de administrador (para configurar Project, labels e branch protection)

---

## 🚀 Setup rápido

1. **Instale e autentique pelo menos um agente** — siga o guia correspondente em [`guias/`](guias)
2. **Configure o GitHub** (Project board, labels, issue templates, branch protection) — siga [`fluxo-de-trabalho/README.md`](fluxo-de-trabalho/README.md#1-setup-inicial-do-repositório)
3. **Aplique o template no seu projeto** — duas opções:
   - **Atalho:** copie o prompt de [`instalacao/README.md`](instalacao/README.md) e cole no agente na raiz do projeto — ele clona, copia e preenche os arquivos sozinho
   - **Manual:** copie `template/CLAUDE.md` → raiz, `template/docs/*.md` → `docs/`, `template/.github/ISSUE_TEMPLATE/*.yml` → `.github/ISSUE_TEMPLATE/`, e preencha os placeholders você mesmo
4. **Teste:** crie uma issue, abra o agente escolhido na raiz do projeto e peça para implementar seguindo o `CLAUDE.md`

---

## 🧭 Qual agente usar em cada task

- **Claude Code** — bugs difíceis, refactors críticos, decisões de arquitetura, review de PRs (inclusive os abertos por outros agentes)
- **Codex** — features/bugs com critérios de aceite bem definidos, implementação direta
- **Antigravity** — mudanças com componente visual/UI (browser embutido testa sozinho), ou várias tasks pequenas em paralelo via Manager view

A label `agent:*` no issue template é só uma sugestão de triagem — você decide na hora qual abrir.

---

## 🌟 Boas práticas

### ✅ Faça

- Mantenha o `CLAUDE.md` curto e atualizado — é o primeiro arquivo lido em toda sessão
- Atualize o `docs/PROGRESS.md` ao final de cada task, incluindo qual agente executou
- Use labels de `scope:S/M/L` para quebrar tasks grandes
- Rode uma rodada do Woz periodicamente para manter o backlog organizado e os Epics quebrados em sub-issues (veja [`template/docs/WOZ.md`](template/docs/WOZ.md))
- Revise sempre os PRs gerados — você é o árbitro final, nunca o agente
- Use o GitHub Project para visualizar o pipeline (Backlog → Triage → Ready → In Progress → Review → Done)

### ❌ Evite

- Issues vagas tipo "melhorar performance" — sempre especifique escopo
- Misturar múltiplas mudanças numa issue só
- Dar permissão de merge direto pro agente
- Esquecer de atualizar o `docs/PROGRESS.md`

---

## 📄 Licença

Esse setup é template livre — adapte ao seu projeto e remova o que não usar.

---

**Feito com 🤖 por humanos que decidiram delegar o tédio para máquinas — sem pagar por token.**
