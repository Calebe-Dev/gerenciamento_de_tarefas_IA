# Antigravity

Plataforma de desenvolvimento agêntico do Google (IDE fork do VS Code + CLI `agy`, sucessor do Gemini CLI). Multi-modelo — suporta Gemini 3 Pro/Flash, Claude Sonnet/Opus 4.6 e GPT-OSS-120B — e roda **até 5 agentes em paralelo** com um navegador Chrome embutido para verificação visual automática.

## Instalação

**CLI (`agy`):**

```bash
curl -fsSL https://antigravity.google/cli/install.sh | bash
```

**IDE:** baixe em [antigravity.google](https://antigravity.google) (disponível em preview público gratuito).

## Autenticação — sem API key

```bash
agy
```

Na primeira execução, escolha login via **conta Google** (OAuth no navegador) — não use "GCP project" se quiser evitar billing de API. Durante o preview público, o uso é gratuito com rate limits generosos.

## Modos de uso para o fluxo deste repo

### 1. CLI (`agy`) — uso direto no terminal

```bash
agy exec "leia a issue #42 com gh issue view 42 e implemente seguindo o CLAUDE.md"
```

Igual ao Claude Code/Codex: executa comandos de shell, então consegue usar o `gh` CLI já autenticado para issues, PRs e o GitHub Project (veja o [fluxo de trabalho](../../fluxo-de-trabalho/README.md)).

### 2. IDE — Manager view (múltiplos agentes em paralelo)

Abra o projeto no Antigravity IDE → **Manager view** → cada agente recebe uma task (ex: uma issue diferente) e trabalha em paralelo, com o browser embutido testando a própria UI que está sendo construída.

Útil para pegar várias issues `scope:S`/`M` do board de uma vez e distribuir entre os agentes paralelos.

## Prompt de exemplo

```
Leia a issue #42 com `gh issue view 42`, siga as instruções do CLAUDE.md e do
docs/AGENTS.md, implemente o que está descrito nos critérios de aceite, valide
visualmente no browser embutido se for uma mudança de UI, rode os testes,
abra um PR referenciando a issue e atualize o docs/PROGRESS.md.
```

## Quando usar Antigravity

- Mudanças com componente visual/UI — o browser embutido testa cliques, formulários e tira screenshots sem você precisar abrir o DevTools
- Várias tasks pequenas/médias (`scope:S`/`M`) que podem rodar em paralelo via Manager view
- Quando quiser comparar resultados de modelos diferentes (Gemini vs Claude) na mesma task
