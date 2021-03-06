﻿
# Comandos úteis Docker

## Instalando o Docker

``curl https://get.docker.com | sudo sh``

## Configurando o proxy

#Caso tenha algum proxy na rede, é necessário criar o arquivo .conf (o Docker lê os arquivos .conf) com os parametros do proxy, pois se não existir não será possível realizar o download das imagens.

``/etc/systemd/system/docker.service.d``


## Reiniciando o serciço do Docker 

#Reiniciando o serviço :

``sudo systemctl daemon-reload``

``sudo systemctl restart docker``

## Comandos mais utilizados

#Verificando a Versão do Docker

``docker version``

#Informação sobre o Docker

``docker info``

#Comandos Docker 

``docker COMMAND --help``

#Realiza o download da imagem hello-world

``docker pull hello-world``

#Exibe as imagens baixadas

``docker images``

#Executa o container Hello-world

``docker run hello-world``

#Example com possíveis opções

``docker run [options] [image] [command] [args]``

#Comando executa o container ubuntu:14.04, com o -i força a interação com o container e -t inicia com um terminal de comando interativo.

``docker run -i -t ubuntu:14.04``


#Exibe todos os container que estão rodando, tanto em primeiro quanto em segundo plano.

``docker ps``


#Exibe todos os container's (parados e em funcionamento).

``doker ps -a``


#Remove o container( 13ea069d7c97 ID)

``docker rm 13ea069d7c97``


#Remove a imagem com hash 13ea069d7c97.

``docker rmi 13ea069d7c97`` 

 
#Executa um container que ja estava iniciado no modo interativo.

``docker exec -it 2cb0d9787c4d bash``


#Criando um container do apache configurando manualmente que o host escute na porta 8080, sendo a mesma setada para acesso externo (opção -p minúsculo).

``docker run -d -p 8080:80 httpd``


#Criando um container do apache , a opção -P significa que ele irá criar uma porta aleatóriamente.

``docker run -d -P httpd``

#Comando cria um container dando um nome para o mesmo.

``docker run -it --name NOME_QUE_QUERO debian``


#Comando que cria uma imagem do container NOME_QUE_QUERO com o nome SERVIDOR_WEB

``docker commit NOME_QUE_QUERO SERVIDOR_WEB``


#O comando docker build cria uma imagem a partir do dockerfile, o parâmetro -t 'tagueia' essa imagem com o nome brunonunes e o . significa que o arquivo do dockerfile esta no mesmo diretório.

``docker build -t brunonunes .``

#Com esse comando é possível criar a imagem setando proxy, caso na rede tenha proxy, os parâmetros são : --build-arg

``docker build --build-arg http_proxy=$http_proxy --build-arg  https_proxy=$https_proxy -t bruno/nginx:1.0 .``
 

#Comando para verificar qual porta funciona o container, no exemplo utilizamos um chamado new_web.

``docker port new_web``


#Exibirá os detalhes de configuração do container.

``docker container inspect``

#Exibirá no modelo json as configurações o volume jenkins_home. Há um campo chamado "Mountpoint" a qual exibe onde o volume esta fisicamente instalado.

``docker volume inspect jenkins_home``

#Para visualizar a senha do Admin do Jenkins quando o mesmo roda em um container Docker.

``docker exec e9a570931d22 cat /var/jenkins_home/secrets/initialAdminPassword``


#Parâmetro para setar que um container inicie  automaticamente toda vez que o Docker for iniciado.

``--restart=always``


#Criando um registry

``docker run -d -p 5000:5000 --name registry registry:2``


#Remove todas os contâiner Docker.

``docker rm $(docker ps -a -q)``

#Remove todos os volumes Docker.

`` docker volume rm $(docker volume ls -q -f dangling=true) ``

#Remove todas as images que não possuem TAG.

`` docker rmi $(docker images -q -f "dangling=true" --no-trunc) ``

#Comando para verificar os processos que estão rodando dentro do container.

``docker container top [mais id/nome do container]``

#Para e remove o container no compose

``docker-compose down --remove-orphans``


## Comunicação entre containers 

### Utiliando o LINKING , usaremos um container PHP com o banco de dados MYSQL e, por fim faremos a comunicação desses containers via LINK.

#Criando um container Docker utilizando o parâmetro -e que chama as variáveis de ambiente e seta algumas configurações, como podemos ver acima, usuario root, senha do root, nome da base de dados, usuario de acesso e senha.

``docker run -d --name database_mySQL -e MYSQL_ROOT_PASSWORD=123 -e MYSQL_DATABASE=teste -e MYSQL_USER=user -e MYSQL_PASSWORD=pass``

#O parametro --link faz, nesse caso, a comunicação entre os container da pagina WEB com o banco de dados MYQL, que tem o nome de database. O parâmetro -v conecta o diretório do host (/home/bruno/site) com o diretório padrão do PHP no container.


``docker run -d --name php_webpage --link database:db -p 9080:80 -v /home/bruno/site:/var/www/html php:5.6-apache``

### Docker Swarm (orquestrador de container's)

#Comando para iniciar o Docker Swarm

``docker swarm init``

#Comando que exibe os "nós" do cluster

``docker node ls``

#Comando que gera um token para eleição de um nó MANAGER

``docker swarm join-token manager``

#Comando para promover um node do Swarm para MANAGER

``docker node promote [name/ID]``

#Comando para despromover um node MANAGER 

``docker node demote [name/ID]``

