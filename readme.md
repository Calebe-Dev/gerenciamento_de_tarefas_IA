# 🤖 AI-Powered Task Management

Sistema de gerenciamento de tarefas de desenvolvimento usando **GitHub Projects** + **Claude Code GitHub Actions** com suporte a múltiplos agentes de IA. Funciona em qualquer projeto, qualquer linguagem.

---

## 📋 Sumário

- [Visão geral](#-visão-geral)
- [Pré-requisitos](#-pré-requisitos)
- [O que precisa estar ativo no GitHub](#-o-que-precisa-estar-ativo-no-github)
- [Setup passo a passo](#-setup-passo-a-passo)
- [Estrutura do repositório](#-estrutura-do-repositório)
- [Padrões de tarefas](#-padrões-de-tarefas)
- [Fluxo de trabalho](#-fluxo-de-trabalho)
- [Arquivo `CLAUDE.md` (template)](#-arquivo-claudemd-template)
- [Workflows do GitHub Actions](#-workflows-do-github-actions)
- [Como usar no dia a dia](#-como-usar-no-dia-a-dia)
- [Boas práticas](#-boas-práticas)
- [Troubleshooting](#-troubleshooting)

---

## 🎯 Visão geral

Esse sistema permite que você:

- Mantenha um **backlog estruturado** no GitHub Projects
- Use **templates de Issue** padronizados (Refactor, Bug, Test, Feature)
- Acione **Claude** com `@claude` em qualquer Issue para que ele implemente, teste e abra PR
- Use **revisão automática** em todo PR
- Mantenha **memória persistente** entre sessões via `PROGRESS.md`
- Use **múltiplos modelos** (Claude para raciocínio, Gemini para contexto longo) de forma assimétrica

**Vantagem principal:** zero infraestrutura externa. Tudo vive no GitHub.

---

## ✅ Pré-requisitos

- Conta no GitHub (qualquer plano serve)
- Plano **Claude Pro** ou **Claude Max** (recomendado) **OU** API key da Anthropic com créditos
- Repositório onde você tem permissões de administrador
- Node.js 18+ instalado localmente (apenas se quiser usar o Claude Code no terminal também)

---

## 🔧 O que precisa estar ativo no GitHub

### 1. Recursos do repositório

Em **Settings → General**, ative:

- ✅ **Issues** (obrigatório)
- ✅ **Projects** (para o board de tasks)
- ✅ **Discussions** (opcional, mas útil para decisões arquiteturais)
- ✅ **Actions → Allow all actions and reusable workflows**

### 2. GitHub App do Claude

Instale o app oficial:

```bash
# Forma mais simples — pelo terminal do Claude Code
claude
> /install-github-app
```

Esse comando guia você pelo setup automático: cria o GitHub App, configura permissões e adiciona o secret.

**Setup manual** (se preferir):

1. Instale o app em: <https://github.com/apps/claude>
2. Selecione o repositório alvo
3. Aceite as permissões solicitadas

### 3. Secrets do repositório

Em **Settings → Secrets and variables → Actions**, crie:

| Secret | Descrição | Obrigatório? |
|---|---|---|
| `ANTHROPIC_API_KEY` | Sua API key da Anthropic | Sim (se não for Claude Max) |
| `CLAUDE_CODE_OAUTH_TOKEN` | Token OAuth (Claude Pro/Max) | Alternativa à API key |
| `GEMINI_API_KEY` | Para tasks de contexto longo | Opcional |

> 💡 **Claude Max users:** rode `claude setup-token` localmente para gerar o `CLAUDE_CODE_OAUTH_TOKEN` e use ele ao invés da API key — assim usa sua subscription ao invés de pagar por uso.

### 4. GitHub Project (o board)

1. Vá em **Projects** → **New project** → **Board**
2. Crie as colunas: `📥 Backlog` · `🔍 Triage` · `📋 Ready` · `🏗️ In Progress` · `👀 Review` · `✅ Done`
3. Configure **automation**: quando uma issue for fechada → mover para `Done`. Quando um PR for aberto referenciando a issue → mover para `Review`.

### 5. Labels padronizadas

Crie estas labels (Settings → Labels):

| Label | Cor | Uso |
|---|---|---|
| `type:refactor` | `#0e8a16` | Refatoração |
| `type:bug` | `#d73a4a` | Correção de bug |
| `type:test` | `#fbca04` | Adição de testes |
| `type:feature` | `#1d76db` | Nova funcionalidade |
| `type:docs` | `#5319e7` | Documentação |
| `scope:S` | `#c5def5` | Pequena (≤2h) |
| `scope:M` | `#bfdadc` | Média (meio dia) |
| `scope:L` | `#bfd4f2` | Grande (+ de um dia) |
| `agent:claude` | `#7c3aed` | Para o Claude executar |
| `agent:gemini` | `#22d3ee` | Para o Gemini (contexto longo) |
| `needs-spec` | `#e99695` | Precisa de spec antes de executar |
| `blocked` | `#000000` | Bloqueada por outra task |

### 6. Branch protection (recomendado)

Em **Settings → Branches → Add rule** para a branch `main`:

- ✅ Require pull request before merging
- ✅ Require status checks (testes do CI)
- ✅ Require conversation resolution
- ✅ Do not allow bypassing the above settings

Assim o Claude **nunca** dá merge direto na main — sempre passa por review humano.

---

## 🚀 Setup passo a passo

### Passo 1 — Estrutura inicial

Na raiz do repositório, crie:

```
.
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── refactor.yml
│   │   ├── bug.yml
│   │   ├── test.yml
│   │   └── feature.yml
│   └── workflows/
│       ├── claude-mention.yml
│       └── claude-review.yml
├── docs/
│   ├── AGENTS.md
│   └── PROGRESS.md
└── CLAUDE.md
```

### Passo 2 — Adicione o `CLAUDE.md`

Esse é o "briefing" que o Claude lê em toda execução. Veja o [template completo abaixo](#-arquivo-claudemd-template).

### Passo 3 — Adicione os templates de Issue

Veja a seção [Padrões de tarefas](#-padrões-de-tarefas).

### Passo 4 — Adicione os workflows

Veja a seção [Workflows do GitHub Actions](#-workflows-do-github-actions).

### Passo 5 — Teste

1. Abra uma Issue usando o template `feature`
2. Comente: `@claude implementa essa feature seguindo o CLAUDE.md`
3. Acompanhe o Claude executar e abrir um PR

---

## 📁 Estrutura do repositório

```
seu-projeto/
├── .github/
│   ├── ISSUE_TEMPLATE/        # Templates padronizados de Issue
│   └── workflows/             # Automações do Claude
├── docs/
│   ├── AGENTS.md              # Mapa de arquitetura para os agentes
│   ├── PROGRESS.md            # Memória persistente entre sessões
│   └── specs/                 # Specs detalhadas (opcional)
│       ├── pending/
│       └── done/
├── CLAUDE.md                  # Instruções principais do Claude
└── src/                       # Seu código
```

---

## 📝 Padrões de tarefas

### Por que padronizar?

Issues sem padrão = agente com contexto ambíguo = código ruim. Cada template abaixo força o **mínimo de informação necessária** para o agente executar sem chutar.

### Template 1: Refactor (`.github/ISSUE_TEMPLATE/refactor.yml`)

```yaml
name: 🔧 Refactor
description: Refatoração de código existente
title: "[REFACTOR] "
labels: ["type:refactor", "needs-spec"]
body:
  - type: textarea
    id: motivation
    attributes:
      label: Motivação
      description: Por que essa refatoração é necessária?
      placeholder: Ex. O módulo X está acoplado ao Y, dificultando testes...
    validations:
      required: true

  - type: textarea
    id: scope
    attributes:
      label: Escopo
      description: Arquivos e módulos envolvidos
      placeholder: |
        - src/services/auth.service.ts
        - src/controllers/auth.controller.ts
    validations:
      required: true

  - type: textarea
    id: dont-touch
    attributes:
      label: O que NÃO tocar
      description: Crítico em microserviços em produção
      placeholder: |
        - Não alterar a API pública do AuthService
        - Não modificar a tabela `users`
    validations:
      required: true

  - type: textarea
    id: acceptance
    attributes:
      label: Critérios de aceite
      placeholder: |
        - [ ] Testes existentes continuam passando
        - [ ] Cobertura mínima de 80%
        - [ ] Sem mudanças na API pública
    validations:
      required: true

  - type: dropdown
    id: size
    attributes:
      label: Escopo estimado
      options:
        - S (≤2h)
        - M (meio dia)
        - L (+ de um dia — considere quebrar)
    validations:
      required: true
```

### Template 2: Bug (`.github/ISSUE_TEMPLATE/bug.yml`)

```yaml
name: 🐛 Bug
description: Correção de bug
title: "[BUG] "
labels: ["type:bug"]
body:
  - type: textarea
    id: description
    attributes:
      label: Descrição do bug
      placeholder: O que está acontecendo?
    validations:
      required: true

  - type: textarea
    id: steps
    attributes:
      label: Passos para reproduzir
      placeholder: |
        1. Acesse...
        2. Clique em...
        3. Veja o erro
    validations:
      required: true

  - type: textarea
    id: expected
    attributes:
      label: Comportamento esperado
    validations:
      required: true

  - type: textarea
    id: actual
    attributes:
      label: Comportamento atual
    validations:
      required: true

  - type: textarea
    id: context
    attributes:
      label: Contexto técnico
      placeholder: |
        - Logs relevantes
        - Stack trace
        - Arquivos suspeitos
    validations:
      required: false
```

### Template 3: Test (`.github/ISSUE_TEMPLATE/test.yml`)

```yaml
name: 🧪 Test
description: Adicionar/melhorar testes
title: "[TEST] "
labels: ["type:test"]
body:
  - type: textarea
    id: target
    attributes:
      label: Módulo/arquivo alvo
      placeholder: src/services/lead.service.ts
    validations:
      required: true

  - type: textarea
    id: cases
    attributes:
      label: Casos a cobrir
      placeholder: |
        - Caminho feliz: criar lead com dados válidos
        - Erro: dados inválidos → retorna 400
        - Edge case: lead duplicado → retorna 409
    validations:
      required: true

  - type: dropdown
    id: type
    attributes:
      label: Tipo de teste
      options:
        - Unit
        - Integration
        - E2E
    validations:
      required: true

  - type: input
    id: coverage
    attributes:
      label: Cobertura mínima esperada
      placeholder: "80%"
    validations:
      required: false
```

### Template 4: Feature (`.github/ISSUE_TEMPLATE/feature.yml`)

```yaml
name: ✨ Feature
description: Nova funcionalidade
title: "[FEATURE] "
labels: ["type:feature", "needs-spec"]
body:
  - type: textarea
    id: user-story
    attributes:
      label: User story
      placeholder: Como [tipo de usuário], quero [ação], para [benefício]
    validations:
      required: true

  - type: textarea
    id: acceptance
    attributes:
      label: Critérios de aceite
      placeholder: |
        - [ ] Endpoint POST /leads cria um lead
        - [ ] Retorna 201 com o lead criado
        - [ ] Valida campos obrigatórios
    validations:
      required: true

  - type: textarea
    id: dependencies
    attributes:
      label: Dependências
      placeholder: |
        - Issue #42 (precisa estar concluída antes)
        - Lib externa: nodemailer
    validations:
      required: false

  - type: textarea
    id: technical-notes
    attributes:
      label: Notas técnicas
      placeholder: |
        - Usar o padrão Repository já existente
        - Reaproveitar UserValidator
    validations:
      required: false
```

---

## 🔄 Fluxo de trabalho

```
┌──────────────┐
│ 1. Você cria │  Issue no GitHub Projects usando template
│   a Issue    │  → coluna "Backlog"
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 2. Triage    │  Você refina, adiciona labels (scope, agent)
│  (você)      │  → coluna "Triage" → "Ready"
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 3. Comenta   │  "@claude implementa essa task seguindo CLAUDE.md"
│   @claude    │  → coluna "In Progress" (manual ou automation)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 4. Claude    │  Lê a Issue + CLAUDE.md + AGENTS.md + PROGRESS.md
│  executa     │  Cria branch → implementa → roda testes → abre PR
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 5. Review    │  Workflow automático faz review do PR
│   automático │  → coluna "Review"
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 6. Você      │  Aprova e dá merge → Issue fecha automaticamente
│   aprova     │  → coluna "Done"
└──────────────┘
```

---

## 📖 Arquivo `CLAUDE.md` (template)

Salve esse arquivo na raiz do seu repositório:

```markdown
# CLAUDE.md

Instruções principais para o Claude ao trabalhar nesse repositório.

## Sobre o projeto

[Descreva em 2-3 linhas o que é o projeto, stack principal, e produção/desenvolvimento]

## Arquitetura

- **Backend:** [linguagem/framework]
- **Frontend:** [linguagem/framework]
- **Banco:** [tipo]
- **Infra:** [Docker/K8s/etc]
- **Detalhes:** veja `docs/AGENTS.md`

## Regras críticas

1. **NUNCA** dê merge direto na `main` — sempre via PR
2. **NUNCA** altere arquivos em `[lista de paths protegidos]` sem confirmação
3. **NUNCA** commite secrets, API keys, ou variáveis de ambiente
4. **SEMPRE** rode os testes antes de marcar a task como pronta
5. **SEMPRE** atualize o `docs/PROGRESS.md` ao concluir uma task
6. **SEMPRE** referencie a Issue no commit: `feat: adicionar X (#42)`

## Convenções de código

- **Commits:** Conventional Commits (`feat:`, `fix:`, `refactor:`, `test:`, `docs:`)
- **Branches:** `<tipo>/<issue-id>-<descrição>` ex: `feat/42-add-lead-endpoint`
- **Estilo:** seguir o linter configurado (`.eslintrc` / `.prettierrc` / `pyproject.toml`)
- **Testes:** [framework usado, ex: Vitest/Jest/PyTest]

## Antes de executar qualquer task

1. Leia `docs/AGENTS.md` para entender a arquitetura
2. Leia `docs/PROGRESS.md` para saber o estado atual
3. Leia os critérios de aceite da Issue
4. Se algo estiver ambíguo, **comente na Issue pedindo esclarecimento** ao invés de chutar

## Ao concluir uma task

1. Garanta que os testes passam (`npm test` / `pytest` / etc)
2. Atualize `docs/PROGRESS.md` com:
   - Issue resolvida
   - Mudanças principais
   - Decisões tomadas
3. Abra um PR referenciando a Issue
4. No corpo do PR, inclua:
   - Resumo das mudanças
   - Como testar
   - Checklist dos critérios de aceite

## Quando usar outro modelo

Se a task envolve:
- **Refactor de muitos arquivos acoplados** → considere delegar para Gemini (`@gemini` se configurado)
- **Análise de logs longos** → Gemini
- **Raciocínio crítico, code review** → fique com Claude

## Permissões

- ✅ Pode: ler/escrever código, rodar testes, abrir PRs, comentar Issues
- ❌ Não pode: dar merge, deletar branches da main, alterar workflows do CI, commitar secrets
```

### Arquivo `docs/AGENTS.md`

```markdown
# AGENTS.md — Mapa de arquitetura

Documento lido por todos os agentes de IA antes de executar tasks.

## Estrutura de pastas

```
src/
├── modules/          # Módulos de negócio
│   ├── auth/         # Autenticação — não tocar sem spec
│   ├── leads/        # Gestão de leads
│   ├── users/        # Gestão de usuários
│   └── notifications/ # Notificações
├── shared/           # Código compartilhado entre módulos
└── infrastructure/   # Configs, db, externos
```

## Regras por módulo

| Módulo | Owner | Regras |
|---|---|---|
| auth | core | Não alterar sem spec aprovada |
| leads | dev team | Pode refatorar livre |
| users | dev team | Pode refatorar livre |
| notifications | core | API pública estável — não quebrar |

## Stack

- Linguagem: [...]
- Framework: [...]
- ORM: [...]
- Testes: [...]
- CI/CD: GitHub Actions

## Padrões usados

- Repository Pattern em todos os módulos
- DI via [framework]
- Errors centralizados em `src/shared/errors/`

## Variáveis de ambiente críticas

Listadas em `.env.example` — nunca commitar `.env`.
```

### Arquivo `docs/PROGRESS.md`

```markdown
# PROGRESS.md — Histórico de tasks executadas

Memória persistente entre sessões de agentes. Cada task concluída adiciona uma entrada.

---

## 2026-06-15

### #42: Adicionar endpoint POST /leads — DONE

- Criado `LeadsController.create()` com validação
- Adicionados 5 testes unitários + 2 de integração
- Cobertura do módulo `leads`: 87%
- Decisão: usar `class-validator` ao invés de Zod (consistência com o resto do projeto)
- PR: #45

---

## 2026-06-14

### #40: Refatorar AuthService — DONE

- Separado em AuthService + TokenService
- API pública mantida
- 12 testes adicionados
- PR: #41
```

---

## ⚙️ Workflows do GitHub Actions

### Workflow 1 — Responder a menções `@claude`

Salve como `.github/workflows/claude-mention.yml`:

```yaml
name: Claude — Mention Handler

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned]

jobs:
  claude:
    if: contains(github.event.comment.body, '@claude') || contains(github.event.issue.body, '@claude')
    runs-on: ubuntu-latest
    permissions:
      contents: write
      issues: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Use OAuth token alternativo se for Claude Max:
          # claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
          claude_args: |
            --model claude-sonnet-4-6
            --max-turns 15
            --append-system-prompt "Sempre leia CLAUDE.md, docs/AGENTS.md e docs/PROGRESS.md antes de qualquer ação."
```

### Workflow 2 — Code review automático em PRs

Salve como `.github/workflows/claude-review.yml`:

```yaml
name: Claude — Auto Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Faça uma revisão de código deste PR seguindo as diretrizes do CLAUDE.md.

            Verifique:
            1. Os critérios de aceite da Issue referenciada foram cumpridos?
            2. Há regressões potenciais?
            3. A cobertura de testes é adequada?
            4. Há violações dos padrões em docs/AGENTS.md?
            5. Há código duplicado ou acoplamento desnecessário?

            Comente apenas pontos relevantes. Se está tudo bem, aprove.
          claude_args: |
            --model claude-sonnet-4-6
            --max-turns 5
```

### Workflow 3 — Triagem automática de Issues (opcional)

Salve como `.github/workflows/claude-triage.yml`:

```yaml
name: Claude — Auto Triage

on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      issues: write
    steps:
      - uses: actions/checkout@v4

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Esta é uma nova Issue. Analise e:
            1. Sugira labels apropriadas (type:*, scope:*)
            2. Identifique se está ambígua e precisa de mais detalhes
            3. Sugira issues relacionadas se houver
            4. Comente um resumo da análise

            Use as labels disponíveis no repositório.
          claude_args: |
            --model claude-haiku-4-5
            --max-turns 3
```

> 💡 **Por que Haiku no triage?** É barato, rápido, e o trabalho é classificação — não raciocínio profundo.

---

## 💻 Como usar no dia a dia

### Criando uma nova task

1. Vá em **Issues → New issue**
2. Escolha o template apropriado (Refactor / Bug / Test / Feature)
3. Preencha todos os campos obrigatórios
4. Salve — a Issue cai na coluna `Backlog` do Project

### Executando uma task

**Opção A — Interativa (`@claude`)**

Comente na Issue:

```
@claude implementa essa task seguindo o CLAUDE.md e o template.
```

O Claude vai:
1. Ler `CLAUDE.md`, `docs/AGENTS.md`, `docs/PROGRESS.md`
2. Criar uma branch (`feat/42-...`)
3. Implementar a task
4. Rodar os testes locais
5. Abrir um PR referenciando a Issue
6. Atualizar `PROGRESS.md`

**Opção B — Direta via assignment**

Atribua a Issue ao `@claude` (após instalar o app) — ele executa automaticamente.

### Pedindo revisão de PR

Comente no PR:

```
@claude revisa esse PR com olhar crítico, focando em [aspecto específico].
```

### Refinando um backlog grande de uma vez

Comente em uma Issue de meta:

```
@claude leia as Issues #40 a #55 e sugira refinamentos de descrição, dependências e priorização.
```

---

## 🌟 Boas práticas

### ✅ Faça

- **Mantenha o `CLAUDE.md` curto e atualizado** — é o primeiro arquivo lido sempre
- **Atualize o `PROGRESS.md` ao final de cada task** — vira o "diário do projeto"
- **Use labels de scope** (`scope:S/M/L`) — ajuda a quebrar tasks grandes
- **Quebre tasks `L` em tasks `M` ou menores** — agente performa melhor em escopo limitado
- **Revise sempre PRs gerados** — você é o árbitro final, não o agente
- **Comente esclarecimentos** em vez de reescrever a Issue toda
- **Use o Project board** para visualizar o pipeline

### ❌ Evite

- Issues vagas tipo "melhorar performance" — sempre especifique escopo
- Misturar múltiplas mudanças em uma Issue só
- Dar permissão de merge direto pro Claude
- Ignorar warnings do agente (se ele pergunta, responda antes de prosseguir)
- Commitar `.env` ou secrets no `CLAUDE.md`
- Esquecer de atualizar o `PROGRESS.md`

### 💰 Otimização de tokens

- Use **Haiku** para triagem, classificação e tasks simples
- Use **Sonnet** para implementação padrão
- Use **Opus** apenas para refatorações complexas multi-arquivo
- Configure `--max-turns` baixo em workflows automáticos (3-5)
- Defina `--max-turns` alto (10-20) só em tasks de implementação complexa

---

## 🔍 Troubleshooting

### O Claude não responde ao `@claude`

1. Verifique se o **GitHub App** está instalado: <https://github.com/apps/claude>
2. Confirme que o secret `ANTHROPIC_API_KEY` (ou `CLAUDE_CODE_OAUTH_TOKEN`) está configurado
3. Confirme que **Actions** está habilitado no repositório
4. Verifique o log em **Actions → Claude — Mention Handler**

### Erro de API key inválida

- Regenere a key em <https://console.anthropic.com>
- Verifique se tem créditos disponíveis
- Atualize o secret no repositório

### Claude não está seguindo o CLAUDE.md

- O `CLAUDE.md` deve estar na **raiz** do repositório
- O conteúdo deve estar em formato Markdown válido
- Adicione `--append-system-prompt "Leia CLAUDE.md primeiro"` no workflow

### Custos altos

- Reduza `--max-turns`
- Use modelo menor (Haiku/Sonnet ao invés de Opus)
- Configure **billing alerts** no console da Anthropic
- Limite quem pode mencionar `@claude` (em workflows, use `if: github.event.comment.user.login == 'seu-user'`)

### Quero acesso proativo (Claude pega tasks sozinho)

Esse setup é **reativo** (responde a `@claude`). Para fluxo proativo (Claude lê o backlog e escolhe a próxima task sozinho), você precisa configurar o **MCP do GitHub** no Claude Code local. Documentação: <https://docs.claude.com/en/docs/agents-and-tools/mcp>

---

## 📚 Referências

- [Claude Code GitHub Actions — docs oficiais](https://docs.anthropic.com/en/docs/claude-code/github-actions)
- [Repositório `anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action)
- [GitHub Projects — documentação](https://docs.github.com/en/issues/planning-and-tracking-with-projects)
- [Conventional Commits](https://www.conventionalcommits.org)

---

## 📄 Licença

Esse setup é template livre — adapte ao seu projeto e remova o que não usar.

---

**Feito com 🤖 por humanos que decidiram delegar o tédio para máquinas.**
