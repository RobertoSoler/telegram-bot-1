<h1 align='center'>Pipeline de Dados do Telegram e Análise de Mensagens</h1>

## Conteúdo

- [1. Introdução](#1-introdução)
  - [1.1. Contexto](#11-contexto)
  - [2.1. Descrição do Projeto](#21-descrição-do-projeto)
- [3. Telegram](#3-telegram)
  - [3.1. Grupos de Telegram](#31-grupos-de-telegram)
  - [3.2. ChatBots](#32-chatbots)
- [4. Criação e Configuração do Bot](#4-criação-e-configuração-do-bot)
  - [4.1. Adição do Bot ao Grupo](#41-adição-do-bot-ao-grupo)
- [5. Estrutura](#5-estrutura)
  - [5.1. Coleta de Mensagens via Webhook](#51-coleta-de-mensagens-via-webhook)
  - [5.2. Armazenamento das Mensagens em Arquivo Raw](#52-armazenamento-das-mensagens-em-arquivo-raw)
  - [5.3. Tratamento dos Dados com Funções Lambda da AWS](#53-tratamento-dos-dados-com-funções-lambda-da-aws)
  - [5.4. Análise das Mensagens](#54-análise-das-mensagens)
- [6. Implementação](#6-implementação)
  - [6.1. ETL - Extration, Transformation \& Loading](#61-etl---extration-transformation--loading)
  - [6.2. Datalake](#62-datalake)
  - [6.3. AWS S3](#63-aws-s3)
  - [6.4. AWS Lambda](#64-aws-lambda)
  - [6.4.1. Função de Ingestão](#641-função-de-ingestão)
  - [6.4.2. Funções de Wrangling](#642-funções-de-wrangling)
  - [6.5. AWS API Gateway](#65-aws-api-gateway)
  - [6.6. AWS Athena](#66-aws-athena)
- [Análise das Mensagens](#análise-das-mensagens)


# 1. Introdução

## 1.1. Contexto

Os grupos de Telegram são uma ferramenta poderosa para as empresas que desejam estreitar o relacionamento com seus consumidores, proporcionando um canal de comunicação direto, engajado e eficiente. Eles permitem que as empresas criem uma comunidade em torno de sua marca, aumentem a fidelidade dos clientes e obtenham ***insights*** valiosos para melhorar seus produtos e serviços.
Uma marca de moda pode criar um grupo de Telegram onde compartilha dicas de estilo, notícias sobre lançamentos de coleções, e oferece descontos exclusivos. Os membros do grupo podem discutir tendências, pedir opiniões e até mesmo sugerir ideias para novos produtos. Essa interação constante não só fortalece a lealdade à marca, mas também transforma os consumidores em defensores ativos da marca.

## 2.1. Descrição do Projeto
Este repositório contém uma aplicação que realiza a captura e análise de mensagens de um grupo de Telegram. A aplicação funciona da seguinte maneira:
- **Captura de Mensagens**: As mensagens enviadas em um grupo de Telegram são capturadas diariamente utilizando a API do Telegram.
- **Armazenamento de Dados**: As mensagens capturadas são armazenadas em arquivos JSON e posteriormente carregadas em um armazenamento em nuvem utilizando AWS S3.
- **Análise de Mensagens**: Após o armazenamento, as mensagens passam por uma análise de sentimento, identificação de menções a produtos da empresa e detecção de intenção de compra.
- **Processamento em Batch**: A análise e o processamento dos dados são realizados diariamente em lotes, utilizando funções Lambda da AWS para automatizar as tarefas.

Essa aplicação seria ideal para empresas que desejam monitorar e analisar a interação de consumidores em grupos de Telegram, obtendo insights valiosos sobre o sentimento geral, interesse em produtos e possíveis intenções de compra.

# 3. Telegram
![`telegram`](./img/telegram-icone-icon.png)

O Telegram é uma plataforma de mensagens instantâneas que é distribuída gratuitamente (freeware) e, em grande parte, ***open source***. Além de ser amplamente popular entre desenvolvedores, o Telegram destaca-se por sua segurança robusta, oferecendo criptografia ponta a ponta em conversas secretas e opções avançadas de privacidade. Ele foi pioneiro na introdução de chatbots, que possibilitam a criação de diversas automações, desde a gestão de comunidades até a integração com outras ferramentas e serviços. O Telegram também suporta canais para transmissões em massa, grupos com até centenas de milhares de membros, e uma vasta gama de personalizações através de APIs abertas e stickers animados.

## 3.1. Grupos de Telegram

Grupos no Telegram são espaços de conversa que permitem a interação de várias pessoas ao mesmo tempo, funcionando como um chat coletivo. Eles podem ter desde poucos membros até centenas de milhares, sendo ideais para comunidades, equipes de trabalho ou grupos de interesse comum. Dentro dos grupos, é possível compartilhar mensagens, arquivos, links, e muito mais. Além disso, o Telegram oferece recursos avançados, como a moderação por administradores, respostas em threads e menções, tornando os grupos uma ferramenta poderosa para comunicação em larga escala.

## 3.2. ChatBots

Um chatbot é um programa de software que simula conversas humanas, permitindo a interação automatizada com usuários através de texto ou voz. Esses bots são amplamente usados em plataformas de mensagens, como o Telegram, para fornecer respostas automáticas, executar tarefas repetitivas, ou mesmo oferecer serviços personalizados, como atendimento ao cliente, envio de notificações, e processamento de pedidos. Graças à integração com APIs e inteligência artificial, os chatbots podem ser programados para realizar uma ampla variedade de funções de forma eficiente e em tempo real.
   - **Integração com Serviços Externos:** Bots no Telegram podem ser integrados a APIs de terceiros ou serviços externos para obter informações em tempo real, como resultados de esportes, cotações de mercado, ou previsões meteorológicas, e compartilhar esses dados no grupo.
   - **Webhook:** O bot pode usar webhooks para receber notificações automáticas de eventos externos e agir conforme necessário. Isso pode incluir a publicação de mensagens ou a execução de ações no grupo.

# 4. Criação e Configuração do Bot

Um bot para administrar um grupo no Telegram funciona como um assistente automatizado que pode ajudar a moderar, gerenciar e interagir com os membros do grupo. Esses bots são programados para executar várias tarefas, desde automatizar respostas a perguntas frequentes até banir usuários que violam regras. Abaixo, uma explicação detalhada sobre como um bot funciona nesse contexto:

   - **Criação do Bot:** Para criar um bot no Telegram, você precisa usar o `@BotFather`, que é o bot oficial do Telegram para criar e gerenciar bots. Com ele, você pode configurar o nome, o username e obter um token de API que será usado para se comunicar com o bot.
   - **Configuração Inicial:** Após criar o bot, você configura as permissões e funcionalidades desejadas. Isso pode incluir definir o escopo do bot, como ele interage com usuários e grupos, e se ele pode visualizar ou enviar mensagens.

## 4.1. Adição do Bot ao Grupo
   - **Adicionar ao Grupo:** O bot deve ser adicionado como um membro do grupo que ele administrará. Dependendo das permissões concedidas ao bot, ele pode realizar várias funções administrativas.
   - **Configuração de Permissões:** Os administradores do grupo podem definir as permissões do bot, como a capacidade de deletar mensagens, banir usuários, fixar mensagens, e outras tarefas administrativas.

# 5. Estrutura

Descrição do Processo de Coleta e Análise de Mensagens do Grupo de Telegram

## 5.1. Coleta de Mensagens via Webhook
   - **Configuração do Webhook:** Um ``webhook`` será configurado para capturar as mensagens enviadas no grupo de Telegram. Esse webhook estará integrado à API do Telegram, permitindo que ele colete as mensagens trocadas no grupo.
   - **Captura Diária das Mensagens:** Em vez de capturar as mensagens em tempo real, a coleta será realizada diariamente. Todas as mensagens trocadas no grupo durante o dia serão capturadas em formato ``JSON`` e convertidas e em seguidas armazenadas, conterá tanto o conteúdo das mensagens quanto os metadados associados (como remetente, timestamp e ID da mensagem).

## 5.2. Armazenamento das Mensagens em Arquivo Raw
   - **Armazenamento em Nuvem (AWS S3):** As mensagens coletadas diariamente serão armazenadas na nuvem utilizando o Amazon S3. Inicialmente, elas serão salvas em arquivos raw no formato `Apache Parquet`. Cada arquivo diário será armazenado com uma estrutura de diretórios que facilita a organização e o acesso, como `/raw/telegram/{data}/mensagens.parquet`.
   - **Organização dos Arquivos Raw:** Os arquivos parquet serão organizados de acordo com a data da coleta, permitindo fácil recuperação e processamento subsequente. Essa organização facilita a busca por dados específicos e garante que a estrutura seja escalável à medida que o volume de dados aumenta.

## 5.3. Tratamento dos Dados com Funções Lambda da AWS
   - **Ativação Automática de Funções Lambda:** Após o armazenamento do arquivo diário, funções Lambda da AWS serão automaticamente ativadas para processar os dados. Essas funções são responsáveis por realizar o tratamento das mensagens, que inclui:
     - **Limpeza de Dados:** Remoção de mensagens irrelevantes, como mensagens automáticas ou spam, e padronização de texto (normalização).
     - **Enriquecimento:** Enriquecimento dos dados, incluindo a análise de sentimento, identificação de menções a produtos da empresa e detecção de intenções de compra. Essas informações adicionais são adicionadas como novos campos nos registros.
   - **Criação de Arquivos Enriched (Formato Parquet):** Após o tratamento, as mensagens enriquecidas são armazenadas em arquivos também no formato parquet. Esses arquivos são mais compactos e otimizados para análises posteriores. Eles são salvos em um diretório separado, como `/enriched/telegram/{data}/mensagens_enriched.parquet`.

## 5.4. Análise das Mensagens
   - **Análise de Sentimento:** Com os dados já enriquecidos, cada mensagem é analisada para determinar o sentimento (positivo, negativo ou neutro). Essa análise ajuda a medir o sentimento geral do grupo.
   - **Identificação de Menções de Produtos:** O sistema identifica e analisa menções específicas a produtos da empresa, permitindo uma compreensão mais profunda de como os produtos estão sendo discutidos e percebidos pelos membros do grupo.
   - **Detecção de Intenção de Compra:** Algoritmos de processamento de linguagem natural (NLP) detectam mensagens que indicam uma intenção de compra, marcando-as para que possam ser revisadas e usadas em estratégias de marketing e vendas.

# 6. Implementação

## 6.1. ETL - Extration, Transformation & Loading
<p align="left"><img src='./img/eb.png' alt='AWS' widht=50 height=50></p>

Uma mensagem recuperada via API é um dado semi-estruturado no formato JSON com algumas chaves mandatórias e diversas chaves opcionais, estas últimas presentes (ou não) dependendo do tipo da mensagem. Por exemplo, mensagens de texto apresentam a chave `text` enquanto mensagens de áudio apresentam a chave `audio`. Neste projeto vamos focar em mensagens do tipo texto, ou seja, vamos ingerir as chaves mandatórias e a chave `text`.

>[!NOTE]
>A lista completa das chaves disponíveis pode ser encontrada na documentação neste [link](https://core.telegram.org/bots/api#message).

A etapa de **extração, transformação e carregamento** (do inglês *extraction, transformation and load* ou **ETL**) é uma etapa abrangente responsável pela manipulação dos dados ingeridos de sistemas transacionais, ou seja, já persistidos em camadas cruas ou *raw* de sistemas analíticos. Os processos conduzidos nesta etapa variam bastante de acordo com a área da empresa, do volume/variedade/velocidade do dado consumido, etc. Contudo, em geral, o dado cru ingerido passa por um processo recorrente de *data wrangling* onde o dado é limpo, deduplicado, etc. e persistido com técnicas de particionamento, orientação a coluna e compressão. Por fim, o dado processado está pronto para ser analisado por profissionais de dados.

A etapa de **ingestão** é responsável, pela ingestão dos dados transacionais em ambientes analíticos. De maneira geral, o dado ingerido é persistido no formato mais próximo do original, ou seja, nenhuma transformação é realizada em seu conteúdo ou estrutura (*schema*). Como exemplo, dados de uma API *web* que segue o formato REST (*representational state transfer*) são entregues, logo, persistidos, no formato JSON.

> [!NOTE]
> Persistir os dados em seu formato original trás muitas vantagens, como a possibilidade de reprocessamento.

## 6.2. Datalake

Um **data lake** é um repositório centralizado que permite armazenar grandes volumes de dados em seu formato bruto (raw), de forma estruturada, semiestruturada ou não estruturada. Ele é projetado para lidar com grandes quantidades de dados de diferentes fontes e tipos, oferecendo flexibilidade para armazenar tudo, desde dados transacionais até logs, arquivos de mídia e streams de sensores.

**Benefícios de Ter Arquivos Raw e Enriched em um Data Lake:**
   - **Flexibilidade:** Mantendo os dados raw, você preserva a capacidade de revisitar e reinterpretar os dados originais à medida que surgem novas necessidades de negócios ou técnicas.
   - **Eficiência:** Dados enriched permitem análises mais rápidas e eficazes, já que os dados estão pré-processados e prontos para consumo.
   - **Confiabilidade:** Separar os dados raw dos enriched garante que os dados originais permaneçam intocados, enquanto os dados processados podem ser otimizados para desempenho e facilidade de uso.
   - **Versatilidade:** Um data lake com camadas de dados raw e enriched suporta uma ampla variedade de casos de uso, desde simples queries SQL até complexos modelos de machine learning.

Dados `raw` (brutos) são os dados que são armazenados no data lake exatamente como foram recebidos das fontes de dados, sem qualquer processamento, limpeza ou transformação.
   - **Propósito:** Manter uma cópia fiel e imutável dos dados originais. Isso garante que, se forem necessárias novas análises ou correções futuras, você sempre possa voltar aos dados originais.

Dados `enriched` (enriquecidos) são os dados que passaram por algum nível de processamento, limpeza, transformação ou agregação. Eles são derivados dos dados raw e preparados para análises específicas, relatórios ou outras finalidades.
   - **Propósito:** Fornecer dados prontos para uso em análises, `machine learning`, relatórios ou dashboards. O processamento pode incluir a remoção de valores nulos, normalização, agregação, junção de dados de diferentes fontes, entre outras transformações.

No projeto, as mensagens capturadas pelo *bot* podem ser ingeridas através da API *web* de *bots* do **Telegram**, portanto são fornecidos no formato JSON. Como o **Telegram** retem mensagens por apenas 24h em seus servidores, a ingestão via **streaming** é a mais indicada. Para que seja possível esse tipo de **ingestão** seja possível, vamos utilizar um *webhook* (gancho *web*), ou seja, vamos redirecionar as mensagens automaticamente para outra API *web*.

## 6.3. AWS S3
<p align="left"><img src='./img/s3.png' alt='AWS' widht=50 height=50></p>

Na etapa de **ingestão**, o `AWS S3` tem a função de passivamente armazenar as mensagens captadas pelo *bot* do **Telegram** no seu formato original: JSON. Para tanto, basta a criação de um *bucket*. Como padrão, vamos adicionar o sufixo `-raw` ao seu nome (vamos seguir esse padrão para todos os serviços desta camada).

Para isso, vamos utilizar uma função do `AWS Lambda` como motor de processamento e um *bucket* do `AWS S3` como camada enriquecida para a persistência do dado processado. Para garantir a recorrência, vamos configurar uma regra do `AWS Event Bridge` como gatilho diáro da função.

O Amazon S3 (Simple Storage Service) é um serviço de armazenamento em nuvem oferecido pela Amazon Web Services (AWS). Ele é projetado para armazenar e recuperar qualquer quantidade de dados a qualquer momento, de qualquer lugar na web.

**Buckets:** O Amazon S3 organiza os dados em contêineres chamados `buckets`. Um b`bucket` é um repositório que armazena objetos (arquivos) e permite que se gerencie o acesso e as configurações de armazenamento.

## 6.4. AWS Lambda
<p align="left"><img src='./img/lbd_logo.png' alt='AWS' widht=50 height=50></p>

As funções **Lambda** da AWS são parte do serviço **AWS Lambda**, que permite executar código sem a necessidade de provisionar ou gerenciar servidores. Esse serviço se enquadra no modelo de computação "serverless", onde a infraestrutura é completamente gerenciada pela AWS, e o usuário paga apenas pelo tempo de execução do código

**Execução de Código Sob Demanda**
   - **Eventos:** As funções Lambda são disparadas em resposta a eventos. Esses eventos podem ser gerados por outros serviços da AWS, como S3, DynamoDB, Kinesis, SNS, ou diretamente por chamadas HTTP através do Amazon API Gateway.
   - **Execução Automatizada:** Quando o evento ocorre, a AWS Lambda invoca automaticamente a função correspondente, executa o código e retorna os resultados.

   - **Eventos Gatilho:** Lambda pode ser facilmente integrado com outros serviços da AWS, permitindo que eventos nesses serviços acionem funções Lambda. Por exemplo, você pode usar Lambda para processar arquivos carregados em um bucket S3, responder a atualizações de um banco de dados DynamoDB, ou como backend de uma API gerenciada pelo API Gateway.

## 6.4.1. Função de Ingestão
Na etapa de **ingestão**, o `AWS Lambda` tem a função de ativamente persistir as mensagens captadas pelo *bot* do **Telegram** em um *bucket* do `AWS S3`. Para tanto vamos criar uma função que opera da seguinte forma:

 - Recebe a mensagem no parâmetro `event`;
 - Verifica se a mensagem tem origem no grupo do **Telegram** correto;
 - Persiste a mensagem no formato JSON no *bucket* do `AWS S3`;
 - Retorna uma mensagem de sucesso (código de retorno HTTP igual a 200) a API de *bots* do **Telegram**.

Esta função está disponivel neste [arquivo](function_lambda_event-caption.py).

## 6.4.2. Funções de Wrangling
Na etapa de **ETL**, o `AWS Lambda` tem a função de ativamente processar as mensagens captadas pelo *bot* do **Telegram**, persistidas na camada cru no *bucket* do `AWS S3`, e persisti-las na camada enriquecida, também em um *bucket* do `AWS S3`. Logo, vamos criar uma função que opera da seguinte forma:

 - Lista todos os arquivos JSON de uma única participação da camada crua de um *bucket* do `AWS S3`;
 - Para cada arquivo listado:
  - Faz o *download* do arquivo e carrega o conteúdo da mensagem;
  - Executa uma função de *data wrangling*;
  - Cria uma tabela do PyArrow e a contatena com as demais.
 - Persiste a tabela no formato Parquet na camada enriquecida em um *bucket* do `AWS S3`.

Esta função está disponivel neste [arquivo](function_lambda_data-enrich.py).

A função `parse_data` tem como objetivo processar um dicionário de dados (no caso, mensagem de um chat no Telegram) e extrair informações específicas para retorná-las em um novo dicionário. A função também adiciona campos para assegurar que certas informações estejam presentes, mesmo que não tenham sido encontradas no dicionário original.

A função `parse_data`  está disponivel neste [arquivo](function_lambda_parse-data.py).

> [!IMPORTANT]
> Note que o código exige a configuração de duas variáveis de ambiente: `AWS_S3_BUCKET` com o nome do *bucket* do `AWS S3` e `TELEGRAM_CHAT_ID` com o id do *chat* do grupo do **Telegram**. Para adicionar variáveis de ambiente em uma função do `AWS Lambda`, basta acessar configurações -> variáveis de ambiente no console da função.

## 6.5. AWS API Gateway
<p align="left"><img src='./img/gateway.png' alt='AWS' widht=50 height=50></p>

Precisamos de um serviço da AWS que forneça um API *web* para receber os dados redirecionados, o `AWS API Gateway` (documentação neste [link](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/welcome.html)). Dentre suas diversas funcionalidades, o `AWS API Gateway` permite o redirecionamento do dado recebido para outros serviços da AWS. Logo, vamos conecta-lo ao `AWS Lambda`, que pode sua vez, irá armazenar o dado em seu formato original (JSON) em um *bucket* do `AWS S3`.

Na etapa de **ingestão**, o `AWS API Gateway` tem a função de receber as mensagens captadas pelo *bot* do **Telegram**, enviadas via *webhook*, e iniciar uma função do `AWS Lambda`, passando o conteúdo da mensagem no seu parâmetro *event*. Para tanto vamos criar uma API e configurá-la como gatilho da função do `AWS Lambda`

## 6.6. AWS Athena
<p align="left"><img src='./img/athena.png' alt='AWS' widht=50 height=50></p>

Precedendo a aetapa de **análise**, o `AWS Athena` tem função de entregar o dados através de uma interface SQL para os usuários do sistema analítico. Para criar a interface, basta criar uma tabela externa sobre o dado armazenado na camada mais refinada da arquitetura, a camada enriquecida.

``` sql
CREATE EXTERNAL TABLE `telegram`(
  `message_id` bigint, 
  `user_id` bigint, 
  `user_is_bot` boolean, 
  `user_first_name` string, 
  `chat_id` bigint, 
  `chat_type` string, 
  `text` string, 
  `date` bigint)
PARTITIONED BY ( 
  `context_date` date)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://<bucket-enriquecido>/'
```
# Análise das Mensagens
<p align="left"><img src='./img/gears.png' alt='AWS' widht=50 height=50></p>

O processo de análise das mensagens armazenadas esta sendo proposto num ambiente de tratamento de dados utilizando linguagem Python e bibliotecas auxiliares. Portanto, a continuação deste trabalho foi desenvolvida num `notebook` da plataforma [Kaggle](https://www.kaggle.com/). Este é o `notebook` desta aplicação: [**Telegram Message Analysis - AI**](https://www.kaggle.com/code/rmsoler/telegram-message-analysis-ai).