#Comando para trazer os deetalhes do NÓ

``docker node inspect [name/ID]``

#Criando um serviço de container com balanceamento de serviços. No exemplo, utilizo 5 containers do NGINX.

``docker service create --name nginx -p 8080:80 -- replicas 5 nginx``

#Criando um serviço de container com balanceamento de serviços, e limitando o uso de CPU apenas 20% e memória apenas 64M.

``docker service create --name nginx -p 8080:80 --replicas 1 --limit-cpu 0.2 --limit-memory 64M nginx``

#Verificando o consumo de memória do container.

``docker container stats [ID/NAME]``

#Listando os container's e onde os mesmos estão rodando.

``docker service ls``

#Listando onde os container estão rodando pelo Service.

``docker service ps [nome do serviço/ID]``

#Visualizar os logs do SERVICE em real time.

``docker service logs -f nginx``


#OBS : Dentro do cluster Swarm temos dois papéis para os nós, MANAGER que é responsável pelo gerenciamento do cluster a qual verifica todos os recursos e afins. E temos também o Worker, somente executa os containers.

#OBS : O ideal para trabalhar com service é que o docker swarm esteja inicializado.             






### Utilizando o Docker Network

#Comando para criar uma rede.

``docker network create redeA``

``docker network create redeB``

#Comando para executar um container na rede especificada.

``docker run -itd --name ubuntu --network=redeA``

``docker run -itd --name alpine --network=redeB``

#Gerenciando redes Docker, exibirá as configurações da rede e os containers conectados.
#OBS : Irá também gerar configurações de rede na máquina física (ip a) .

``docker inspect redeA``

#Conectando um container que já está em execução em uma rede específica.
#OBS : Nesse caso iremos conectar o container com nome alpine2 na rede chamada redeA.

``docker network connect redeA alpine2``

#Removendo um container de uma Rede.


``docker network disconnect redeA alpine2``

#Apagando uma rede, mas para ser possível não poderá ter container conectado na mesma.

``docker network rm redeA``

#Comando para visualizar as redes criadas.

``docker network ls``

#Comando para listar os containers anexados em uma determinada rede.

``docker network inspect [nome da rede]``

#Criando um Network Alias para o container. Nesse exemplo teremos dois containers a qual vão responder pelo mesmo DNS.

``docker run -d --network=teste --network-alias alias_de_teste elasticsearch:2``
``docker run -d --network=teste --network-alias alias_de_teste elasticsearch:2``

## Criando Imagens com o Dockerfile 

### Definição : Dockerfile é um arquivo de configuração que contém as instruções para criação de uma imagem.

#### Criando o arquivo Dockerfile e adicionando as informações :

/`` 
        FROM debian
        MAINTAINER Kimbro Staken version: 0.1
        RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/           apt/lists/*
        ENV APACHE_RUN_USER www-data
        ENV APACHE_RUN_GROUP www-data
        ENV APACHE_LOG_DIR /var/log/apache2
        ADD index.html /var/www/html
        EXPOSE 80
        CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
*/``



## Volumes Docker

### Volume entre o host e o container ("Host directory as a data volume")

#O parâmetro -v indica a pasta a qual você irá mapear o diretório da máquina e após o (:) indique qual a pasta do container a qual vc quer vincular. OBS : Nessa ocasião foi usado uma imagem do apache (HTTPD) logo, o diretório que exibe o site é /usr/local/apache2/htdocs e foi vinculada ao da máquina /home/bruno/site.

``docker run -d --name web_pasta -v /home/bruno/site:/usr/local/apache2/htdocs -p 9091:80 httpd .``


### Container como volume ("Data-only container") Como mapear um volume utilizando outro container.

#Docker create cria o container mas o mesmo fica em standby. O parâmetro -v indica a pasta a qual ele vai manipular que no caso é a da aplicação WEB. O parâmetro --name é o nome dado para o seu container (datacontainer) .

``docker create -v /usr/local/apache2/htdocs --name datacontainer ubuntu``

#O parâmetro -d indica o nome do container, nesse caso podemos utilizar esse ao invés da pasta do volume.

``docker run --name web_volumes -d --volumes-from datacontainer -p 9093:80 httpd``


### Docker Volume ("Shared-storage Volume") Cria diretamente na arquitetura de sistemas de arquivos da plataforma Docker,um volume salvo e mapeado. Obs : Pode ser adicionado plugins.

#Criando um volume com o nome datastore

``docker volume create --name datastore``

#A aplicação irá salvar em volume na pasta indicada, nesse caso é o /tmp, não irá ficar persistido no container, será diretamente para o volume.

``docker run -v datastore:/tmp debian``

#Comando para manipular os volumes.

``docker volume ls``

## Configurando o proxy dentro do container 

#Configurando o HTTP , com usuário e senha e respectiva porta.

``export http_proxy=http://bmartins:minhasenha@proxylatam.indra.es:8080``

#Configurando o HTTPS

``export https_proxy=http://bmartins:minhasenha@proxylatam.indra.es:8080``

#Desfazendo as configurações do Proxy utilizando um array {}

``unset {http,https,ftp,no}_proxy``

## Link s Uteis 

https://www.icloud.com/keynote/0wCNgCKHRe9jwynm1eWhrOcQg#02_-_Docker_-_Parte_2

#Se houver entrypoint no Dockerfile e no momento de executar o container for necessário anular, então acresente :

``--entrypoint=""``


