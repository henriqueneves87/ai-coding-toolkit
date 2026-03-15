# AI Coding Toolkit

Coleção de **skills**, **commands**, **agents** e **rules** para o [Cursor IDE](https://cursor.sh), projetados para governança de IA, qualidade de código e produtividade. Transforma assistentes de IA em engenheiros disciplinados: planejamento antes de execução, logs legíveis, tratamento de erro explícito e commits com critério de valor.

> **Documentação completa:** [`docs/manual.md`](docs/manual.md)

---

## O que está incluído

### Skills (9 ativos + 5 infra)

| Skill | Tipo | Ativa quando |
|-------|------|-------------|
| `code-with-logs` | Ativo | Script Python executável |
| `create-subagents` | Ativo | Script >200 linhas |
| `error-handling-patterns` | Ativo | Código com try/except ou I/O |
| `testing-patterns` | Ativo | Criação de testes |
| `git-workflow` | Ativo | Commits, branches, PRs |
| `create-documentation` | Ativo | Criação/atualização de docs |
| `create-execution-plan` | Ativo | Planejamento multi-etapas |
| `frontend-conventions` | Ativo | UI/UX em React/Next.js |
| `optimize-context` | Ativo | context.md > 300 linhas ou `/optimize-context` |
| `create-rule` | Infra ⚙️ | Criação de rules do Cursor |
| `create-skill` | Infra ⚙️ | Criação de Agent Skills |
| `create-subagent` | Infra ⚙️ | Criação de subagentes |
| `migrate-to-skills` | Infra ⚙️ | Migração para formato Skills |
| `update-cursor-settings` | Infra ⚙️ | Modificar settings.json |

> Skills **Infra** são utilitários do toolkit. Skills **Ativos** disparam automaticamente por contexto.

### Commands (12)

**Essenciais (uso diário):**

| Command | O que faz |
|---------|-----------|
| `/project-init` | Bootstrap de projeto novo: context.md + roadmap.md + estrutura completa |
| `/context-boot` | Carrega contexto mínimo no início da sessão (~500 tokens) |
| `/apply-conventions` | Checklist pré-geração: logs? erros? plano? skills aplicáveis? |
| `/update-context` | Atualiza context.md com avanço real do projeto |
| `/create-execution-plan` | Plano multi-etapas com dependências e critérios de aceite |
| `/decision-needed` | Apresenta trade-offs e força decisão humana documentada |
| `/optimize-context` | Compacta context.md, arquiva histórico em context_history.md |
| `/checkpoint-and-branch` | Cria branch de backup antes de mudança arriscada |

**Situacionais:**

| Command | O que faz |
|---------|-----------|
| `/architecture-review` | Auditoria DRY-RUN do projeto (não altera código) |
| `/decision-report` | Relatório técnico versionado (DR_XXX) |
| `/summarize-session` | Resume sessão + sugere próximos passos |
| `/help-commands` | Lista todos os comandos com exemplos |

### Agents (8)

| Agent | Delegado quando |
|-------|----------------|
| `debugger` | Bug ou comportamento inesperado |
| `test-writer` | Criação de testes abrangentes |
| `code-reviewer` | Revisão de qualidade e segurança |
| `migration-applier` | Migration SQL (PostgreSQL/Supabase) |
| `context-audit-agent` | Auditoria evidence-first |
| `context-feature-agent` | Feature nova spec-first |
| `context-migration-agent` | Migração safety-first com rollback |
| `context-refactor-agent` | Refatoração incremental com validação |

### Rules (3)

| Rule | Escopo |
|------|--------|
| `ai-conventions-compact.md` | Postura, tarefas, commits, segurança |
| `skills-auto-apply.md` | Tabela de triggers + lista de agents |
| `project-specific-template.md` | Template para regras específicas do projeto |

---

## Instalação

### Cursor

O repositório segue o formato **single plugin** (`.cursor-plugin/plugin.json`) do Cursor. Hoje a UI de Plugins exibe apenas o Marketplace — não há opção para adicionar repositório local como usuário individual. Use a instalação via symlinks:

**Instalação via symlinks (recomendado):**

```bash
git clone https://github.com/henriqueneves87/ai-coding-toolkit.git
```

**Rules** — vá em **Settings → Cursor Settings → Rules** e adicione como User Rules:
- `rules/ai-conventions-compact.md`
- `rules/skills-auto-apply.md`
- `rules/project-specific-template.md` (copie e adapte por projeto)

**Skills** — copie para `~/.cursor/skills/`:

```powershell
Copy-Item -Recurse skills\* "$env:USERPROFILE\.cursor\skills\" -Force
```

**Commands** — copie para `.cursor/commands/` no projeto:

```powershell
Copy-Item -Recurse commands\* ".cursor\commands\" -Force
```

Ou use o instalador:

```powershell
# Windows
.\install.ps1

# Linux/Mac
chmod +x install.sh && ./install.sh
```

---

## Opções do instalador (Cursor)

| Opção | Descrição |
|-------|-----------|
| (padrão) | Cria symlinks — atualização automática via `git pull` |
| `-Copy` / `--copy` | Copia arquivos — não requer Admin no Windows |
| `-Force` / `--force` | Reinstala com backup do existente |
| `-Uninstall` / `--uninstall` | Remove a instalação |

---

## Atualização

**Symlinks (padrão):**

```bash
cd ai-coding-toolkit
git pull
```

**Cópia:**

```bash
git pull
.\install.ps1 -Copy -Force    # Windows
./install.sh --copy --force    # Linux/Mac
```

---

## Templates

O diretório `templates/` contém:

| Template | Uso |
|----------|-----|
| `.pre-commit-config.yaml` | Pre-commit com ruff (lint + format) |
| `.github/workflows/ci.yml` | CI com ruff + pytest no GitHub Actions |

**Aplicar em um projeto:**

```bash
cp templates/.pre-commit-config.yaml .
cp -r templates/.github .
pip install pre-commit && pre-commit install
```

Consulte `docs/manual.md` seção 8 para fundamento anti-vibecoding.

---

## Estrutura

```
ai-coding-toolkit/
├── README.md
├── install.ps1              # Instalador Windows (Cursor)
├── install.sh               # Instalador Linux/Mac (Cursor)
├── docs/
│   └── manual.md            # Documentação completa
├── skills/                  # 9 ativos + 5 infra
│   ├── code-with-logs/
│   ├── create-subagents/
│   ├── error-handling-patterns/
│   ├── testing-patterns/
│   ├── git-workflow/
│   ├── create-documentation/
│   ├── create-execution-plan/
│   ├── frontend-conventions/
│   ├── optimize-context/
│   ├── create-rule/         # infra
│   ├── create-skill/        # infra
│   ├── create-subagent/     # infra
│   ├── migrate-to-skills/   # infra
│   └── update-cursor-settings/ # infra
├── commands/                # 12 commands
│   ├── project-init.md
│   ├── context-boot.md
│   ├── apply-conventions.md
│   ├── update-context.md
│   ├── create-execution-plan.md
│   ├── decision-needed.md
│   ├── checkpoint-and-branch.md
│   ├── architecture-review.md
│   ├── decision-report.md
│   ├── optimize-context.md
│   ├── summarize-session.md
│   └── help-commands.md
├── agents/                  # 8 agents
│   ├── debugger.md
│   ├── test-writer.md
│   ├── code-reviewer.md
│   ├── migration-applier.md
│   ├── context-audit-agent.md
│   ├── context-feature-agent.md
│   ├── context-migration-agent.md
│   └── context-refactor-agent.md
├── rules/                   # 3 rules
│   ├── ai-conventions-compact.md
│   ├── skills-auto-apply.md
│   └── project-specific-template.md
└── templates/               # Pre-commit + CI
    ├── .pre-commit-config.yaml
    └── .github/workflows/ci.yml
```

---

## Compartilhando com a equipe

1. Cada membro clona o repo e roda o instalador
2. Updates: `git pull` (symlinks) ou `git pull + install` (cópia)
3. Para contribuir: PR no repo com novas skills/commands/agents

---

## Licença

MIT
