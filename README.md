# Plugins da LidIA

Plugins oficiais da **LidIA**, a assistente do Centro Lemann para profissionais da educação pública no Brasil. Com eles, você consulta a LidIA sem sair do seu assistente de IA (Claude, ChatGPT e outros clientes compatíveis com [Agent Plugins](https://agent-plugins.org)).

Este repositório é um marketplace com dois plugins:

| Plugin                               | Para quem                              | O que faz                                                                                                                                 |
| ------------------------------------ | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| [`lidia`](plugins/lidia)             | Qualquer pessoa com conta na LidIA     | Pergunte sobre IDEB, SAEB, Censo Escolar, matrículas, aprendizagem ou fluxo escolar e receba a análise com gráficos e o link da conversa. |
| [`lidia-admin`](plugins/lidia-admin) | Requer conta de administrador da LidIA | Consultas SQL somente leitura ao banco do aplicativo e às bases da educação brasileira. Depende do plugin `lidia`.                        |

## Instalação

**Claude Code**

```
/plugin marketplace add centrolemann/lidia-plugins
/plugin install lidia@lidia-plugins
/plugin install lidia-admin@lidia-plugins   # só administradores; instala o lidia junto
```

**ChatGPT e outros clientes:** instale `lidia`. Se você for administrador, **instale os dois**, `lidia` e `lidia-admin`: nem todo cliente instala dependências automaticamente.

Ao conectar, o login é feito na própria LidIA, via OAuth. Se ainda não tiver conta, você pode criá-la nesse momento.

## Servidores e ferramentas

Esta lista espelha o que cada servidor MCP expõe. Um teste no repositório interno da LidIA compara esta lista, as skills e os manifestos com os servidores antes de cada publicação.

### `lidia`: `https://lidia.centrolemann.org.br/api/mcp/lidia`

Disponível para qualquer conta conectada.

- `ask_lidia`: envia uma pergunta à LidIA, em uma conversa nova ou em uma existente.
- `get_lidia_answer`: busca a resposta de uma conversa que ainda estava em andamento.

Skill: **`consultar-dados-educacionais`**, que ensina o modelo a enviar a pergunta, acompanhar a resposta e apresentá-la com os gráficos e o link da conversa.

### `lidia-admin`: `https://lidia.centrolemann.org.br/api/mcp/admin`

Requer conta de administrador da LidIA. Outras contas conseguem conectar, mas cada chamada responde "Esta ferramenta é exclusiva para administradores da LidIA."

Banco do aplicativo LidIA (Postgres, somente leitura):

- `get_lidia_app_schema`
- `query_lidia_app_data`
- `list_lidia_app_tables`
- `describe_lidia_app_tables`
- `preview_lidia_app_table`

Bases da educação brasileira (BigQuery, somente leitura):

- `list_brazilian_education_tables`
- `describe_brazilian_education_tables`
- `query_brazilian_education_data`
- `preview_brazilian_education_table`

Também expõe o prompt `data_analyst_guide` e o recurso `lidia-data-mcp://guide`.

Skill: **`analisar-dados-lidia`**, que orienta a escolha da fonte certa e as armadilhas das métricas.

## Como usar

1. Instale o plugin e conecte sua conta da LidIA.
2. Peça, por exemplo: _"Como evoluiu o IDEB dos anos iniciais em Sobral desde 2015?"_
3. Você recebe os achados, os gráficos e o link da conversa. Cada consulta fica salva no seu histórico da LidIA.

Gráficos: no ChatGPT, os gráficos aparecem como links para a imagem. No Claude, a imagem também pode ser exibida na conversa. Em todos os casos, a resposta lista o título e o link de cada gráfico.

## Dados enviados e recebidos

- **Enviado à LidIA:** apenas a pergunta que você fez e, em perguntas de acompanhamento, o identificador da conversa. No `lidia-admin`, também as consultas SQL. Nada é enviado a outros serviços.
- **Recebido da LidIA:** o texto da resposta, os gráficos (imagem PNG, com os dados usados) e o link da conversa; no `lidia-admin`, as linhas retornadas pelas consultas.
- As conversas ficam salvas na sua conta da LidIA, como as conversas feitas no aplicativo.
- Existe um limite diário de novas conversas por conta.

## Privacidade

A LidIA trata os dados segundo a LGPD. Guardamos as perguntas e respostas na sua conta para que você continue a conversa no aplicativo. Não vendemos dados, e as imagens de gráficos são links assinados que expiram em 7 dias. A política completa, com dados coletados, finalidades, compartilhamento, retenção e contato, está em: https://lidia.centrolemann.org.br/privacidade

## Estrutura

```
.claude-plugin/marketplace.json   # marketplace do Claude
.agents/plugins/marketplace.json  # marketplace de clientes OpenAI
plugins/<plugin>/
  plugin.json                     # Agent Plugins 1.0 (+ extensions.com.openai)
  mcp.json                        # Agent Plugins 1.0 (streamable-http)
  .claude-plugin/plugin.json      # manifesto nativo do Claude
  .mcp.json                       # MCP nativo do Claude (http)
  skills/<skill>/SKILL.md
```

## De onde vem este repositório

Este repositório é um espelho somente leitura, publicado pelo Centro Lemann, em commits de snapshot, a partir do repositório interno da LidIA junto com o código dos servidores. Pull requests aqui não são incorporados; para sugestões ou problemas, abra uma issue ou escreva para o suporte.

## Suporte

Dúvidas ou problemas: sistemas@centrolemann.org.br

## Licença

MIT. Veja [LICENSE](LICENSE).
