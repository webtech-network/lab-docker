# Containerizando uma Aplicação

Neste STEP você aprenderá como criar uma imagem de container para uma aplicação já existente. Para isso utilizaremos de uma aplicação escrita em TypeScript para Deno.

## STEP 5.1 - Baixando a aplicação

A aplicação está disponível em na pasta [aplicacao](aplicacao), dentro da pasta que contém este arquivo README.

Para baixá-la, clone este repositório git ou baixe o zip dos arquivos da branch main.

### Clonando o repositório

Em um terminal (ou Prompt de Comando), digite o seguinte comando:

```
git clone git@github.com:webtech-network/lab-docker.git
```

Ou

```
git clone https://github.com/webtech-network/lab-docker.git
```

### Baixando o arquivo zip da branch main

Acesse o link: https://github.com/webtech-network/lab-docker/archive/refs/heads/main.zip, baixe e extraia o arquivo ```main.zip```.

## STEP 5.2 - Executando a aplicação

Na pasta [aplicacao](aplicacao), abra um terminal (ou Prompt de Comando) e digite o seguinte comando:

```
deno run --allow-net --allow-read ./main.ts
```

Para testar o funcionamento da aplicação, em um navegador web (na mesma máquina), acesse: http://127.0.0.3:8080

Com a aplicação aberta no navegador web, no menu de navegação, clique no link 'Café', e veja a página resultante.

## STEP 5.3 - Criando o arquivo Dockerfile

### STEP 5.3.a - Método recomendado

Neste método, será demonstrado a forma recomendada de containerizarmos uma aplicação Deno.

#### STEP 5.3.a.1 - Criando novo arquivo

Dentro da pasta aplicação, crie um arquivo com o nome ```Dockerfile```.

#### STEP 5.3.a.2 - Escolhendo imagem base

