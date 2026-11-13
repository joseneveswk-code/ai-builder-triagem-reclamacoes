# AI Builder — Triagem Inteligente de Reclamações

Protótipo educacional de automação com **n8n + Google Sheets + Google Gemini + JavaScript**, usando exclusivamente dados sintéticos.

## Problema e objetivo

Simular a triagem de reclamações de um marketplace de delivery: identificar assunto, gravidade, causa provável, resposta sugerida e necessidade de escalonamento humano.

## Fluxo

Gatilho manual → leitura de planilha → filtro de pendentes → limite de registros → loop → Gemini → validação em JavaScript → atualização da planilha → espera → próximo registro.

## Avaliação inicial

- Base sintética preparada: 60 registros.
- Registros com resultados avaliados: 7.
- Concordância de categoria: **6/7 (85,7%)**.
- Concordância de gravidade: **5/7 (71,4%)**.
- Acerto simultâneo: **5/7 (71,4%)**.
- Caso grave identificado e sinalizado para escalonamento: **1/1** na amostra.

As categorias do modelo e do gabarito têm nomenclaturas diferentes; a comparação foi feita por equivalência semântica. Esses indicadores descrevem apenas sete casos, não uma validação estatística ampla.

## Limitações e segurança

- Dados totalmente fictícios; não há integração com atendimento real.
- Respostas simulam ações operacionais; não representam ações efetivamente executadas.
- O modelo atingiu uma cota gratuita de requisições, interrompendo o processamento ampliado.
- O código atual recupera dados originais por índice do nó `Limit` durante um loop; essa associação deve ser revisada antes de qualquer uso em produção.
- O fluxo não deve ser considerado pronto para produção.

## Como reproduzir

1. Importe `workflow_n8n.json` no n8n.
2. Configure suas próprias credenciais Google Sheets e Gemini.
3. Selecione sua própria planilha e aba, com as colunas esperadas pelo fluxo.
4. Revise a lógica de associação de registros antes de executar em lote.
5. Execute manualmente com poucos registros fictícios e verifique a atualização da planilha.

Não inclua tokens, segredos ou dados pessoais no repositório.

## Demonstração

A página `index.html` apresenta os resultados de forma estática, sem consumir a API Gemini. Pode ser publicada pelo GitHub Pages.
