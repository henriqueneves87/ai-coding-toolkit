---
name: checkpoint-and-branch
description: Cria branch de backup antes de mudança arriscada (checkpoint + branch dedicado).
---

# checkpoint-and-branch

## CHECKPOINT SEGURO + BRANCH DEDICADO (HARD STOP)

Versão: 1.3  
Tipo: Command  
Uso: Obrigatório antes de reorganização/deleção/refatoração estrutural  
Modo: PRÉ-EXECUÇÃO (NÃO MODIFICA NADA SEM CONFIRMAÇÃO)

---

## OBJETIVO

Criar um "airbag" antes de qualquer mudança estrutural:

- garantir worktree limpo
- criar commit de checkpoint
- criar branch dedicado
- bloquear execução até validação do usuário

---

## DEFINIÇÃO (IMPORTANTE)

**Worktree limpo** = `git status` não mostra:
- modified
- untracked
- staged
- pending changes

Ou seja: "nothing to commit, working tree clean".

---

## PRÉ-CHECAGEM (OBRIGATÓRIA)

A IA deve instruir o usuário a executar:

```
git status
```

### Interpretação do resultado

**Se estiver limpo:** Prosseguir para criação do branch.

**Se estiver sujo:** A IA deve orientar um destes caminhos:

- **Opção A (recomendada):** commit rápido de checkpoint
  - incluir tudo que está pendente (`git add -A`)
  - registrar com mensagem padronizada (ver seção abaixo)

- **Opção B:** stash (apenas se o usuário pedir explicitamente)
  - evitar, pois esconde contexto e pode causar confusão
  - usar apenas em casos excepcionais

---

## GUARDRAIL PREVENTIVO — VALIDAÇÃO ANTES DO CHECKPOINT (OBRIGATÓRIO)

**ANTES** de criar o checkpoint commit, a IA deve validar o tamanho das mudanças pendentes.

### Comandos de verificação preventiva

A IA deve executar automaticamente ANTES de `git add -A`:

```bash
# Contar arquivos modificados/não rastreados
git status --short | wc -l

# Estimar linhas modificadas (aproximado)
git diff --stat | tail -1
# OU se já estiver staged:
git diff --cached --stat | tail -1
```

### Limites oficiais (GUARDRAIL)

**Limites oficiais para checkpoint:**
- `CHECKPOINT_MAX_FILES` = **10 arquivos modificados**
- `CHECKPOINT_MAX_LINES` = **1000 linhas inseridas/deletadas**

**Interpretação:**
- Se mudanças pendentes **passarem** dos limites → **BLOQUEAR** checkpoint único
- Se mudanças pendentes **estiverem dentro** dos limites → permitir checkpoint único

### Comportamento obrigatório (PREVENTIVO)

**Se as mudanças pendentes passarem dos limites:**

1. **BLOQUEAR** criação de checkpoint único
2. **OBRIGAR** commit em 2 passos:
   - **Passo 1:** `git add -A` + `git commit -m "WIP snapshot: pre-[descrição]"` (mudanças em andamento)
   - **Passo 2:** `git commit --allow-empty -m "checkpoint: pre-[descrição]"` (checkpoint limpo vazio) OU aguardar novas mudanças
3. **Explicar** o motivo: "Checkpoint grande aumenta custo de rollback/review e mistura contexto"

### Exemplo de saída da IA (PREVENTIVO)

```
⚠️  GUARDRAIL ATIVADO — CHECKPOINT GRANDE PREVENIDO
   
   Mudanças pendentes detectadas:
   - Arquivos: 32 (> 10) ❌
   - Linhas: ~6417 (> 1000) ❌
   
   ⛔ CHECKPOINT ÚNICO BLOQUEADO
   
   Motivo: Checkpoint grande aumenta custo de rollback/review e mistura contexto.
   
   ✅ SOLUÇÃO OBRIGATÓRIA — Commit em 2 passos:
   
   1. Primeiro: Commitar mudanças em andamento
      git add -A
      git commit -m "WIP snapshot: pre-[descrição]"
   
   2. Depois: Criar checkpoint limpo (vazio ou com mudanças mínimas)
      git commit --allow-empty -m "checkpoint: pre-[descrição]"
      # OU aguardar novas mudanças antes do checkpoint final
   
   Execute os 2 passos e depois confirme com git status + git log -1 --oneline
```

**Nota:** Este guardrail **BLOQUEIA** checkpoint único grande. É uma proteção preventiva, não apenas aviso.

### Se as mudanças estiverem dentro dos limites

**Se mudanças pendentes estiverem dentro dos limites:**
- ✅ Permitir checkpoint único normalmente
- ✅ Prosseguir com fluxo padrão

---

## FLUXO PADRÃO (RECOMENDADO)

### 1) Garantir status limpo (ou commitar checkpoint)

**IMPORTANTE:** Antes de executar `git add -A`, a IA deve validar o tamanho das mudanças usando o **GUARDRAIL PREVENTIVO** acima.

**Se mudanças estiverem dentro dos limites (< 10 arquivos E < 1000 linhas):**

**Ação do usuário:**

```
git status
git add -A
git commit -m "checkpoint: pre-[descrição-da-mudança]"
```

