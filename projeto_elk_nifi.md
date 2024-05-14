# Projeto Ni-fi + ELK
Projeto  Apache Nifi + ELK -  Nesse projeto vamos coletar dados do Covid-19 de algumas fontes, tratar pelo Apache Nifi e fazer modificação e ingestão desses dados em um index Elasticsearch e os seus dados disponibilizar via Dashboard do Kibana.

Todo esse ambiente vai ser criado em Docker via Docker compose.<br>
**É muito importante que esse passo seja executado a risca para que o final, portanto:**

* O ambiente deverá ser criado em Docker
* Siga exatamente os passos desse material, deu erro? Volte reveja se o erro persistir me acione


## Pré-requisitos
Quais os pré requisitos para a criação desse ambiente:
1. Ter o Docker e o Docker compose instalado


## Auxiliando a instalação do Docker:

## Pré requisitos no S.O:
* Docker instalado
* Docker compose instalado
* Git instalado

## Instalando Pré-reqs (caso esteja usando Linux como disse acima)
Para instalar todos os pré reqs citados acima rode os comandos abaixo:

```
sudo sudo update -y
sudo sudo install docker git -y
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

Com tudo instalado validamos o docker compose:

```
docker-compose -version
```
## Docker compose
É um arquivo yml que vem com as configurações necessárias para subirmos esses dados em Docker para iniciarmos os trabalhos. Maiores detalhes sobre o conteudo dele, basta ver o vídeo.

## Baixando o repositorio do curso:
Agora vamos fazer o download dos arquivos do curso para podermos dar inicio a criação do ambiente, vamos baixar via Git:
```
git clone https://github.com/andreinsardi/aula_nifi_elk.git
```

O clone desse repositorio vai baixar a pasta nifi_elk_projeto onde contem a pastas compose, vamos entrar na pasta compose.
```
cd nifi_elk_projeto/compose
```

Assim que entramos dentro desse diretório temos um arquivo docker-compose.yml que faz as seguintes configurações pra você:
* Nifi (ultima versão) na porta 8080
* Elasticsearch (ultima versão no momento 7.7) na porta 9200
* Kibana (ultima versão no momento 7.7) na porta 5601
* Postgres 11 na porta 5432
* PGAdmin4 na porta 80

Explicando as ferramentas:
* Elasticsearch - Banco de dados NoSQL orientado a documentos, exemplo: JSON.
* Kibana - Ferramenta gráfica que gerencia o funcionamento do Elastic, serve como console do consultas no ElasticSearch e uma ferramenta poderossima de criação de dashboards e outras várias coisas.
* Postgres - Banco de dados relacional free bem consolidado no mercado a anos.
* PgAdmin4 - Ferramenta Web de consultas aos dados do Postgres.

## Rodando o docker-compose ecriando o ambiente:
Bom agora que já sabemos tudo que será instalado vamos rodar um comando para a instalação desses 5 ambientes em minutos. 
No diretório compose:
```
docker-compose up -d
```
Ele vai fazer o download das imagens dos containers e já iniciar os 5 serviços pra você e no final se tudo deu certo ele tem que mostrar a seguinte mensagem pra vc!
```
Creating postgres      ... done
Creating elasticsearch ... done
Creating nifi          ... done
Creating kibana        ... done
Creating pgadmin4      ... done
```

Se essas mensagens foram exibidas, vocês estão prontos para começar, existe outras configurações bem específicas que temos que fazer ainda mas já temos um ambiente.

## Configurando Bibliotecas de acesso ao Postgres e criação das tabelas:
Nesse passo vamos realizar a configuração da biblioteca JDBC Postgres que vai permitir com que o Nifi faça acesso aos dados do Postgres via controller service. 

## Importando o Template no Nifi:
O template está na pasta templates e vamos fazer a importação dele da seguinte forma, primeiro logamos no Nifi.
```
http://ipvm:8080
```
Agora entramos em upload template:<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/265D64CE-A3B5-4E57-8762-F432BCF6AC10_4_5005_c.jpeg)

Depois clicamos em select e depois em upload:<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/A784D852-5D09-4256-B984-98F8EFFF402B_4_5005_c.jpeg)

Agora com o template importado quando vamos no menu superior principal e arrastamos o icone do template para o Flow ele mostra a opção mapa_covid. Só dar OK e está importado.

### Jogando o jdbc dentro do container do Nifi:
No repositorio, existe uma pasta chamada libs, vamos mover o conteudo desse diretorio para dentro do container nifi com o comando abaixo, **partindo do principío que estamos na pasta nifi_elk_projeto**:
```
cd libs
sudo docker cp postgresql-42.2.11.jar nifi:/nifi/libs/
```

### Jogando o certificado dentro do container do Nifi:
No repositorio Git que baixamos, existe uma pasta chamada certificados, esse cara é um truststore do java que precisamos pra fazer download em APIs que usam HTTPS, você pode criar um seu mas já deixei ele configurado, a senha de dele é "senha123", agora vamos movimentá-lo para o container assim como o anterior:
```
cd ..
cd certificados
sudo docker cp cacerts nifi:/nifi/libs/
```

Também vamos criar a seguinte estrutura no Potgres:
* **Criar o Schema covid:** O Schema no postgres é um conjunto lógico que contem os dados como tabelas, views, procedures e etc.
* **Criar as 2 tabelas usadas no postgres** Nesse passo vamos criar a tabela covid_dados e a covid_ibge, cujo a criação dessa estrutura está no arquivo.sql que está no repositorio Git que baixamos a pouco no diretório nifi_elk_projeto/script/estrutura_postgres.sql.


### Criando a estrutura Postgres via PGAdmin4:
Vamos entrar via navegador no endereço abaixo:
```
http://ipvm
```
Feito isso a pagina do PGadmin4 vai ser aberta solicitando o login (caso não tenha alterado no compose, use os dados abaixo):
* **Usuário:** andre.insardi@espm.br
* **Senha:** root

Logue na pagina do PGAdmin4:<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/30965F19-7579-48E2-BFA9-93837D8D3C54.jpeg)

Após logado vá em servers e adicione um novo server:<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/C8675E38-8EDA-4934-B5DE-D2CDA524F324_4_5005_c.jpeg)

Feito isso você será levado a pagina inicial do PGAmin4 onde vamos cadastrar um novo servidor, entre com os dados abaixo (caso não tenha alterado no compose também):
* **Nome:** Projetao
* **Host:** postgres (pois é o nome que demos no Docker compose)
* **Usuário:** postgres
* **Senha:** root 

Agora adicione projetão no nome do server:<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/28F2073D-7284-4080-8961-B86E01E30E3A.jpeg)

Após isso colocar o host como postgres(pois é o nome do container e eles se resolvem):<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/408D3B24-6C6B-4F65-BC8A-8EA10FABBE25.jpeg)

Feito isso já temos o servidor configurado, clicamos no database e na aba lá em cima clicamos em "Query Tool" para importar e executar a nossa query.<br>
![](https://github.com/andreinsardi/nifi_elk_projeto/blob/master/passoapasso/57F3FFFE-5BDC-40D0-924E-6EA6FE1149F1.jpeg)

Importar o arquivo estrutura_postgres.sql citado acima, o mesmo se encontra no diretorio nifi_elk_projeto/scripts/

Executando o arquivo criamos as seguintes estruturas:
* Criação do schema covid
* Criação da tabela covid.covid_casos
* Criação da tabela covid.municipios

## Importando dados do arquivo CSV para a tabela covid.covid_ibge:
Agora nesse projeto temos uma única tabela estática (que não será modificada) que é a do código do IBGE com a longitude e latitude de cada município, na outra tabela vamos trazer os dados do API, o Nifi vai fazer o processo de Merge dessas 2 tabelas e o resultado vai ser um JSON com os dados da API + a Geolocalização dos municípios, possibilitando assim mostrar no Kibana qual o numero de casos por municipio em um mapa como mostrado no video do capítulo anterior:

Expandindo a estrutura do schema covid criado (dê refresh caso não exibir), vamos em tables e achamos a tabela municipios, damos botão direito e importamos a mesma do arquivo **municipios.csv** que está no diretório nifi_elk_projeto/datasets em um esquema muito parecido como o script que rodamos acima.

Feito isso nossa parte do Postgres já está pronta.

## Criando o indice no Elasticsearch:
O indice no Elaticsearch funciona como um database em um banco de dados convencional. Por padrão o Elaticsearch cria o indice se você não possui um, mas do jeito dele, as vezes ele não faz o mapping com os datatypes corretos. Sendo assim vamos deixar a criação no esquema.

### Entrando no Kibana e deixando o Script no dev_tools:
O Dev_tools é a ferramenta onde rodamos as queries do Elasticsearch, vamos pegar o script create_mapping.es no diretorio scripts e já deixar na tela pronto pra rodar. Para entrar no Kibana basta entrar no link abaixo e no dev_tools o icone da chavinha na lateral esquerda.
```
http://ipvm:5601
```
## Apresentando como funciona a API do Brasil.io
A API do Brasil.io disponibiliza dados sobre o Covid diáriamente, coletando através de robos, informações em sites da secretaria de saúde da cada municipio sobre obitos e novos casos de Covid 19.

O endereço da API é a seguinte:
```
https://brasil.io/api/dataset/covid19/caso/data/
```
Nessa API tem exatamente os mesmos campos da tabela do Postgres, covid_casos, mas para usar um exemplo didático, o dado vem no formato chave valor, não é um formato com fizemos o CSV e que é quase um padrão do mercado de dados. Por isso vamos tratar esse cara de documentos para linhas (NoSQL para SQL/Não relacional para Relacional).

Outro ponto de atenção é que pra fins didáticos, estou usando um processor pra cada page do API, mas é possivel usar um processor que faz o count das paginas pra mim e eu use um só, caso queira fazer isso como desafio, fique a vontade.

O desenho de como vai ficar o processo é mais ou menos o seguinte:<br>
![](https://github.com/AnselmoBorges/nifi_elk_projeto/blob/master/passoapasso/IMG_0171.PNG)

Fontes:
* Dados da API
* Tabela municipios do Postgres

Tratamento:
* Conversão dos dados do API para tabela do Postgres
* Start de Join criando consolidada via NiFI no Postgres
* Transformação de tabela para Documentos no Elasticsearch enriquecida com GeoLocalização.

Preparação e visualização:
* Mapping dos dados no Elasticsearch para visualização.
* Exibição dos dados em dashboards do Kibana.
