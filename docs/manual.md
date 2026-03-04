# Manual do AI Coding Toolkit

Versão: 5.0
Última atualização: 2026-02-24

---

## 1. O que é

O AI Coding Toolkit é um conjunto de **commands, skills, rules e agents** que transformam assistentes de IA (Cursor, Windsurf, Trae) em engenheiros de software disciplinados. Em vez de aceitar código "que compila", o toolkit força planejamento antes de execução, logs legíveis, tratamento de erro explícito, commits com critério de valor e documentação viva.

---

## 2. Instalação

### Cursor

1. Clone o repositório na máquina:
   ```bash
   git clone https://github.com/henriqueneves87/ai-coding-toolkit.git
   ```
2. **Rules** — vá em **Settings → Cursor Settings → Rules** e adicione como User Rules:
   - `rules/ai-conventions-compact.md` — postura, tarefas, commits
   - `rules/skills-auto-apply.md` — triggers automáticos
   - `rules/project-specific-template.md` — copie e adapte por projeto
3. **Skills** — copie a pasta `skills/` para `~/.cursor/skills/` (global) ou `.cursor/skills/` no projeto:
   ```powershell
   # Global (vale para todos os projetos)
   Copy-Item -Recurse skills\* "$env:USERPROFILE\.cursor\skills\" -Force
   ```
4. **Commands** — copie a pasta `commands/` para `.cursor/commands/` no projeto:
   ```powershell
   Copy-Item -Recurse commands\* ".cursor\commands\" -Force
   ```
   Os commands ficam disponíveis via `/` no chat do Cursor.

### Windsurf

1. Clone o repositório
2. Execute: `cd windsurf-toolkit && .\install.ps1` (ou `./install.sh` no Linux/Mac)
3. Skills, workflows e rules são copiados para `~/.codeium/windsurf/`

### Trae

1. Clone o repositório
2. Execute: `cd trae-toolkit && .\install.ps1` (ou `./install.sh` no Linux/Mac)
3. O `project_rules.md` compilado é copiado para `.trae/` do projeto

---

## 3. Primeiro Projeto — `/project-init`

Ao iniciar um projeto novo, invoque `/project-init` no chat. A IA vai:

1. **Perguntar** nome, objetivo, stack, fase atual e próximo passo
2. **Criar a árvore de pastas** numeradas:
   ```
   docs/
     00_overview/        → context.md, roadmap.md
     01_architecture/    → overview.md, conventions.md, integrations.md
     02_business_rules/
     03_api/
     04_operations/
     05_decisions/reports/
     06_testing/
     07_changelog/
     _scratchpad/
     execution_plans/
   ```
3. **Preencher** `context.md` (fonte única de verdade do projeto) e `roadmap.md` (fases planejadas)
4. **Sugerir** o próximo passo: `/create-execution-plan`

**Exemplo real:**
```
/project-init
Nome: saphiro-baas
Objetivo: API multi-tenant para gestão de associações esportivas
Stack: Python + FastAPI + Supabase
Fase: 0.3 — MVP funcional
Próximo passo: Implementar módulo de pagamentos
```

---

## 4. Fluxo Diário

```
Início da sessão          Durante o trabalho          Fim da sessão
       │                         │                          │
  /context-boot             código + commits          /summarize-session
       │                         │                          │
  (contexto carregado)    /update-context              (resumo salvo)
       │                  (a cada avanço real)
  /apply-conventions
  (checklist ativo)
```

### 3 comandos essenciais do dia a dia

| Comando | Quando usar | O que acontece |
|---------|-------------|----------------|
| `/context-boot` | Início de toda sessão | Carrega context.md + roadmap.md (~500 tokens). Sem isso, a IA começa do zero. |
| `/apply-conventions` | Antes de gerar código | Ativa checklist: logs? erros? plano? skills? Sem isso, a IA gera código "genérico". |
| `/update-context` | Após avanço real | Atualiza context.md com estado atual. Sem isso, a próxima sessão começa desatualizada. |

### 4 comandos situacionais

