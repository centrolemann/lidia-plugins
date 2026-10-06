---
name: consultar-dados-educacionais
description: Use quando a pessoa pedir dados, indicadores ou análises da educação pública brasileira (por exemplo IDEB, SAEB, Censo Escolar, matrículas, aprendizagem, fluxo escolar, redes municipais ou estaduais) ou pedir explicitamente para consultar a LidIA. Envia a pergunta à LidIA, acompanha a resposta e a apresenta com os gráficos e o link da conversa.
---

# Consultar dados educacionais com a LidIA

A LidIA é a assistente do Centro Lemann para profissionais da educação no Brasil. Ela consulta bases oficiais de dados educacionais, gera gráficos e guarda cada consulta como uma conversa na conta da pessoa. Use as ferramentas do conector `lidia` sempre que a pergunta depender desses dados. Não responda com números da sua própria memória.

## Autenticação e conexão

Este conector exige uma conta LidIA conectada por OAuth. Instalar o plugin ou entrar no site não autoriza o conector automaticamente.

- Se houver `authentication_required`, HTTP 401, uma solicitação de autenticação do host ou uma falha explícita de credenciais, diga que o conector `lidia` precisa ser autenticado. Use a opção nativa Conectar/Autenticar quando disponível e explique que a pessoa deve concluir o login e consentimento no navegador.
- **ChatGPT web/desktop:** use Conectar/Autenticar no plugin. Se o cartão de conexão não aparecer no chat, abra os detalhes do plugin e conecte a conta.
- **Codex:** veja o nome efetivo do servidor em `/mcp` ou `codex mcp list`; ele pode ter um prefixo do plugin. Use `codex mcp login <nome-do-servidor>` com esse nome, ou a opção de autenticação do plugin. Não invente o nome registrado.
- **Claude Code:** abra `/mcp`, selecione o servidor e autentique; alternativamente, use `claude mcp login <nome-do-servidor>` com o nome efetivo mostrado pelo host.
- **Claude web/desktop:** abra Customize → Connectors, encontre o conector e clique Connect. Na configuração, a opção “Sign in when needed” permite solicitar login quando necessário. Em uma organização, uma pessoa administradora precisa disponibilizar o conector primeiro; cada pessoa conecta a própria conta.
- Se não houver conta, ofereça [criar uma conta LidIA](https://lidia.centrolemann.org.br/sign-up). Para uma conta existente, ofereça [entrar na LidIA](https://lidia.centrolemann.org.br/sign-in). Depois, a pessoa deve voltar e autorizar o conector neste assistente. Nunca peça senha, código de login ou token no chat.
- Se houver `account_setup_required` ou “Sua conta LidIA ainda não está pronta”, a autenticação já ocorreu: peça para concluir o cadastro no site com o mesmo login. Se persistir, indique [suporte](https://lidia.centrolemann.org.br/suporte). Não repita OAuth em um ciclo.
- Se houver `admin_required` ou acesso exclusivo para administradores, indique o suporte para solicitar essa permissão. Criar uma conta ou entrar novamente não concede perfil de administrador.
- Ferramentas ausentes ou um erro genérico, sem evidência de autenticação, significam um problema de conexão: explique que é preciso verificar a instalação, a ativação do conector e o login. Não afirme que a pessoa está desconectada nem que o serviço está fora do ar sem evidência.
- Aguarde a conclusão da autenticação e retome o pedido original usando as ferramentas. Não substitua a consulta por números da memória, não contorne permissões e não repita chamadas rejeitadas antes de resolver o acesso.

## Fluxo

1. **Pergunte à LidIA.** Chame `ask_lidia` com a pergunta da pessoa, em português, com as palavras dela e com todo o contexto necessário: lugar (município, estado ou escola), indicador, ano ou período e rede, quando ela tiver informado. Não invente parâmetros que ela não deu, porque a LidIA pede esclarecimentos quando faltar algo importante.
   - Use `conversationId` somente quando ele já tiver sido retornado por uma ferramenta da LidIA neste chat, ainda estiver disponível no contexto e corresponder ao assunto da pergunta. Nunca invente um identificador nem reutilize identificadores de outros chats.
   - Sem esse identificador, chame `ask_lidia` omitindo o campo `conversationId`, para criar uma conversa nesta conta. Inclua na pergunta o contexto educacional disponível neste chat. Não peça à pessoa um link ou ID para começar, mesmo que ela mencione uma conversa anterior.
   - Se a mensagem trouxer várias consultas, faça as novas consultas antes de resolver os acompanhamentos com os identificadores retornados. A referência a uma conversa anterior ou em andamento não deve bloquear as novas consultas.
2. **Acompanhe o resultado.** Chame `get_lidia_answer` somente com um identificador retornado neste chat e disponível no contexto. Sem ele, consulte novamente com `ask_lidia` sem `conversationId`, usando a pergunta educacional disponível. Se houver apenas um pedido de status e nenhuma pergunta no contexto, peça a pergunta educacional, nunca um link ou ID. Siga o campo `status`:
   - `done`: a resposta está pronta. Siga para o passo 3.
   - `running`: a LidIA ainda está trabalhando. Espere o tempo indicado e chame `get_lidia_answer` com o mesmo `conversationId`. Repita até receber outro status. Consultas longas podem levar alguns minutos.
   - `busy`: a mensagem anterior dessa conversa ainda está em andamento. Chame `get_lidia_answer` até terminar e só então envie a nova pergunta.
   - `failed`: a LidIA não conseguiu responder. Diga isso com clareza e ofereça tentar de novo, na mesma conversa ou em uma nova.
   - `not_found`: a conversa não existe ou não pertence a essa conta. Comece uma nova com `ask_lidia` sem `conversationId`.
   - Qualquer outro erro, como um limite diário atingido: repita a mensagem da ferramenta para a pessoa, sem tentar contornar.
3. **Mostre os dados em um gráfico quando isso ajudar.** Se o seu ambiente tiver ferramentas de visualização, tente recriar os gráficos relevantes para a pergunta usando `structuredContent.charts`: `rows` contém os valores, `xKey` identifica o eixo horizontal, `yKeys` identifica as séries e `type` indica o tipo de gráfico. Preserve títulos, rótulos, anos, unidades e valores; mantenha dados ausentes como ausentes. Use apenas os dados retornados, sem estimar números pelos pixels da imagem nem completar séries com valores inventados.
   - Se não houver ferramenta de visualização, faltarem dados suficientes ou a geração falhar, exiba a imagem recebida quando possível e ofereça seu link. Continue com a explicação, sem bloquear a resposta.
   - Liste sempre cada gráfico retornado com o título e o link da imagem (`imageUrl`), inclusive quando tiver recriado o gráfico com suas próprias ferramentas. Em alguns ambientes, como o ChatGPT, o link é a única forma de a pessoa ver o gráfico.
4. **Explique os achados.** Depois da visualização, resuma os achados da LidIA de forma fiel. Mantenha números, anos, fontes e ressalvas exatamente como vieram. Não acrescente dados que a LidIA não trouxe.
5. **Mostre o link da conversa.** Termine com `conversationUrl`, convidando a pessoa a continuar a análise na LidIA. Use o link retornado pela ferramenta.
6. **Perguntas de acompanhamento** sobre o mesmo assunto ("e em 2023?", "compare com o estado"): reutilize o `conversationId` somente nas condições do passo 1. Se ele não estiver disponível, chame `ask_lidia` sem `conversationId`, incluindo o contexto da pergunta e do acompanhamento para começar uma nova conversa.

## Cuidados

- A LidIA responde sobre educação pública brasileira. Para outros assuntos, não use estas ferramentas.
- Não envie dados pessoais sensíveis de estudantes ou profissionais na pergunta.
- Se a pessoa escrever em outro idioma, traduza a pergunta para o português ao chamar `ask_lidia` e responda no idioma dela.
