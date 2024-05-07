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