| Comando | Quando usar |
|---------|-------------|
| `/create-execution-plan` | Tarefa com 3+ etapas, bug complexo, feature grande |
| `/decision-needed` | Trade-off técnico que precisa de decisão humana |
| `/checkpoint-and-branch` | Antes de mudança estrutural arriscada (airbag) |
| `/help-commands` | Esqueceu qual comando usar |

---

## 5. Comandos — Referência com Exemplos

### /project-init
**O que faz:** Cria estrutura completa de docs + context.md + roadmap.md
**Quando usar:** Início de projeto novo
**Quando a IA falha sem ele:** Cria docs soltos, sem estrutura, sem context.md

### /context-boot
**O que faz:** Carrega contexto mínimo (context.md + roadmap.md) no início da sessão
**Quando usar:** Toda vez que abrir uma nova conversa
**Quando a IA falha sem ele:** Repete perguntas já respondidas, ignora decisões tomadas

```
/context-boot
```

### /apply-conventions
**O que faz:** Força checklist de convenções antes de gerar código
**Quando usar:** Antes de pedir código, especialmente em tarefas governadas
**Quando a IA falha sem ele:** Gera código sem logs, sem tratamento de erro, sem plano

```
/apply-conventions
Tarefa: Criar endpoint de pagamento com validação de cartão
```

### /update-context
**O que faz:** Atualiza context.md com avanço real do projeto
**Quando usar:** Após concluir feature, corrigir bug importante, tomar decisão técnica
**Quando a IA falha sem ele:** Próxima sessão começa com contexto desatualizado

```
/update-context
Avanço: Módulo de pagamentos implementado e testado. Decisão: usar Stripe em vez de PagSeguro.
```

### /create-execution-plan
**O que faz:** Decompõe tarefa complexa em etapas ordenadas com dependências
**Quando usar:** Feature com 3+ etapas, bug que afeta múltiplos arquivos, refatoração grande
**Quando a IA falha sem ele:** Tenta fazer tudo de uma vez, esquece etapas, cria conflitos

```
/create-execution-plan
Objetivo: Migrar autenticação de session-based para JWT
Contexto: 15 endpoints afetados, 3 middlewares, testes existentes
```

### /decision-needed
**O que faz:** Apresenta trade-offs e força decisão humana documentada
**Quando usar:** Escolha entre abordagens com impacto significativo
**Quando a IA falha sem ele:** Escolhe sozinha sem documentar, decisão se perde

```
/decision-needed
Contexto: Cache de sessões — Redis vs in-memory vs banco
Restrição: Budget limitado, sem infra extra
```

### /checkpoint-and-branch
**O que faz:** Cria branch de backup antes de mudança arriscada
**Quando usar:** Refatoração grande, migração de banco, mudança de arquitetura
**Quando a IA falha sem ele:** Mudança quebra algo e não tem como voltar facilmente

```
/checkpoint-and-branch
Motivo: Refatorar camada de acesso ao banco de dados
```

### /optimize-context
**O que faz:** Compacta context.md movendo histórico para context_history.md
**Quando usar:** context.md > 300 linhas, após concluir fase grande, sessões lentas por excesso de tokens
**Quando a IA falha sem ele:** context.md cresce indefinidamente, consumindo tokens em toda interação

```
/optimize-context
```

### Comandos situacionais

| Comando | O que faz | Quando usar |
|---------|-----------|-------------|
| `/architecture-review` | Auditoria DRY-RUN (não altera código) | Mensalmente ou antes de fase nova |
| `/decision-report` | Relatório técnico versionado (DR_XXX) | Após decisão técnica importante |
| `/summarize-session` | Resume sessão + sugere próximos passos | Fim de sessão de trabalho |
| `/help-commands` | Lista todos os comandos disponíveis | Quando não lembrar qual usar |

---

## 6. O que acontece automaticamente

O toolkit tem **skills** e **agents** que a IA aplica sozinha quando detecta o contexto certo. Você não precisa invocar — eles ativam automaticamente.

### Skills (9 ativos)

