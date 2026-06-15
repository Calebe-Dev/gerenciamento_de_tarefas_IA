# AGENTS.md — Mapa de arquitetura

Documento lido por qualquer agente de IA (Claude Code, Codex, Antigravity) antes de executar uma task. Mantenha curto e atualizado — é o "mapa" que evita que o agente precise reler o projeto inteiro a cada sessão.

## Estrutura de pastas

```
src/
├── modules/          # Módulos de negócio
│   ├── auth/         # Autenticação — não tocar sem spec
│   ├── leads/         # Gestão de leads
│   ├── users/         # Gestão de usuários
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
- CI/CD: [...]

## Padrões usados

- Repository Pattern em todos os módulos
- DI via [framework]
- Errors centralizados em `src/shared/errors/`

## Variáveis de ambiente críticas

Listadas em `.env.example` — nunca commitar `.env`.

## Comandos úteis do projeto

```bash
# instalar dependências
[...]

# rodar em desenvolvimento
[...]

# rodar testes
[...]

# lint / format
[...]
```
