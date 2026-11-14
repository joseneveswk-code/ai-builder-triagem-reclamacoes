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

## Escolha como avaliar

**Opção A — demonstração imediata, sem Gemini:** acesse https://joseneveswk-code.github.io/ai-builder-triagem-reclamacoes/ , selecione uma reclamação fictícia e clique em **Executar demonstração**. O navegador exibe os resultados registrados na avaliação inicial, sem chamadas à IA e sem enviar dados a serviços externos. É uma simulação interativa, não uma nova classificação.

**Opção B — execução técnica com Gemini:** siga as etapas abaixo para importar o workflow e executar uma classificação nova em um ambiente próprio. Essa opção exige n8n, credenciais do Google Sheets e Gemini e disponibilidade da API; a revisão de item linking do workflow publicado ainda não foi confirmada por um teste independente neste repositório.

## Como reproduzir

1. Baixe [planilha_modelo.csv](planilha_modelo.csv) e importe o CSV para uma **nova planilha Google Sheets**; mantenha os cabeçalhos na primeira linha e os IDs únicos. Não use a planilha original do autor.
3. Importe `workflow_n8n.json` no n8n.
3. Configure suas próprias credenciais Google Sheets e Gemini.
4. Selecione sua própria planilha e aba, com as colunas esperadas pelo fluxo.
5. Confira se a planilha contém as colunas `id`, `data`, `canal`, `texto`, `categoria_ia`, `gravidade_ia`, `causa_provavel`, `resposta_sugerida` e `precisa_escalar`. Use IDs únicos e deixe `categoria_ia` vazia nos registros pendentes.
6. Teste primeiro com **um único registro fictício** e confira se o ID e a linha atualizada correspondem à reclamação original.
7. Repita com dois ou três registros fictícios de categorias diferentes e verifique o histórico de execução, os resultados do Gemini e a atualização correta das linhas.
8. Caso o nó `Code in JavaScript` indique falha de item linking, interrompa o teste e revise o vínculo do nó Gemini antes de continuar. Não execute lotes maiores sem essa validação.

**Exemplo mínimo para testar (valores fictícios):**

| id | data | canal | texto | categoria_ia | gravidade_ia | causa_provavel | resposta_sugerida | precisa_escalar |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| TESTE-001 | 2026-11-14 | app | Minha bebida não veio no pedido. | | | | | |
| TESTE-002 | 2026-11-14 | app | A entrega está atrasada há uma hora. | | | | | |

**Atenção:** antes de executar, confirme que ambos os nós Google Sheets apontam para a nova planilha e a aba importada; configure `Limit` para 1 e verifique o ID escolhido. Não é necessário publicar ou ativar o workflow para executar manualmente. Os resultados do modelo podem variar e erros de cota podem impedir o teste.

A execução do workflow real depende de credenciais próprias e da disponibilidade/cota do modelo configurado. O JSON publicado não contém credenciais.

Não inclua tokens, segredos ou dados pessoais no repositório.

## Demonstração

A página `index.html` oferece uma **demonstração interativa sem instalação**: o visitante seleciona um dos sete casos fictícios e clica em **Executar demonstração**. O site exibe os resultados previamente registrados; **não há inferência nova, chamada ao Gemini, envio de mensagens ou escalonamento real**. A página é hospedada no GitHub Pages.

### Arquitetura para um ambiente de produção real

A experiência equivalente com **IA real em tempo de execução** exigiria uma aplicação com backend seguro, autenticação, controle de acesso e cotas, integração com o modelo de IA via servidor, validação de saídas, observabilidade, proteção de dados e revisão humana de casos sensíveis. As chaves de API não poderiam ser expostas no JavaScript do navegador. O escalonamento e o envio de respostas precisariam de integrações operacionais efetivas e confirmação de execução.

**Essa arquitetura é uma evolução proposta, não uma funcionalidade já implementada neste projeto.**
