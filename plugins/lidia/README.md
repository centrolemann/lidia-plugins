# LidIA

A LidIA é a assistente do Centro Lemann para profissionais da educação pública brasileira. Este plugin permite consultar IDEB, SAEB, Censo Escolar, matrículas e outros indicadores educacionais, com respostas, fontes, gráficos e um link para continuar a conversa na LidIA.

## Conectar e usar

Instale este plugin e conecte uma conta da LidIA pelo login OAuth. Uma conta da LidIA é necessária; o plugin não exige perfil de administrador. Cada pergunta cria ou continua uma conversa salva nessa conta, e há um limite diário de novas conversas.

Exemplos:

- Como evoluiu o IDEB dos anos iniciais em Sobral entre 2017 e 2023?
- Compare a proficiência em matemática no SAEB entre as capitais do Nordeste.
- Quantas matrículas em tempo integral Pernambuco tinha em 2023?

A skill `consultar-dados-educacionais` orienta o assistente a enviar a pergunta com `ask_lidia`, acompanhar respostas em andamento com `get_lidia_answer` e manter o contexto nas perguntas de acompanhamento. O conector usa https://lidia.centrolemann.org.br/api/mcp/lidia. Este plugin funciona de forma independente.

O assistente só reutiliza identificadores retornados pelas ferramentas da LidIA neste chat e ainda disponíveis no contexto. Sem um identificador, começa uma nova conversa com o contexto disponível, sem pedir um link ou ID à pessoa.

As respostas incluem imagens PNG e os dados dos gráficos. Quando houver ferramentas de visualização disponíveis, a skill orienta o assistente a recriar os gráficos relevantes com esses dados antes de explicar os achados e mostrar o link da conversa. Quando isso não for possível, ele pode exibir a imagem recebida ou oferecer seu link. O título e o link de cada gráfico continuam disponíveis; os links de imagem expiram em sete dias.

## Se o conector pedir autenticação

Instalar o plugin não conecta sua conta automaticamente. Use a opção Conectar/Autenticar do assistente e conclua o login e consentimento no navegador. Se não tiver conta, [crie uma na LidIA](https://lidia.centrolemann.org.br/sign-up); se já tiver, [entre com seu login](https://lidia.centrolemann.org.br/sign-in). Depois volte ao assistente e autorize o conector `lidia`.

- **ChatGPT web/desktop:** conecte a conta nos detalhes do plugin ou no cartão de autenticação do chat.
- **Codex:** consulte `/mcp` ou `codex mcp list` e execute `codex mcp login <nome-do-servidor>` usando o nome efetivo mostrado, inclusive um possível prefixo do plugin.
- **Claude Code:** use `/mcp` para autenticar o servidor ou `claude mcp login <nome-do-servidor>`.
- **Claude web/desktop:** em Customize → Connectors, clique Connect. Na configuração, selecione “Sign in when needed” para solicitar login quando necessário. Em organizações, uma pessoa administradora disponibiliza o conector e cada pessoa conecta a própria conta.

Uma mensagem de cadastro incompleto pede que você conclua o cadastro na LidIA com o mesmo login. Uma mensagem de acesso exclusivo para administradores pede autorização do [suporte](https://lidia.centrolemann.org.br/suporte), não outro login. Nunca compartilhe senhas, códigos de login ou tokens no chat. Se as ferramentas não aparecerem sem uma mensagem de autenticação, verifique também a instalação e a ativação do conector.

## Dados e privacidade

A LidIA recebe a pergunta e, em acompanhamentos, o identificador da conversa. Perguntas e respostas ficam no histórico da conta até a solicitação de exclusão. O assistente conectado recebe a resposta, as imagens e os dados dos gráficos, seus links e o link da conversa. O login usa Clerk, o armazenamento usa Supabase, e o serviço é hospedado na Vercel. A LidIA processa perguntas e conversas com modelos da OpenAI e do Google via Vercel AI Gateway e consulta os dados educacionais no Google BigQuery. Consultas SQL geradas pela LidIA passam pela verificação Jev da TypeSafe AI via Gateway. Não envie dados pessoais sensíveis de estudantes ou profissionais.

Qualquer pessoa com um link de conversa pode copiá-la para a própria conta e continuar a análise. Compartilhe o link apenas com quem pode ver o conteúdo. A política de privacidade detalha esse comportamento e os serviços utilizados.

- [Privacidade](https://lidia.centrolemann.org.br/privacidade)
- [Termos de uso](https://lidia.centrolemann.org.br/termos)
- [Suporte e solicitação de exclusão](https://lidia.centrolemann.org.br/suporte)

Publicado pelo Centro Lemann. Licença MIT, declarada nos manifestos do plugin.