**Se mudanças passarem dos limites (> 10 arquivos OU > 1000 linhas):**

**Ação obrigatória do usuário (2 passos):**

```
# Passo 1: Commitar mudanças em andamento
git status
git add -A
git commit -m "WIP snapshot: pre-[descrição-da-mudança]"

# Passo 2: Criar checkpoint limpo
git commit --allow-empty -m "checkpoint: pre-[descrição-da-mudança]"
```

**Mensagens de commit padronizadas:**

- `checkpoint: pre-architecture-review`
- `checkpoint: pre-refactoring-[módulo]`
- `checkpoint: pre-structure-reorganization`
- `checkpoint: pre-deletion-[componente]`

### 2) Criar branch dedicado

**Ação do usuário:**

```
git switch -c chore/[tipo]-[descrição]
```

**Padrões de nome de branch:**

- `chore/architecture-review`
- `chore/refactor-[módulo]`
- `chore/reorganize-[estrutura]`
- `chore/remove-[componente]`

### 3) Confirmar estado

**Ação do usuário:**

```
git status
git log -1 --oneline
```

**Resultado esperado:**

- `git status` deve mostrar: "nothing to commit, working tree clean"
- `git log -1` deve mostrar o commit de checkpoint recém-criado

---

## VALIDAÇÃO PÓS-CHECKPOINT (OBRIGATÓRIA)

Após criar o checkpoint commit, a IA deve verificar o tamanho do checkpoint criado:

### Comandos de verificação pós-commit

A IA deve executar automaticamente:

```
git diff --name-only HEAD~1 | wc -l  # Contar arquivos modificados
git diff --stat HEAD~1 | tail -1     # Contar linhas inseridas/deletadas
```

### Limites oficiais (mesmos do guardrail preventivo)

**Limites oficiais:**
- `CHECKPOINT_MAX_FILES` = 10 arquivos modificados
- `CHECKPOINT_MAX_LINES` = 1000 linhas inseridas/deletadas

### Comportamento obrigatório (pós-commit)

**Se o checkpoint criado passar dos limites:**

1. **REGISTRAR** no relatório de auditoria subsequente
2. **AVISAR** explicitamente (checkpoint já foi criado, mas é grande)
3. **DOCUMENTAR** no commit message (se ainda não foi feito): adicionar sufixo `[checkpoint grande — cuidado]`

### Exemplo de saída da IA (pós-commit)

```
⚠️  CHECKPOINT GRANDE DETECTADO (após criação)
   Arquivos: 32 (> 10)
   Linhas: 6417 (> 1000)
   
   Nota: Checkpoint já foi criado, mas é grande.
   Isso aumenta custo de rollback/review.
   
   Este aviso será registrado no relatório de auditoria subsequente.
```

**Nota:** Esta validação é **informativa** (checkpoint já foi criado). O guardrail preventivo acima é que **BLOQUEIA** checkpoint grande antes de criá-lo.

---

## CONFIRMAÇÃO OBRIGATÓRIA (HARD STOP)

A IA **DEVE** exigir que o usuário cole **SOMENTE** as saídas dos comandos:

1. Saída completa de `git status`
2. Saída de `git log -1 --oneline`

### Resposta padrão da IA (se confirmação ausente)

> ⛔ **Execução BLOQUEADA.**  
>   
> Crie o checkpoint commit e o branch dedicado, execute os comandos de confirmação e cole as saídas aqui.  
> Sem essa confirmação, nenhuma mudança estrutural será executada.

### Validação mínima

A IA deve verificar:

- ✅ `git status` mostra "working tree clean"
- ✅ `git log -1` mostra commit com mensagem iniciando com "checkpoint:" ou "WIP snapshot:"
- ✅ Branch atual corresponde ao padrão esperado (opcional, mas recomendado)
- ⚠️ Se checkpoint for grande (> 10 arquivos OU > 1000 linhas), registrar aviso explícito no relatório

---

## REGRAS (INQUEBRÁVEIS)

- ❌ A IA **não pode** executar reorganização/deleção sem este checkpoint
- ✅ O branch deve ser dedicado exclusivamente para mudanças estruturais
- ✅ Commits posteriores devem ser pequenos, atômicos e reversíveis
- ✅ Cada commit deve fazer sentido isoladamente
- ✅ Evitar commits gigantes que misturam múltiplas mudanças
- ⚠️ **GUARDRAIL PREVENTIVO:** Checkpoint único com > 10 arquivos OU > 1000 linhas é **BLOQUEADO** (obrigatório commit em 2 passos)

## ROLLBACK (DESFAZER CHECKPOINT)

Se necessário desfazer o checkpoint:

```
# Voltar para o commit anterior ao checkpoint
git reset --hard HEAD~1

# Ou voltar para o branch principal
git switch main  # ou master
git branch -D chore/[nome-do-branch]
```

**Atenção:** Rollback destrutivo. Use apenas se tiver certeza.

---

## REGRA FINAL

> **Sem checkpoint commit + branch dedicado + confirmação explícita do usuário,  
> nenhuma mudança estrutural é permitida.**

Esta regra é **absoluta** e **não negociável**.

---

=== FIM DO COMMAND checkpoint-and-branch ===
