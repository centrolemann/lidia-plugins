---
name: analisar-dados-lidia
description: Use quando uma pessoa administradora da LidIA pedir métricas ou análises do próprio produto (usuários, conversas, feedback, avaliações, projetos, eventos de MCP) ou consultas SQL diretas às bases da educação brasileira (IDEB, SAEB, Censo Escolar no BigQuery), inclusive cruzando as duas pelo município. Escolhe a família de ferramentas certa do conector `lidia-admin`, escreve SQL somente leitura e evita armadilhas de contagem, fuso horário e contas internas.
---

# Analisar dados da LidIA

O conector `lidia-admin` expõe **duas fontes de dados somente leitura**. Elas não são intercambiáveis: use a família de ferramentas de cada uma. As instruções necessárias estão nesta skill e nas descrições das ferramentas. O prompt `data_analyst_guide` e o recurso `lidia-data-mcp://guide` são documentação opcional.

As ferramentas exigem conta de administrador da LidIA. Se uma chamada responder "Esta ferramenta é exclusiva para administradores da LidIA.", repita a mensagem para a pessoa e pare. Não tente contornar. Pessoas sem autorização precisam solicitar acesso ao suporte. Contas de revisão autorizadas acessam dados sintéticos do aplicativo; identifique esses resultados como demonstração. Nas consultas do aplicativo, use nomes de tabela sem prefixo de esquema, conforme o catálogo retornado; a conexão seleciona o esquema autorizado.

## 1. Banco do aplicativo LidIA (Postgres)

Dados do produto: usuários, conversas, feedback, avaliações, projetos, documentos, eventos de auditoria do MCP e o _catálogo de metadados_ do BigQuery (`bigquery_tables`, `bigquery_columns`).

1. Comece com **`get_lidia_app_schema`**. Em uma chamada, ele traz todas as tabelas consultáveis: comentários, colunas, valores de enum, chaves estrangeiras, contagem aproximada de linhas e formatos das colunas JSONB.
2. Depois use **`query_lidia_app_data`**. Se precisar só de uma parte, use `list_lidia_app_tables`, `describe_lidia_app_tables` e `preview_lidia_app_table`.

Limites: transação somente leitura, 15 s de timeout, `rowLimit` padrão 200 e máximo 1000. SQL com erro devolve a mensagem do Postgres (`isError: true`).

- Passe `dryRun: true` para validar o SQL e ver a estimativa do planejador (`estimate.rows`, `estimate.totalCost`) sem executar.
- `approxRowCount` vem das estatísticas do planejador. Ausente significa "desconhecido", não "vazio".
- **Não** use estas ferramentas para IDEB, SAEB, matrículas ou outras estatísticas educacionais.

### Armadilhas das métricas do produto

- **Contas internas:** exclua das métricas de uso `users.role = 'admin'` e e-mails que contenham `evalharness` ou `@forgia.`, que comecem com `loadtest` ou que terminem em `@loadtest.example.com`.
- **Conversas apagadas:** `chat_sessions.is_deleted = true` é exclusão lógica. Exclua, a menos que esteja estudando exclusões.
- **Fuso horário:** os timestamps estão em UTC, mas o dia do produto é America/Sao_Paulo (UTC-3). Trunque datas nesse fuso, senão a atividade da noite cai no dia errado.
- **Ativação:** o cadastro cria automaticamente a primeira conversa, então "tem conversa" dá quase 100%. Use "ativo em 2 ou mais dias".
- **Joins multiplicam linhas:** um usuário tem muitas `chat_sessions`, cada uma muitos `chat_turns`, cada um muitos `requests_tokens`. Depois de um join, `COUNT(*)` conta linhas filhas. Use `COUNT(DISTINCT users.id)` ou agregue a tabela filha numa subconsulta antes do join.
- `bigquery_tables` e `bigquery_columns` são catálogo de metadados, não as estatísticas. `lemann_*`, `eval_*` e `agents_learned_actions` são tabelas ativas, não sobras.

## 2. Bases da educação brasileira (BigQuery `bases-cl-fl.painel_equidade`)

Tabelas de equidade da educação pública usadas pelo agente da LidIA, como `ideb_territorio`, `saeb_territorio`, `dim_territorio` e `dim_escola`.

1. **`list_brazilian_education_tables`**: lista as tabelas com o nome completo no BigQuery.
2. **`describe_brazilian_education_tables`**: metadados das colunas.
3. **`query_brazilian_education_data`**: um SELECT somente leitura em `bases-cl-fl.painel_equidade`.
4. **`preview_brazilian_education_table`**: linhas de exemplo de uma tabela, sem SQL.

Escreva sempre as tabelas como `` `bases-cl-fl.painel_equidade.<tabela>` ``. Outros projetos ou datasets, inclusive `basedosdados.*`, são rejeitados.

Limites: somente SELECT, restrito ao dataset, teto de 20 GB faturados, `rowLimit` padrão 200 e máximo 1000. SQL com erro devolve a mensagem do BigQuery (`isError: true`).

- `rowLimit` limita as linhas _retornadas_, não os bytes _lidos_. Use filtros nas partições para reduzir a leitura; `LIMIT` não garante menos bytes faturados.
- Passe `dryRun: true` para validar e ver `execution.processedBytes` sem executar. `processedBytes: 0` costuma indicar cache.
- `yearRange` pode passar do ano atual em tabelas com metas pactuadas (por exemplo, `alfabetizacao_territorio` até 2030). Nesses anos as medidas vêm NULL. `"unknown"` em tabelas `dim_*` significa "não se aplica".
- Colunas contínuas trazem `numericSummary` (min, p25, mediana, p75, max). Colunas categóricas trazem `first5UniqueValues`.
- **Não** use estas ferramentas para usuários, conversas ou feedback da LidIA.

## Cruzando as duas fontes

A única chave suportada entre as bases é o município: `users.municipality_id` no Postgres (TEXT, código IBGE de 7 dígitos) ↔ `co_municipio` no BigQuery (INT64). Consulte um lado e filtre o outro com uma lista `IN (...)`, convertendo o tipo explicitamente: `CAST(co_municipio AS STRING)` no BigQuery ou `municipality_id::bigint` no Postgres. Sem a conversão, o filtro dá erro ou não encontra nada.

## Resultados

As ferramentas de consulta devolvem um resumo de uma linha e `{ columns, rows }`, em que cada linha é um array de valores na ordem das colunas. O SQL enviado não volta na resposta, então mostre à pessoa o SQL que você usou quando isso ajudar a conferir o número.

Ao apresentar: diga de qual fonte veio cada número, os filtros aplicados (contas internas, fuso, período) e as ressalvas. Não invente valores que as consultas não retornaram.
