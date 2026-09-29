---
name: consultar-dados-educacionais
description: Use quando a pessoa pedir dados, indicadores ou análises da educação pública brasileira (por exemplo IDEB, SAEB, Censo Escolar, matrículas, aprendizagem, fluxo escolar, redes municipais ou estaduais) ou pedir explicitamente para consultar a LidIA. Envia a pergunta à LidIA, acompanha a resposta e a apresenta com os gráficos e o link da conversa.
---

# Consultar dados educacionais com a LidIA

A LidIA é a assistente do Centro Lemann para profissionais da educação no Brasil. Ela consulta bases oficiais de dados educacionais, gera gráficos e guarda cada consulta como uma conversa na conta da pessoa. Use as ferramentas do conector `lidia` sempre que a pergunta depender desses dados. Não responda com números da sua própria memória.

## Fluxo

1. **Pergunte à LidIA.** Chame `ask_lidia` com a pergunta da pessoa, em português, com as palavras dela e com todo o contexto necessário: lugar (município, estado ou escola), indicador, ano ou período e rede, quando ela tiver informado. Não invente parâmetros que ela não deu, porque a LidIA pede esclarecimentos quando faltar algo importante.
2. **Acompanhe o resultado** pelo campo `status`:
   - `done`: a resposta está pronta. Siga para o passo 3.
   - `running`: a LidIA ainda está trabalhando. Espere o tempo indicado e chame `get_lidia_answer` com o mesmo `conversationId`. Repita até receber outro status. Consultas longas podem levar alguns minutos.
   - `busy`: a mensagem anterior dessa conversa ainda está em andamento. Chame `get_lidia_answer` até terminar e só então envie a nova pergunta.
   - `failed`: a LidIA não conseguiu responder. Diga isso com clareza e ofereça tentar de novo, na mesma conversa ou em uma nova.
   - `not_found`: a conversa não existe ou não pertence a essa conta. Comece uma nova com `ask_lidia` sem `conversationId`.
   - Qualquer outro erro, como um limite diário atingido: repita a mensagem da ferramenta para a pessoa, sem tentar contornar.
3. **Apresente a resposta.**
   - Resuma os achados da LidIA de forma fiel. Mantenha números, anos, fontes e ressalvas exatamente como vieram. Não acrescente dados que a LidIA não trouxe.
   - Liste sempre cada gráfico retornado, com o título e o link da imagem (`imageUrl`), mesmo quando também exibir a imagem. Em alguns ambientes, como o ChatGPT, o link é a única forma de a pessoa ver o gráfico. Se o seu ambiente conseguir exibir a imagem, exiba também.
   - Termine sempre com o link da conversa (`conversationUrl`), convidando a pessoa a continuar a análise na LidIA.
4. **Perguntas de acompanhamento** sobre o mesmo assunto ("e em 2023?", "compare com o estado"): chame `ask_lidia` com o mesmo `conversationId`, para que a LidIA use o contexto da conversa.

## Cuidados

- A LidIA responde sobre educação pública brasileira. Para outros assuntos, não use estas ferramentas.
- Não envie dados pessoais sensíveis de estudantes ou profissionais na pergunta.
- Se a pessoa escrever em outro idioma, traduza a pergunta para o português ao chamar `ask_lidia` e responda no idioma dela.
