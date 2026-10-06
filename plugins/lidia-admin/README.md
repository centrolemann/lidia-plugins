# LidIA Admin

O LidIA Admin permite análises somente leitura do banco do aplicativo LidIA e das bases da educação pública brasileira. Foi criado pelo Centro Lemann para pessoas administradoras da LidIA e funciona de forma independente do plugin LidIA.

## Conectar e usar

Instale este plugin e conecte uma conta da LidIA pelo login OAuth. As ferramentas exigem perfil de administrador: outras contas podem conectar, mas recebem uma mensagem de acesso restrito ao chamar ferramentas. Contas de revisão explicitamente autorizadas acessam somente dados sintéticos do aplicativo em um esquema isolado.

A skill `analisar-dados-lidia` orienta o assistente a escolher a fonte adequada e escrever SQL somente leitura. O conector usa https://lidia.centrolemann.org.br/api/mcp/admin.

Exemplos:

- Liste as tabelas educacionais, descreva ideb_territorio e consulte uma amostra de 2023.
- Mostre o esquema do banco do aplicativo antes de fazer uma consulta.
- Conte as conversas não excluídas do aplicativo. Na conta de demonstração, o resultado usa somente as tabelas sintéticas.

As ferramentas do aplicativo consultam PostgreSQL; as ferramentas educacionais consultam exclusivamente o dataset BigQuery `bases-cl-fl.painel_equidade`. Consultas de escrita e consultas a outros datasets são rejeitadas. Os resultados têm limites de linhas, e o assistente deve apresentar os filtros e a fonte de cada número.

Quando um gráfico ajudar a análise e houver ferramentas de visualização disponíveis, a skill orienta o assistente a gerar o gráfico com as linhas retornadas antes de explicar os achados. Sem essas ferramentas, ele apresenta uma tabela ou um resumo dos dados. As consultas SQL não criam uma conversa na LidIA nem retornam um link de conversa.

## Se o conector pedir autenticação

Instalar o plugin não conecta sua conta automaticamente. Use a opção Conectar/Autenticar do assistente e conclua o login e consentimento no navegador. Se não tiver conta, [crie uma na LidIA](https://lidia.centrolemann.org.br/sign-up); se já tiver, [entre com seu login](https://lidia.centrolemann.org.br/sign-in). Depois volte ao assistente e autorize o conector `lidia-admin`.

- **ChatGPT web/desktop:** conecte a conta nos detalhes do plugin ou no cartão de autenticação do chat.
- **Codex:** consulte `/mcp` ou `codex mcp list` e execute `codex mcp login <nome-do-servidor>` usando o nome efetivo mostrado, inclusive um possível prefixo do plugin.
- **Claude Code:** use `/mcp` para autenticar o servidor ou `claude mcp login <nome-do-servidor>`.
- **Claude web/desktop:** em Customize → Connectors, clique Connect. Na configuração, selecione “Sign in when needed” para solicitar login quando necessário. Em organizações, uma pessoa administradora disponibiliza o conector e cada pessoa conecta a própria conta.

Uma mensagem de cadastro incompleto pede que você conclua o cadastro na LidIA com o mesmo login. Uma mensagem de acesso exclusivo para administradores pede autorização do [suporte](https://lidia.centrolemann.org.br/suporte), não outro login. Nunca compartilhe senhas, códigos de login ou tokens no chat. Se as ferramentas não aparecerem sem uma mensagem de autenticação, verifique também a instalação e a ativação do conector.

## Dados e privacidade

Uma pessoa administradora pode receber linhas do banco do aplicativo, inclusive dados pessoais autorizados; esses resultados são compartilhados com o assistente conectado. Não solicite dados pessoais desnecessários. A conta de revisão recebe apenas dados sintéticos do aplicativo, identificados como demonstração.

O texto SQL e seu dialeto, inclusive valores literais escritos na consulta, são enviados ao Jev da TypeSafe AI via Vercel AI Gateway para verificar efeitos antes da execução. Essa verificação não envia as linhas retornadas pelo banco. A auditoria das ferramentas omite o SQL bruto e registra metadados técnicos. A política de privacidade detalha os operadores, finalidades e retenção.

- [Privacidade](https://lidia.centrolemann.org.br/privacidade)
- [Termos de uso](https://lidia.centrolemann.org.br/termos)
- [Suporte e solicitação de exclusão](https://lidia.centrolemann.org.br/suporte)

Publicado pelo Centro Lemann. Licença MIT, declarada nos manifestos do plugin.