| Skill | Ativa quando | O que faz |
|-------|-------------|-----------|
| `code-with-logs` | Script Python executável | Adiciona logs de progresso com timestamp `[HH:MM:SS]`, emoji, ETA |
| `create-subagents` | Script >200 linhas | Divide em subagentes modulares com early return |
| `error-handling-patterns` | Código com try/except ou I/O | Força early return, erros explícitos, sem catch silencioso |
| `testing-patterns` | Criação de testes | Padrão AAA, edge cases, nomeação descritiva |
| `git-workflow` | Commits, branches, PRs | Critério de valor, mensagens padronizadas |
| `create-documentation` | Criação/atualização de docs | Estrutura organizada, anti-dumping |
| `create-execution-plan` | Planejamento multi-etapas | Plano com dependências, blocos paralelos, critérios de aceite |
| `frontend-conventions` | UI/UX em React/Next.js | Tokens semânticos, feedback (toast/skeleton/empty), a11y |
| `optimize-context` | context.md > 300 linhas | Compacta context.md, arquiva histórico em context_history.md |

### Agents (8 disponíveis)

| Agent | Delegado quando | O que faz |
|-------|----------------|-----------|
| `debugger` | Bug ou comportamento inesperado | Investigação sistemática com root cause analysis |
| `test-writer` | Precisa de testes | Gera testes abrangentes com cobertura de edge cases |
| `code-reviewer` | Código novo ou modificado | Revisão de qualidade, segurança e manutenibilidade |
| `migration-applier` | Migration SQL | Validação, aplicação e verificação de migrations |
| `context-audit-agent` | Auditoria necessária | Coleta evidências antes de analisar |
| `context-feature-agent` | Feature nova | Especifica antes de implementar |
| `context-migration-agent` | Migração estrutural | Backup, validação antes/depois, rollback plan |
| `context-refactor-agent` | Refatoração | Passos incrementais com validação após cada um |

---

## 7. Fluxos Completos (Receitas)

### Iniciar projeto novo

```
1. /project-init                    → estrutura + context.md + roadmap.md
2. /create-execution-plan           → plano da primeira entrega
3. Executar tarefa por tarefa       → com /apply-conventions ativo
4. /update-context                  → registrar avanço
```

### Implementar feature

```
1. /context-boot                    → carregar contexto
2. /create-execution-plan           → decompor a feature
   Objetivo: Implementar sistema de notificações push
   Contexto: API REST existente, banco Supabase, sem fila ainda
3. Executar cada tarefa do plano    → skills ativam automaticamente
4. /update-context                  → registrar feature concluída
5. Commit com critério de valor     → feat: add push notification system
```

### Corrigir bug

```
1. /context-boot                    → carregar contexto
2. Descrever o bug com evidências   → agent debugger ativa automaticamente
   "Endpoint /payments retorna 500 quando valor é decimal. Log: TypeError..."
3. Se bug é complexo:
   /create-execution-plan           → plano de correção
4. Corrigir + testar                → agent test-writer gera testes
5. /update-context                  → registrar correção
6. Commit                           → fix: handle decimal values in payment endpoint
```

### Refatorar código

```
1. /context-boot                    → carregar contexto
2. /checkpoint-and-branch           → backup antes de mexer
   Motivo: Refatorar módulo de autenticação
3. /create-execution-plan           → plano incremental
4. Executar passo a passo           → agent context-refactor-agent ativa
5. Validar após cada passo          → testes passando?
6. /update-context                  → registrar refatoração
7. Commit                           → refactor: extract auth middleware from routes
```

### Reorganizar projeto

```
1. /context-boot → /architecture-review → /decision-needed
2. /checkpoint-and-branch → /create-execution-plan → executar
3. /update-context → commit
```

### Fim de sessão

```
1. /update-context → /summarize-session → commit (se houver valor)
```

---

## 8. Pre-commit e CI

### Por que isso existe

Código gerado por IA sem revisão mecânica é **vibecoding** — funciona no momento, quebra depois. Pre-commit hooks e CI são a última barreira antes de código problemático entrar no repositório.

### Pre-commit (local)

O toolkit inclui template em `templates/.pre-commit-config.yaml` com **ruff** (lint + format):

```bash
pip install pre-commit
cp templates/.pre-commit-config.yaml .pre-commit-config.yaml
pre-commit install
```

