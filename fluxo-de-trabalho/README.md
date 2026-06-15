# Fluxo de trabalho — GitHub Project + agentes locais

Como organizar e executar tasks usando um **GitHub Project** (board) como fonte de verdade, gerenciado via `gh` CLI por você e pelos agentes (Claude Code / Codex / Antigravity) rodando localmente — sem bots de CI, sem API key.

---

## Pré-requisitos

- [`gh` CLI](https://cli.github.com) instalado e autenticado:

  ```bash
  gh auth login
  ```

- Escopo `project` habilitado (não vem por padrão no `gh auth login`):

  ```bash
  gh auth refresh -s project
  ```

- Pelo menos um agente configurado — veja [`guias/`](../guias)

---

## 1. Setup inicial do repositório

### Recursos do GitHub

Em **Settings → General**, ative:

- ✅ **Issues**
- ✅ **Projects**

### Issue templates

Copie [`template/.github/ISSUE_TEMPLATE/`](../template/.github/ISSUE_TEMPLATE) para `.github/ISSUE_TEMPLATE/` no seu projeto. Define 4 tipos de task (Refactor, Bug, Test, Feature), cada um forçando o mínimo de informação necessária — incluindo um campo "Agente sugerido".

### Labels

Crie tudo via `gh`, sem precisar abrir o navegador:

```bash
gh label create "type:refactor" --color 0e8a16 --description "Refatoração"
gh label create "type:bug"      --color d73a4a --description "Correção de bug"
gh label create "type:test"     --color fbca04 --description "Adição de testes"
gh label create "type:feature"  --color 1d76db --description "Nova funcionalidade"
gh label create "type:docs"     --color 5319e7 --description "Documentação"

gh label create "scope:S" --color c5def5 --description "Pequena (≤2h)"
gh label create "scope:M" --color bfdadc --description "Média (meio dia)"
gh label create "scope:L" --color bfd4f2 --description "Grande (+ de um dia)"

gh label create "agent:claude-code" --color 7c3aed --description "Sugerido para o Claude Code"
gh label create "agent:codex"       --color 10a37f --description "Sugerido para o Codex"
gh label create "agent:antigravity" --color 4285f4 --description "Sugerido para o Antigravity"

gh label create "needs-spec" --color e99695 --description "Precisa de spec antes de executar"
gh label create "blocked"    --color 000000 --description "Bloqueada por outra task"
```

### GitHub Project (board)

```bash
gh project create --owner <seu-usuario-ou-org> --title "Nome do Projeto"
```

Depois, abra o Project na web (uma única vez) e edite o campo **Status** para ter as colunas: `Backlog` · `Triage` · `Ready` · `In Progress` · `Review` · `Done`. Habilite as automações nativas do GitHub Projects:

- Issue fechada → mover para `Done`
- PR aberto referenciando a issue → mover para `Review`

### Branch protection

Em **Settings → Branches → Add rule** para `main`:

- ✅ Require pull request before merging
- ✅ Require status checks
- ✅ Require conversation resolution
- ✅ Do not allow bypassing the above settings

Assim nenhum agente (nem você) dá merge direto na `main` — sempre via PR revisado.

### Arquivos de instrução

Copie e adapte para o seu projeto:

- [`template/CLAUDE.md`](../template/CLAUDE.md) → raiz do repo
- [`template/docs/AGENTS.md`](../template/docs/AGENTS.md) → `docs/AGENTS.md`
- [`template/docs/PROGRESS.md`](../template/docs/PROGRESS.md) → `docs/PROGRESS.md`

---

## 2. Fluxo do dia a dia

```
┌──────────────┐
│ 1. Criar     │  gh issue create --template feature.yml
│   Issue      │  (ou pela UI) → cai em "Backlog"
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 2. Triage    │  Você adiciona labels (type/scope/agent) e prioridade
│  (você)      │  gh issue edit <n> --add-label "scope:M,agent:codex"
└──────┬───────┘  → mover para "Ready"
       │
       ▼
┌──────────────┐
│ 3. Escolher  │  Você abre, na sua máquina, o agente indicado pela
│   agente     │  label `agent:*` (Claude Code, Codex ou Antigravity)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 4. Agente    │  Lê CLAUDE.md + docs/AGENTS.md + docs/PROGRESS.md +
│  executa     │  `gh issue view <n>` → branch → implementa → testa
└──────┬───────┘  → `gh pr create` → atualiza docs/PROGRESS.md
       │
       ▼
┌──────────────┐
│ 5. Review    │  Você (ou outro agente) roda `gh pr diff <n>` e revisa
│              │  Item move para "Review" (automação do Project)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ 6. Merge     │  gh pr merge <n> --squash --delete-branch
│              │  Issue fecha → item move para "Done" automaticamente
└──────────────┘
```

---

## 3. Cheat-sheet `gh`

### Issues

```bash
gh issue create --template feature.yml            # criar via template
gh issue list --label "type:bug" --state open      # listar por label
gh issue view 42                                   # ver detalhes/critérios
gh issue edit 42 --add-label "scope:M,agent:codex"
gh issue comment 42 --body "Comentário..."
```

### Pull Requests

```bash
gh pr create --title "feat: ... (#42)" --body "Closes #42" --base main
gh pr view 45
gh pr diff 45             # revisar as mudanças
gh pr checks 45           # ver status do CI
gh pr merge 45 --squash --delete-branch
```

### Project (board)

```bash
gh project list --owner <seu-usuario>
gh project item-list <numero> --owner <seu-usuario>      # itens + status atual
gh project field-list <numero> --owner <seu-usuario>      # IDs dos campos (ex: Status)

# mover um item para outra coluna (ex: "In Progress")
gh project item-edit \
  --id <item-id> \
  --field-id <status-field-id> \
  --project-id <project-id> \
  --single-select-option-id <option-id-da-coluna>
```

> Os IDs (`item-id`, `field-id`, `project-id`, `option-id`) saem de `item-list --format json` e `field-list --format json`. Peça ao agente para descobri-los uma vez e documentar em `docs/AGENTS.md` — não precisa repetir a cada task.

---

## 4. Sobre GitHub Actions / API key

Este fluxo **não usa GitHub Actions nem chaves de API** — toda a execução roda localmente, autenticada pela sua assinatura (Claude Pro/Max, ChatGPT Plus/Pro ou conta Google) e pelo seu login no `gh`. Zero custo por token, zero secrets para gerenciar.

Se no futuro você quiser bots reativos (ex: revisão automática em todo PR via `@claude` em CI), isso é possível com `anthropic/claude-code-action` + `ANTHROPIC_API_KEY`/`CLAUDE_CODE_OAUTH_TOKEN` — mas está fora do escopo deste guia, que é CLI-first.
