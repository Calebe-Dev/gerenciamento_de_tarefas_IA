# Claude Code

CLI agêntica da Anthropic. Roda no terminal, entende o repositório inteiro, edita arquivos, executa comandos, faz commits/PRs e segue instruções de `CLAUDE.md`/`AGENTS.md`.

## Instalação

```bash
# Instalador nativo (recomendado, Mac/Linux)
curl -fsSL https://claude.ai/install.sh | bash

# Alternativa via npm
npm install -g @anthropic-ai/claude-code
```

## Autenticação — sem API key

Requer uma assinatura **Claude Pro** ou **Claude Max** (o plano gratuito do claude.ai não dá acesso ao Claude Code).

```bash
claude
> /login
```

Escolha "Claude account" e siga o fluxo no navegador. A partir daí, todo uso consome a cota da sua assinatura — **nenhuma `ANTHROPIC_API_KEY` é necessária**. Para checar o status/limite da assinatura, use `/status` dentro do Claude Code.

## Comandos essenciais para o fluxo deste repo

```bash
# abrir o Claude Code na raiz do projeto
claude

# dentro da sessão:
/login        # autenticar com a assinatura
/status       # ver plano atual e uso
/init         # gerar um CLAUDE.md inicial a partir do código existente
/agents       # configurar subagentes especializados (opcional)
```

Como o `gh` CLI já está autenticado na máquina (veja [fluxo de trabalho](../../fluxo-de-trabalho/README.md)), o Claude Code pode chamá-lo livremente via `Bash` — não precisa de nenhuma configuração extra de permissões para issues/PRs do GitHub.

## Prompt de exemplo

```
Leia a issue #42 com `gh issue view 42`, siga as instruções do CLAUDE.md e do
docs/AGENTS.md, implemente o que está descrito nos critérios de aceite, rode
os testes, abra um PR referenciando a issue e atualize o docs/PROGRESS.md.
```

## Quando usar Claude Code

- Raciocínio mais profundo: bugs difíceis, refactors críticos, decisões de arquitetura
- Code review crítico de PRs (inclusive PRs abertos pelos outros agentes)
- Tasks onde seguir regras/instruções com precisão importa mais que velocidade

## Limites do plano

- **Pro:** cota menor, ótimo para tasks pontuais (`scope:S`/`M`)
- **Max:** cota bem maior, permite sessões longas e tasks `scope:L`
- Se a cota acabar no meio de uma task, o Claude Code avisa — pause e retome depois, ou troque para outro agente disponível