Para construirmos a imagem da nossa aplicação, iremos utilizar uma imagem como base. Para isso, acessaremos o [Docker Hub](https://hub.docker.com/), e pesquisaremos por alguma imagem que possa servir como base.

Como a nossa aplicação foi desenvolvida para Deno, uma opção mais adequada é utilizarmos a [imagem oficial do Deno](https://hub.docker.com/r/denoland/deno).

Para que o Docker crie a nossa imagem com base em outra, utilizamos a instrução ```FROM```.

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM denoland/deno:2.2.8
```

No momento do desenvolvimento deste lab, a última imagem do deno era ```2.2.8```. Caso deseja utilizar a última versão, substitua ```2.2.8``` por ```latest```.

#### STEP 5.3.a.3 - Expondo portas

Para que o container Docker possa escutar por uma determinada porta, devemos expô-la. Para isso utilizaremos a instrução ```EXPOSE```.

A nossa aplicação utiliza a porta ```8080```.

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM denoland/deno:2.2.8
EXPOSE 8080
```

#### STEP 5.3.a.4 - Definindo o ambiente de trabalho

Na imagem do container Docker, quando desejamos trocar de diretório no qual iremos trabalhar, devemos utilizar a instrução ```WORKDIR```. Ele deve ser seguido do caminho absoluto do diretório a ser alternado.

No nosso exemplo, iremos trabalhar no diretório raís. Para isso, utilizaremos o comando ```WORKDIR``` seguido de ```/```.

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM denoland/deno:2.2.8
EXPOSE 8080
WORKDIR /
```

#### STEP 5.3.a.5 - Copiando os arquivos

Para podermos copiar os arquivos da nossa aplicação para dentro da imagem do container Docker, devemos utilizar os comandos ```ADD``` e/ou ```COPY```.

A instrução ```COPY``` permite copiar arquivos locais para dentro da imagem do container.

Já a instrução ```ADD``` permite adicionar arquivos tanto locais como remotos para dentro da imagem do container.

Para mais informações sobre a diferença entre essas instruções, acesse: https://docs.docker.com/build/building/best-practices/#add-or-copy

No nosso exemplo utilizaremos somente a instrução ```COPY```.

Para copiarmos todos os arquivos da pasta atual para dentro da imagem do container, o comando copy deve ser seguido de ```. .``` (pontos separados por espaço).

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM denoland/deno:2.2.8
EXPOSE 8080
WORKDIR /
COPY . .
```

#### STEP 5.3.a.6 - Definindo o comando a ser executado quando o container inicializar

Para podermos definir o comando a ser executado quando o container for inicializado, devemos utilizar a instrução ```CMD```.

Na nossa aplicação, o comando a ser executado é:

```
deno run --allow-net --allow-read ./main.ts
```

```deno``` é o nome do executável.

```run --allow-net --allow-read ./main.ts``` são os argumentos passados a ele.

Como o executável ```deno``` está localizado na pasta ```/bin```, devemos utilizar ```/bin/deno```.

E para cada argumento separado por parâmetro, devemos transformár-los em strings separadas por vírgula: ```"run", "--allow-net", "--allow-read", "/main.ts"```.

Dessa forma, a instrução ficará:

```
CMD ["/bin/deno", "run", "--allow-net", "--allow-read", "./main.ts"]
```

E o arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM denoland/deno:2.2.8
EXPOSE 8080
WORKDIR /
COPY . .
CMD ["/bin/deno", "run", "--allow-net", "--allow-read", "./main.ts"]
```

### STEP 5.3.b - Método Alternativo

Supondo que não existisse a imagem do Deno, e desejássemos containerizarmos essa aplicação. Neste método, mostraremos uma forma alternativa de containerizarmos essa aplicação.

#### STEP 5.3.b.1 - Criando novo arquivo

Nesta etapa repetiremos o que foi feito no [STEP 5.3.a.1](#step-53a1---criando-novo-arquivo).

#### STEP 5.3.b.2 - Escolhendo imagem base

Neste caso, utilizaremos a imagem base de uma distribuição Linux. Neste exemplo, utilizaremos a imagem do [Debian](https://hub.docker.com/_/debian).

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM debian:latest
```

#### STEP 5.3.b.3 - Expondo portas

Nesta etapa repetiremos o que foi feito no [STEP 5.3.a.3](#step-53a3---expondo-portas).

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM debian:latest
EXPOSE 8080
```

#### STEP 5.3.b.4 - Definindo o ambiente de trabalho

Nesta etapa repetiremos o que foi feito no [STEP 5.3.a.4](#step-53a4---definindo-o-ambiente-de-trabalho).

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM debian:latest
EXPOSE 8080
WORKDIR /
```

#### STEP 5.3.b.5 - Instalando requisitos

Para podermos instalar os requisitos, será necessário executar comandos no Docker. Para isso, a instrução ```RUN``` deve ser utilizada.

Para instalarmos o Deno, devemos executar o seguinte comando, disponível no [site oficial](https://deno.com/):

```
curl -fsSL https://deno.land/install.sh | sh
```

Esse comando depende dos comandos ```curl``` e ```unzip``` estarem instalados no sistema. Como os comandos ```curl``` e ```unzip``` não vem instalados na imagem do Debian, será necessário instalá-los por meio dos seguintes comandos:

```
apt update
apt install -y curl unzip
```

Para que esses comandos possam ser executados pelo Docker, será necessário converté-los para instruções do Docker. Os comandos serão convertidos da seguinte forma:

```apt update``` -> ```RUN apt update```

```apt install -y curl unzip``` -> ```RUN apt install -y curl unzip```

```curl -fsSL https://deno.land/install.sh | sh``` -> ```RUN bash -c "curl -fsSL https://deno.land/install.sh | sh"```

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM debian:latest
EXPOSE 8080
WORKDIR /
RUN apt update
RUN apt install -y curl unzip
RUN bash -c "curl -fsSL https://deno.land/install.sh | sh"
```

#### STEP 5.3.b.6 - Copiando os arquivos

Nesta etapa repetiremos o que foi feito no [STEP 5.3.a.5](#step-53a5---copiando-os-arquivos).

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM debian:latest
EXPOSE 8080
WORKDIR /
RUN apt update
RUN apt install -y curl unzip
RUN bash -c "curl -fsSL https://deno.land/install.sh | sh"
COPY . .
```

#### STEP 5.3.b.7 - Definindo o comando a ser executado quando o container inicializar

Nesta etapa repetiremos o que foi feito no [STEP 5.3.a.6](#step-53a6---definindo-o-comando-a-ser-executado-quando-o-container-inicializar).

O arquivo ```Dockerfile``` ficará da seguinte forma:

```
FROM debian:latest
EXPOSE 8080
WORKDIR /
RUN apt update
RUN apt install -y curl unzip
RUN bash -c "curl -fsSL https://deno.land/install.sh | sh"
COPY . .
CMD ["/root/.deno/bin/deno", "run", "--allow-net", "--allow-read", "./main.ts"]
```

### STEP 5.4 - Construindo a imagem

Para construírmos a imagem a partir do Dockerfile, na pasta onde criamos o Dockerfile, em um terminal (ou Prompt de Comando), execute o seguinte comando:

```
docker buildx build --tag exemplo .
```

E será mostrada uma saída parecida com esta:

```
[+] Building 0.1s (7/7) FINISHED                                                                                                                                                                                    docker:rootless
 => [internal] load build definition from Dockerfile                                                                                                                                                                           0.0s
 => => transferring dockerfile: 279B                                                                                                                                                                                           0.0s
 => [internal] load metadata for docker.io/denoland/deno:2.2.8                                                                                                                                                                 0.0s
 => [internal] load .dockerignore                                                                                                                                                                                              0.0s
 => => transferring context: 2B                                                                                                                                                                                                0.0s
 => [internal] load build context                                                                                                                                                                                              0.0s
 => => transferring context: 636B                                                                                                                                                                                              0.0s
 => [1/3] FROM docker.io/denoland/deno:2.2.8                                                                                                                                                                                   0.0s
 => CACHED [2/3] COPY . .                                                                                                                                                                                                      0.0s
 => exporting to image                                                                                                                                                                                                         0.0s
 => => exporting layers                                                                                                                                                                                                        0.0s
 => => writing image sha256:45e50a14890fb030423b6128a515b9ac5b9f7f9ae0273c3ef700f9de6383392f                                                                                                                                   0.0s
 => => naming to docker.io/library/exemplo 
```

### STEP 5.5 - Listando imagens

Para verificarmos que a imagem foi criada, iremos realizar a listagem de todas as imagens. Para isso, em um terminal (ou Prompt de Comando), digite o seguinte comando:

```
docker image ls
```

E será exibida uma saída parecida com esta:

```
REPOSITORY            TAG                        IMAGE ID       CREATED          SIZE
exemplo               latest                     45e50a14890f   7 minutes ago    178MB
denoland/deno         2.2.8                      964e12a68fbb   2 days ago       178MB
denoland/deno         latest                     964e12a68fbb   2 days ago       178MB
wordpress             6.7.2-php8.1-apache        70b8089f02c3   7 weeks ago      700MB
mysql                 8.0                        1c83f38450c3   2 months ago     764MB
redis                 7.4.2                      ad4b31aa2de6   3 months ago     117MB
redis                 latest                     ad4b31aa2de6   3 months ago     117MB
```

Nesta saída é possível ver todas as imagens Docker armazenadas localmente, incluindo as imagens que foram baixadas com o comando ```docker pull```.

Caso existe uma imagem com o ```REPOSITORY``` com o nome igual á ```exemplo```, isso indica que a imagem foi criada com sucesso.

### STEP 5.6 - Criando e Executando Container

Para testarmos o funcionamento da imagem de container criada, iremos criar e executar um container Docker com a imagem criada.

Para isso, em um terminal (ou Prompt de Comando), digite o seguinte comando:

```
docker run -it -p 8080:8080 exemplo
```

Para testar o funcionamento da aplicação, em um navegador web (na mesma máquina), acesse: http://127.0.0.4:8080

Para terminar a execução, digite o comando: Control + C.

### STEP 5.7 - Parando e Deletando todos os Containers

Nesta etapa, iremos parar e deletar todos os containers. Para isso, em um terminal (ou Prompt de Comando), digite os seguintes comandos:

```
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
```

A opção ```-a``` (dentro de ```$(docker ps -aq)```) mostra todos os containers, incluindo os que não estão em execução.

E a opção ```-q``` (dentro de ```$(docker ps -aq)```) exibe apenas os IDs dos containers em execução.