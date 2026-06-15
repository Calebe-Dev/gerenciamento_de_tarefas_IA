# CLAUDE.md

Instruções principais para o agente de IA — **Claude Code, Codex ou Antigravity** — ao trabalhar neste repositório. Funciona com qualquer um dos três, autenticados via assinatura (sem API key).

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

## Comandos `gh` disponíveis

Este projeto é organizado com um **GitHub Project** (board) + Issues. Use o `gh` CLI — já autenticado na máquina — para interagir com eles. Nenhuma API key adicional é necessária.

```bash
# ver detalhes de uma issue (critérios de aceite, labels, etc)
gh issue view <numero>

# listar issues abertas por label
gh issue list --label "type:feature"

# comentar na issue (ex: pedir esclarecimento)
gh issue comment <numero> --body "..."

# criar uma sub-issue vinculada a um Epic (papel Woz — veja docs/WOZ.md)
gh issue create --title "..." --label "type:feature" --parent <epic-numero>

# abrir PR referenciando a issue
gh pr create --title "feat: ..." --body "Closes #42" --base main

# ver/mover o status do item no Project
gh project item-list <numero> --owner <owner>
gh project item-edit --id <item-id> --field-id <field-id> --project-id <project-id> --single-select-option-id <option-id>
```

> Cheat-sheet completa de comandos `gh project` (incluindo como descobrir os IDs acima): veja [`fluxo-de-trabalho/README.md`](../fluxo-de-trabalho/README.md).

## Papel especial: Woz (Product Owner)

Para tasks de **backlog** — triagem, refinamento de issues, quebra de Epics
em sub-issues — o agente deve adotar a persona descrita em
[`docs/WOZ.md`](docs/WOZ.md). Não é um quarto agente: é um modo de trabalho
que qualquer um dos três veste quando o pedido é sobre organizar o backlog,
não implementar código.

## Antes de executar qualquer task

1. Leia `docs/AGENTS.md` para entender a arquitetura
2. Leia `docs/PROGRESS.md` para saber o estado atual
3. Leia os critérios de aceite da Issue: `gh issue view <numero>`
4. Se a issue tiver um "Epic pai" (`#N`), leia também `gh issue view N` para entender o contexto maior
5. Se algo estiver ambíguo, **comente na Issue pedindo esclarecimento** (`gh issue comment`) ao invés de chutar

## Ao concluir uma task

1. Garanta que os testes passam (`npm test` / `pytest` / etc)
2. Atualize `docs/PROGRESS.md` com:
   - Issue resolvida
   - Agente que executou (Claude Code / Codex / Antigravity)
   - Mudanças principais
   - Decisões tomadas
3. Abra um PR referenciando a issue (`gh pr create`)
4. No corpo do PR, inclua:
   - Resumo das mudanças
   - Como testar
   - Checklist dos critérios de aceite
5. Mova o item correspondente para `Review` no GitHub Project (`gh project item-edit`)

## Permissões

- ✅ Pode: ler/escrever código, rodar testes, criar branches, commitar, abrir PRs, comentar Issues, mover itens do Project
- ❌ Não pode: dar merge na `main`, force-push, deletar branches, alterar workflows do CI, commitar secrets
