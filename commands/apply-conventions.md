---
name: apply-conventions
description: Checklist pré-geração: logs, erros, plano, skills aplicáveis.
---

# apply-conventions — Checklist Obrigatório ANTES de Gerar Código

Versão: 2.0 (Generic)  
Tipo: Command  
Uso: Automático (a IA DEVE executar antes de gerar código executável)

---

## Objetivo

Validar e aplicar obrigatoriamente as convenções do projeto ANTES de gerar código, garantindo aderência aos padrões sem dumping de contexto.

---

## Comportamento OBRIGATÓRIO

Ao detectar criação de script executável, a IA DEVE:

### 1. CHECKLIST PRÉ-GERAÇÃO

**ANTES de gerar qualquer código, validar:**

- **Script executável (tem `main()`)?** → Aplicar `code-with-logs` obrigatoriamente
- **Script >200 linhas ou múltiplas etapas?** → Aplicar `create-subagents` obrigatoriamente
- **Logs terão timestamp `[HH:MM:SS]`?** → Obrigatório
- **Plano apresentado antes de executar?** → Obrigatório

### 2. APLICAÇÃO AUTOMÁTICA DE SKILLS

**Durante a geração, aplicar automaticamente:**

| Contexto detectado | Skill a aplicar |
|--------------------|-----------------|
| Script Python executável (`main()` ou `if __name__`) | `code-with-logs` |
| Script >200 linhas ou múltiplas etapas | `create-subagents` |
| Criação/atualização de documentação | `create-documentation` |
| Migration SQL | Padrão Validação → Aplicação → Verificação |

### 3. NUNCA GERAR CÓDIGO SEM:

- Timestamp `[HH:MM:SS]` em todos os logs importantes
- Helper de logging do projeto (se disponível) ou timestamp inline
- Subagentes se script >200 linhas ou múltiplas etapas
- Plano apresentado antes de executar

---

## Saída Esperada

Após validação, confirmar explicitamente:

```
VALIDAÇÃO PRÉ-GERAÇÃO CONCLUÍDA

Checklist:
- Script executável? → [Sim/Não] → code-with-logs [aplicado/não aplicável]
- Script >200 linhas? → [Sim/Não] → create-subagents [aplicado/não aplicável]
- Logs com timestamp? → [Sim] via [helper do projeto / inline]
- Plano apresentado? → [Sim]

Aplicando convenções...
```

---

## Regra de Ouro

**NUNCA gere código executável sem validar este checklist primeiro.**

Se detectar violação após geração:
1. Informar a violação
2. Oferecer correção imediata
3. Aplicar correção se autorizado
