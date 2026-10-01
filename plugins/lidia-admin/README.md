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

## Dados e privacidade

Uma pessoa administradora pode receber linhas do banco do aplicativo, inclusive dados pessoais autorizados; esses resultados são compartilhados com o assistente conectado. Não solicite dados pessoais desnecessários. A conta de revisão recebe apenas dados sintéticos do aplicativo, identificados como demonstração.

O texto SQL e seu dialeto, inclusive valores literais escritos na consulta, são enviados ao Jev da TypeSafe AI via Vercel AI Gateway para verificar efeitos antes da execução. Essa verificação não envia as linhas retornadas pelo banco. A auditoria das ferramentas omite o SQL bruto e registra metadados técnicos. A política de privacidade detalha os operadores, finalidades e retenção.

- [Privacidade](https://lidia.centrolemann.org.br/privacidade)
- [Termos de uso](https://lidia.centrolemann.org.br/termos)
- [Suporte e solicitação de exclusão](https://lidia.centrolemann.org.br/suporte)

Publicado pelo Centro Lemann. Licença MIT, declarada nos manifestos do plugin.
