# PROGRESS.md — Histórico de tasks executadas

Memória persistente entre sessões de agentes. Cada task concluída adiciona uma entrada — independente de qual agente (Claude Code, Codex ou Antigravity) executou.

---

## 2026-06-15

### #42: Adicionar endpoint POST /leads — DONE

- **Agente:** Claude Code
- Criado `LeadsController.create()` com validação
- Adicionados 5 testes unitários + 2 de integração
- Cobertura do módulo `leads`: 87%
- Decisão: usar `class-validator` ao invés de Zod (consistência com o resto do projeto)
- PR: #45

---

## 2026-06-14

### #40: Refatorar AuthService — DONE

- **Agente:** Antigravity
- Separado em AuthService + TokenService
- API pública mantida
- 12 testes adicionados
- PR: #41