Todo `git commit` passa por ruff (lint + auto-fix) e ruff-format. Se não passa, o commit é **bloqueado**.

### CI (GitHub Actions)

Template em `templates/.github/workflows/ci.yml`. Roda lint + pytest em todo push/PR. Se falhar, merge é bloqueado.

### Fundamento

Sem enforcement: IA gera, você aceita, erros acumulam. Com enforcement: pre-commit valida localmente, CI confirma no servidor. Código que não passa **não entra**.

---

## 9. Anti-patterns

### Vibecoding

**O que é:** Aceitar código gerado por IA sem entender, sem plano, sem testes.

**Sinais:**
- Pedir "faz isso" sem contexto → IA inventa stack, estrutura, nomes
- Não usar `/context-boot` → cada sessão começa do zero
- Não usar `/apply-conventions` → código sem logs, sem erros explícitos
- Aceitar tudo sem revisar → bugs entram silenciosamente
- Commitar tudo → histórico vira lixo

**Antídoto:** Usar o fluxo diário (seção 4). Sempre carregar contexto, sempre aplicar convenções, sempre revisar antes de commitar.

### Context Dumping

**O que é:** Colar blocos enormes de código, logs ou SQL no chat esperando que a IA "entenda".

**Sinais:**
- Colar 500 linhas de log → IA se perde, responde genérico
- Colar arquivo inteiro → tokens desperdiçados, resposta superficial
- Repetir contexto em toda mensagem → janela de contexto esgota rápido

**Antídoto:** Sintetizar e referenciar. Em vez de colar o log inteiro, dizer: "Erro no endpoint /payments, linha 45: TypeError. Log completo em `logs/error_2026-02-24.log`". Usar `/context-boot` para carregar contexto estruturado.

---

## 10. Referência Rápida

### Comandos

| Comando | Uso | Frequência |
|---------|-----|------------|
| `/project-init` | Bootstrap de projeto novo | Uma vez por projeto |
| `/context-boot` | Carregar contexto no início da sessão | Toda sessão |
| `/apply-conventions` | Checklist antes de gerar código | Toda sessão |
| `/update-context` | Registrar avanço real | Após cada entrega |
| `/create-execution-plan` | Planejar tarefa complexa | Tarefas 3+ etapas |
| `/decision-needed` | Forçar decisão humana | Trade-offs importantes |
| `/optimize-context` | Compactar context.md (economia de tokens) | context.md > 300 linhas |
| `/checkpoint-and-branch` | Backup antes de mudança arriscada | Refatorações grandes |
| `/architecture-review` | Auditoria do projeto | Mensal |
| `/decision-report` | Relatório técnico formal | Decisões importantes |
| `/summarize-session` | Resumo de fim de sessão | Fim do dia |
| `/help-commands` | Lista de comandos | Quando precisar |

### Skills (automáticos)

| Skill | Trigger |
|-------|---------|
| `code-with-logs` | Script Python executável |
| `create-subagents` | Script >200 linhas |
| `error-handling-patterns` | Código com try/except ou I/O |
| `testing-patterns` | Criação de testes |
| `git-workflow` | Commits, branches, PRs |
| `create-documentation` | Docs |
| `create-execution-plan` | Planejamento multi-etapas |
| `frontend-conventions` | UI/UX React/Next.js |
| `optimize-context` | context.md > 300 linhas |

### Agents (automáticos)

| Agent | Trigger |
|-------|---------|
| `debugger` | Bugs |
| `test-writer` | Testes |
| `code-reviewer` | Código novo/modificado |
| `migration-applier` | Migrations SQL |
| `context-audit-agent` | Auditorias |
| `context-feature-agent` | Features novas |
| `context-migration-agent` | Migrações |
| `context-refactor-agent` | Refatorações |

### Rules (3 no total)

| Rule | Escopo |
|------|--------|
| `ai-conventions-compact.md` | Postura, tarefas, commits, segurança |
| `skills-auto-apply.md` | Tabela de triggers + lista de agents |
| `project-specific-template.md` | Template para regras do projeto |
