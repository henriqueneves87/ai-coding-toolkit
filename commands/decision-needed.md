---
name: decision-needed
description: Apresenta trade-offs e força decisão humana documentada.
---

# decision-needed — DECISAO TECNICA NECESSARIA

Versão: 1.0  
Tipo: Command  
Uso: Manual (invocado pelo usuário)

## Quando usar

- trade-offs técnicos
- escolhas arquiteturais
- decisões irreversíveis
- comparações de abordagem
- dúvidas entre alternativas válidas

## Como usar

Digite o comando antes da solicitação:

decision-needed

## Comportamento obrigatório da IA

- listar claramente as opções possíveis
- explicar prós e contras de cada opção
- indicar riscos técnicos e impactos futuros
- **não decidir sozinha**
- aguardar escolha explícita do usuário
- após a escolha:
  - registrar a decisão
  - indicar impacto em documentação e roadmap

## Formato esperado da resposta

- opções numeradas
- comparação objetiva
- recomendação técnica **sem executar**
- pergunta final solicitando decisão do usuário

## Exemplo de uso

decision-needed  
Devemos normalizar os dados no banco ou no backend?
