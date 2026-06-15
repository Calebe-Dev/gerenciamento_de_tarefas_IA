# Codex CLI

Agente de codificação da OpenAI que roda no terminal. Lê o repositório, edita arquivos, executa comandos e abre PRs a partir de instruções em linguagem natural.

## Instalação

```bash
npm install -g @openai/codex
```

Requer Node.js 18+.

## Autenticação — sem API key

Incluso nos planos **ChatGPT Plus, Pro, Business, Edu e Enterprise**.

```bash
codex
# ou explicitamente:
codex login
```

Escolha "Sign in with ChatGPT" — abre o navegador para o login OAuth da sua conta ChatGPT. O uso passa a consumir os créditos incluídos no seu plano, **sem precisar de uma API key da OpenAI Platform**.

> ⚠️ Se fizer login com uma API key em vez da conta ChatGPT, o Codex passa a faturar por uso na OpenAI Platform — fora do escopo deste guia. Use sempre o login via ChatGPT.

## Comandos essenciais para o fluxo deste repo

```bash
# abrir o Codex na raiz do projeto
codex

# rodar uma instrução direto, sem sessão interativa
codex exec "leia a issue #42 com gh issue view 42 e implemente"

# verificar se está autenticado via ChatGPT (e não via API key)
codex login status
```

Assim como o Claude Code, o Codex executa comandos de shell — ele pode usar o `gh` CLI já autenticado na máquina para ler issues, abrir PRs e atualizar o GitHub Project (veja o [fluxo de trabalho](../../fluxo-de-trabalho/README.md)).

## Prompt de exemplo

```
Leia a issue #42 com `gh issue view 42`, siga as instruções do CLAUDE.md e do
docs/AGENTS.md, implemente o que está descrito nos critérios de aceite, rode
os testes, abra um PR referenciando a issue e atualize o docs/PROGRESS.md.
```

## Quando usar Codex

- Implementação padrão de features/bugs bem especificados (`type:feature`, `type:bug` com critérios claros)
- Boa opção quando você já tem assinatura ChatGPT Plus/Pro e quer distribuir carga entre agentes
- Tasks onde já existe uma spec detalhada — Codex tende a seguir bem instruções objetivas
