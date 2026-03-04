---
name: decision-report
description: Relatório técnico versionado (DR_XXX) para tomada de decisão.
---

# decision-report — RELATÓRIO TÉCNICO VERSIONADO EM MARKDOWN

Versão: 1.2  
Tipo: Command  
Uso: Manual (invocado pelo usuário)  
Saída obrigatória: NOVO ARQUIVO `.md` (NUNCA sobrescrever)  
Objetivo: Análise e síntese para tomada de decisão, com histórico preservado

---

## Objetivo

Forçar a IA a **gerar um relatório técnico FORMAL**, materializado como
**arquivo Markdown versionado**, preservando **histórico completo de decisões,
análises e momentos do projeto**.

Este command existe para **evitar perda de contexto histórico** e
**impedir sobrescrita de análises anteriores**.

❌ Este command **não serve** para gerar código  
❌ Este command **não executa decisões**

---

## Diretório oficial de saída

Todos os relatórios devem ser criados em:

docs/05_decisions/reports/

yaml
Copiar código

Caso o diretório não exista, a IA deve **criá-lo**.

---

## Regra obrigatória de versionamento (CRÍTICA)

A IA **NUNCA** pode sobrescrever um relatório existente.

Cada execução do command **DEVE gerar um novo arquivo**, seguindo
**numeração sequencial crescente**, com o formato:

decision_report_001.md
decision_report_002.md
decision_report_003.md
...

yaml
Copiar código

### Regras de numeração

- A IA deve:
  1. listar os arquivos existentes no diretório
  2. identificar o maior número já utilizado
  3. criar o próximo número sequencial
- A contagem **não pode ser reiniciada**
- A ausência de arquivos implica iniciar em `001`

❌ Usar data no nome do arquivo **não substitui** a numeração  
❌ Sobrescrever arquivo existente é **falha grave de governança**

---

## Quando usar

Use `decision-report` quando quiser:

- consolidar o que foi feito até agora
- entender claramente o problema
- mapear causas e impactos
- avaliar alternativas
- registrar análises importantes
- criar memória institucional para decisões futuras

---

## Como usar

Digite o comando antes da solicitação:

decision-report

yaml
Copiar código

---

## Comportamento obrigatório da IA

Ao receber `decision-report`, a IA deve:

- criar **um novo arquivo versionado**
- **não alterar arquivos anteriores**
- **não executar código**
- **não refatorar**
- **não modificar arquitetura**
- **não assumir decisões finais**
- produzir apenas **análise técnica e síntese**

---

## Estrutura obrigatória do arquivo

Cada arquivo criado **DEVE conter exatamente** a estrutura abaixo:

```md
# Relatório Técnico para Tomada de Decisão

## 1. Contexto resumido
- o que estava sendo feito
- objetivo original

## 2. O que foi implementado
- descrição objetiva do estado atual
- sem julgamento

## 3. Problema(s) identificado(s)
- sintomas observados
- pontos de fricção
- incertezas técnicas

## 4. Possíveis causas
- causas técnicas
- causas de desenho
- causas de processo

## 5. Impactos se mantiver como está
- riscos técnicos
- riscos de manutenção
- riscos de escalabilidade ou auditoria

## 6. Alternativas possíveis
- alternativa A
- alternativa B
- alternativa C (se houver)

## 7. Prós e contras por alternativa
- análise objetiva
- sem viés decisório

## 8. Sugestão técnica (não decisória)
- recomendação argumentada
- deixar explícito que a decisão é humana

## 9. Próximo passo sugerido
- o que deve ser analisado ou decidido a seguir
Regras de escrita
Markdown válido

linguagem técnica

frases curtas

sem código

sem logs

sem emojis

texto claro, copiável e reutilizável

## Integração com governança

Após gerar o relatório, a IA deve:

sugerir explicitamente (sem executar) o uso do command:

```
update-context
```

caso o relatório represente avanço relevante, mudança de entendimento ou decisão iminente

---

## Integração com Context Engineering

Para projetos que usam Context Engineering, este command pode ser usado em conjunto com:

- **`context-audit`** — Para gerar relatório após auditoria técnica
  - Use `context-audit` para investigar e coletar evidências
  - Use `decision-report` para consolidar análise e gerar relatório formal

- **`context-feature`** — Para gerar relatório de decisão arquitetural antes de implementar feature
  - Use `decision-report` para documentar decisões técnicas importantes
  - Use `context-feature` para implementar após decisão

- **`update-context`** — Para atualizar contexto após decisão documentada
  - Use `decision-report` para documentar decisão
  - Use `update-context` para registrar no contexto do projeto

**Fluxo recomendado:**
1. `context-audit` → investigar e coletar evidências
2. `decision-report` → consolidar análise e gerar relatório
3. `update-context` → registrar decisão no contexto do projeto

---

## Exemplo de uso

```
decision-report
Analise o estado atual da conciliação entre EDI e Schedule e gere um relatório técnico versionado.
```

---

## REGRA FINAL DO COMMAND

Relatório sem histórico não é memória.  
Memória sem versionamento não é governança.  
Cada decisão deixa rastro.
