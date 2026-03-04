# Plano: [NOME_DESCRITIVO]

Fase: [N | Hotfix]
Versao: 1.0
Status: NAO INICIADO | EM ANDAMENTO | CONCLUIDO
Ultima atualizacao: YYYY-MM-DD
Pre-requisitos: [lista ou "nenhum"]

---

## Objetivo

[1-2 frases descrevendo o resultado esperado ao concluir este plano]

---

## Diagnostico / Contexto

### Para bugs/hotfixes:

**Causa raiz:**

[Descricao clara e concisa da causa raiz]

**Logs:**
```
[Trecho relevante do log de erro]
```

**Estado do banco:**

| Campo/Coluna | Valor esperado | Valor atual | Status |
|--------------|---------------|-------------|--------|
| [campo] | [esperado] | [atual] | OK/FALHA |

**Codigo afetado:**
```
Arquivo: [caminho/arquivo.py], linhas [N-M]
[trecho do codigo com o problema]
```

### Para features/refatoracoes:

**Situacao atual:**

[O que existe hoje]

**Mapa do codebase (se Fase 0 foi executada):**

[Resumo consolidado dos dominios, arquivos, dependencias]

**Regras de negocio relevantes:**

[Regras que impactam o plano]

---

## Decisoes tecnicas

| Decisao | Escolha | Justificativa |
|---------|---------|---------------|
| [decisao 1] | [escolha] | [por que] |

---

## Tarefas

### T1 -- [Nome da tarefa]

- **Complexidade:** baixa | media | alta
- **Estimativa:** ~15min | ~1h | ~2-3h
- **Arquivos:** `[caminho/arquivo1.py]`, `[caminho/arquivo2.ts]`
- **Depende de:** nenhuma
- **Conflita com:** nenhuma
- **Status:** pendente
- **Criterio de aceite:** [frase verificavel]
- **Notas para IA:**

  No arquivo `[caminho/arquivo.py]`, funcao `[nome_funcao()]`, localizar o bloco:

  ```python
  # ANTES (codigo atual):
  [codigo existente]
  ```

  **Alterar para:**

  ```python
  # DEPOIS (codigo novo):
  [codigo novo]
  ```

  **Restricoes:**
  - [restricao 1]
  - [restricao 2]

  **Regras de negocio:**
  - [regra que impacta esta tarefa]

---

### T2 -- [Nome da tarefa]

- **Complexidade:** [nivel]
- **Estimativa:** [tempo]
- **Arquivos:** `[caminho/arquivo.ext]`
- **Depende de:** T1
- **Conflita com:** nenhuma
- **Status:** pendente
- **Criterio de aceite:** [frase verificavel]
- **Notas para IA:**

  [Instrucoes detalhadas e especificas]

---

## Ordem de execucao

```
Bloco A ([descricao]):
  T1 + T2 (paralelo)

Bloco B ([descricao]):
  T3 → T4

Sequencia completa:
  [Bloco A] → [Bloco B] → verificacao final
```

---

## Mapa de conflitos de arquivo

| Arquivo | Tarefas que alteram |
|---------|---------------------|
| [caminho/arquivo.py] | T1, T3 |

Tarefas na mesma linha NAO podem rodar em paralelo.

---

## Notas para IA

1. **[Recurso/ID]:** [valor] — [para que serve]
2. **Convencao:** [padrao do projeto relevante]
3. **Restricao:** [o que NAO fazer]
4. **Deploy:** [como o deploy funciona]
5. **Skills aplicaveis:** [code-with-logs, create-subagents, etc.]

---

## Metricas de sucesso

- [Metrica verificavel 1]
- [Metrica verificavel 2]
- [Metrica verificavel 3]

---

## Rollback / Contingencia

- **Backup necessario antes:** [sim/nao — o que fazer]
- **Como reverter:** [passos de rollback]
- **Ponto de nao-retorno:** [a partir de qual tarefa nao da para reverter facilmente]
