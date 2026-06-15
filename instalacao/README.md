# 🚀 Instalação via prompt

Forma rápida de aplicar o [`template/`](../template) em qualquer projeto: copie o prompt abaixo, cole no Claude Code / Codex / Antigravity **na raiz do projeto que você quer organizar**, e o agente clona, copia e preenche os arquivos sozinho.

---

## O prompt

```text
Configure o sistema de organização de projetos com IA (Claude Code / Codex /
Antigravity, sem API key) neste projeto, usando o template de:
https://github.com/Calebe-Dev/gerenciamento_de_tarefas_IA

1. Clone temporariamente o repositório de template:
   git clone --depth 1 https://github.com/Calebe-Dev/gerenciamento_de_tarefas_IA.git /tmp/gerenciamento_de_tarefas_IA

2. Copie para a raiz deste projeto (NÃO sobrescreva arquivos que já existam —
   se houver conflito, me avise antes de continuar):
   - template/CLAUDE.md                    → ./CLAUDE.md
   - template/docs/AGENTS.md                → ./docs/AGENTS.md
   - template/docs/PROGRESS.md              → ./docs/PROGRESS.md
   - template/docs/WOZ.md                   → ./docs/WOZ.md
   - template/.github/ISSUE_TEMPLATE/*.yml  → ./.github/ISSUE_TEMPLATE/

3. Remova o clone temporário (/tmp/gerenciamento_de_tarefas_IA).

4. Analise este projeto (package.json / pyproject.toml / etc., estrutura de
   pastas, scripts de teste/lint/build) e preencha os placeholders entre
   [colchetes] em ./CLAUDE.md e ./docs/AGENTS.md com as informações reais
   deste projeto.

5. Me mostre um resumo do que foi criado/preenchido e o que ainda precisa de
   revisão manual.

Não crie branch, não faça commit e não abra PR — apenas crie/edite os
arquivos localmente para eu revisar.
```

---

## Depois de rodar o prompt

1. Revise `CLAUDE.md` e `docs/AGENTS.md` — o agente preenche com base no que encontra no projeto, mas vale checar
2. Configure o GitHub Project, labels e branch protection seguindo [`fluxo-de-trabalho/`](../fluxo-de-trabalho/README.md)
3. Comece a usar: crie uma issue com um dos templates e abra o agente seguindo o fluxo descrito

---

## Por que isso é viável

- `git clone` de um repositório público não exige API key nem login extra — só `git`, que já está disponível em qualquer ambiente onde Claude Code, Codex ou Antigravity rodam
- Os três agentes executam comandos de shell, então conseguem clonar, copiar arquivos e limpar o temporário sozinhos, sem ferramentas adicionais
- Se este repositório de template for privado, basta o usuário já ter `gh auth login` configurado — o `git` reaproveita esse login via HTTPS automaticamente
