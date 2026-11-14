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
- O código foi revisado para usar o vínculo de itens do n8n (`itemMatching`) entre a resposta do Gemini e o registro de entrada. A alteração **ainda precisa ser validada em uma execução real no n8n**; se o nó Gemini não preservar o vínculo, a execução falhará de forma explícita, evitando uma associação silenciosamente incorreta.
- O fluxo não deve ser considerado pronto para produção.

## Como reproduzir

1. Importe `workflow_n8n.json` no n8n.
2. Configure suas próprias credenciais Google Sheets e Gemini.
3. Selecione sua própria planilha e aba, com as colunas esperadas pelo fluxo.
4. Confira se a planilha contém as colunas `id`, `data`, `canal`, `texto`, `categoria_ia`, `gravidade_ia`, `causa_provavel`, `resposta_sugerida` e `precisa_escalar`. Use IDs únicos e deixe `categoria_ia` vazia nos registros pendentes.
5. Teste primeiro com **um único registro fictício** e confira se o ID e a linha atualizada correspondem à reclamação original.
6. Repita com dois ou três registros fictícios de categorias diferentes e verifique o histórico de execução, os resultados do Gemini e a atualização correta das linhas.
7. Caso o nó `Code in JavaScript` indique falha de item linking, interrompa o teste e revise o vínculo do nó Gemini antes de continuar. Não execute lotes maiores sem essa validação.

**Exemplo mínimo para testar (valores fictícios):**

| id | data | canal | texto | categoria_ia | gravidade_ia | causa_provavel | resposta_sugerida | precisa_escalar |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| TESTE-001 | 2026-11-14 | app | Minha bebida não veio no pedido. | | | | | |
| TESTE-002 | 2026-11-14 | app | A entrega está atrasada há uma hora. | | | | | |

A execução do workflow real depende de credenciais próprias e da disponibilidade/cota do modelo configurado. O JSON publicado não contém credenciais.

Não inclua tokens, segredos ou dados pessoais no repositório.

## Demonstração

A página `index.html` oferece uma **demonstração interativa sem instalação**: o visitante seleciona um dos sete casos fictícios e clica em **Executar demonstração**. O site exibe os resultados previamente registrados; **não há inferência nova, chamada ao Gemini, envio de mensagens ou escalonamento real**. A página é hospedada no GitHub Pages.

### Arquitetura para um ambiente de produção real

A experiência equivalente com **IA real em tempo de execução** exigiria uma aplicação com backend seguro, autenticação, controle de acesso e cotas, integração com o modelo de IA via servidor, validação de saídas, observabilidade, proteção de dados e revisão humana de casos sensíveis. As chaves de API não poderiam ser expostas no JavaScript do navegador. O escalonamento e o envio de respostas precisariam de integrações operacionais efetivas e confirmação de execução.

**Essa arquitetura é uma evolução proposta, não uma funcionalidade já implementada neste projeto.**
