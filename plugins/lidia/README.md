# LidIA

A LidIA é a assistente do Centro Lemann para profissionais da educação pública brasileira. Este plugin permite consultar IDEB, SAEB, Censo Escolar, matrículas e outros indicadores educacionais, com respostas, fontes, gráficos e um link para continuar a conversa na LidIA.

## Conectar e usar

Instale este plugin e conecte uma conta da LidIA pelo login OAuth. Uma conta da LidIA é necessária; o plugin não exige perfil de administrador. Cada pergunta cria ou continua uma conversa salva nessa conta, e há um limite diário de novas conversas.

Exemplos:

- Como evoluiu o IDEB dos anos iniciais em Sobral entre 2017 e 2023?
- Compare a proficiência em matemática no SAEB entre as capitais do Nordeste.
- Quantas matrículas em tempo integral Pernambuco tinha em 2023?

A skill `consultar-dados-educacionais` orienta o assistente a enviar a pergunta com `ask_lidia`, acompanhar respostas em andamento com `get_lidia_answer` e manter o contexto nas perguntas de acompanhamento. O conector usa https://lidia.centrolemann.org.br/api/mcp/lidia. Este plugin funciona de forma independente.

Os gráficos são links de imagem PNG. Sua exibição dentro da conversa depende do assistente utilizado; o link da imagem continua disponível. Os links de gráficos expiram em sete dias.

## Dados e privacidade

A LidIA recebe a pergunta e, em acompanhamentos, o identificador da conversa. Perguntas e respostas ficam no histórico da conta até a solicitação de exclusão. O assistente conectado recebe a resposta, os links dos gráficos e o link da conversa. O login usa Clerk, o armazenamento usa Supabase, e o serviço é hospedado na Vercel. A LidIA processa perguntas e conversas com modelos da OpenAI e do Google via Vercel AI Gateway e consulta os dados educacionais no Google BigQuery. Consultas SQL geradas pela LidIA passam pela verificação Jev da TypeSafe AI via Gateway. Não envie dados pessoais sensíveis de estudantes ou profissionais.

Qualquer pessoa com um link de conversa pode copiá-la para a própria conta e continuar a análise. Compartilhe o link apenas com quem pode ver o conteúdo. A política de privacidade detalha esse comportamento e os serviços utilizados.

- [Privacidade](https://lidia.centrolemann.org.br/privacidade)
- [Termos de uso](https://lidia.centrolemann.org.br/termos)
- [Suporte e solicitação de exclusão](https://lidia.centrolemann.org.br/suporte)

Publicado pelo Centro Lemann. Licença MIT, declarada nos manifestos do plugin.
