# 🐳 Introdução ao Docker

Docker é uma plataforma que permite criar, implantar e executar aplicativos em contêineres. Os contêineres são ambientes isolados e portáteis que incluem tudo o que um aplicativo precisa para rodar.

Este tutorial cobre os conceitos básicos e os comandos essenciais do Docker.

---

## 📥 Instalação do Docker no Linux

No Linux, o Docker pode ser instalado via repositórios oficiais. Primeiro, atualize o sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

Em seguida, instale o Docker:

```bash
sudo apt install docker.io -y
```

Para verificar se a instalação foi bem-sucedida:

```bash
docker --version
```

Agora, inicie o serviço do Docker e o adicione ao boot:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Opcionalmente, adicione seu usuário ao grupo do Docker para rodar comandos sem `sudo`:

```bash
sudo usermod -aG docker $USER
```

Após isso, saia e entre novamente no sistema.

---

## 📦 Trabalhando com Imagens no Docker

### 🔍 Como procurar imagens no Docker Hub

O [Docker Hub](https://hub.docker.com/) é um repositório onde você pode encontrar diversas imagens prontas.

Para buscar imagens diretamente no terminal:

```bash
docker search nome-da-imagem
```

Exemplo:

```bash
docker search ubuntu
```

### 📥 Baixando uma imagem

O comando abaixo baixa uma imagem do Docker Hub para o seu sistema:

```bash
docker pull hello-world
```

Para listar todas as imagens baixadas:

```bash
docker images
```

---

## 🚀 Executando Containers

- Rodar um container de teste:

  ```bash
  docker run hello-world
  ```

- Rodar um container interativo:

  ```bash
  docker run -dit ubuntu
  ```

  O `-d` executa em segundo plano, `-i` mantém a entrada ativa e `-t` aloca um terminal.

- Nomear um container:

  ```bash
  docker run -dit --name meu-container ubuntu
  ```

- Listar containers em execução:

  ```bash
  docker ps
  ```

- Listar todos os containers (incluindo os parados):

  ```bash
  docker ps -a
  ```

- Parar um container:

  ```bash
  docker stop ID_CONTAINER
  ```

- Iniciar um container parado:

  ```bash
  docker start ID_CONTAINER
  ```

- Remover um container:

  ```bash
  docker rm ID_CONTAINER
  ```

- Remover uma imagem:

  ```bash
  docker rmi nome-imagem
  ```

- Remover todos os containers parados:

  ```bash
  docker container prune
  ```

- Inspecionar um container:

  ```bash
  docker inspect nome-container
  ```

- Ver logs de um container:

  ```bash
  docker logs nome-container
  ```

- Acessar o terminal de um container em execução:

  ```bash
  docker exec -it nome-container /bin/bash
  ```

- Parar todos os containers em execução:

  ```bash
  docker stop $(docker ps -q)
  ```

- Remover todos os containers:

  ```bash
  docker rm $(docker ps -a -q)
  ```

- Remover todas as imagens:

  ```bash
  docker rmi $(docker images -q)
  ```

- Remover todos os volumes não utilizados:

  ```bash
  docker volume prune
  ```

---

## 📂 Manipulação de Arquivos

Copiar um arquivo do host para dentro de um container:

```bash
docker cp arquivo.txt nome-container:/destino
```

Executar comandos dentro de um container em execução:

```bash
docker exec nome-container ls
```

---

## 🗄️ Exemplo: Rodando um Banco de Dados MySQL

1. Baixar a imagem do MySQL:

   ```bash
   docker pull mysql
   ```

2. Rodar um container com MySQL:

   ```bash
   docker run -e MYSQL_ROOT_PASSWORD=senha123 --name container-mysql -d -p 3306:3306 mysql
   ```

3. Acessar o terminal do container:

   ```bash
   docker exec -it container-mysql bash
   ```

4. Dentro do container, acessar o MySQL:

   ```bash
   mysql -u root -p --protocol=tcp
   ```

5. Criar um banco de dados:

   ```sql
   CREATE DATABASE meu_banco;
   ```

6. Para acessar o banco de fora do container, obtenha o IP do container:

   ```bash
   docker inspect container-mysql | grep "IPAddress"
   ```

7. Instale um cliente MySQL no host:

   ```bash
   apt -y install mysql-client
   ```

Agora, você pode usar o IP do container para se conectar ao banco pelo DBeaver ou outro cliente SQL.

---

## 📁 Volumes e Montagem de Diretórios

Volumes permitem armazenar dados persistentes fora dos containers.

- Criar um volume nomeado:

  ```bash
  docker volume create meu-volume
  ```

- Usar um volume dentro de um container:

  ```bash
  docker run -dti --mount source=meu-volume,target=/dados ubuntu
  ```

- Usar bind mount para mapear uma pasta do host:

  ```bash
  docker run -dti --mount type=bind,src=/caminho/host,dst=/caminho/container ubuntu
  ```

- O parâmetro `ro` torna a pasta somente leitura:

  ```bash
  docker run -dti --mount type=bind,src=/caminho/host,dst=/caminho/container,readonly ubuntu
  ```

---

## 🛠️ Criação de Imagens com Dockerfile

Um Dockerfile é um script de texto que contém uma série de instruções para construir uma imagem Docker. Aqui está um exemplo básico de como criar uma imagem Docker:

1. Crie um arquivo chamado `Dockerfile` e adicione o seguinte conteúdo:

    ```dockerfile
    FROM ubuntu
    RUN apt update && apt install -y python3 nano && apt clean
    COPY arquivo-teste.py /pasta/destino/container/arquivo-teste.py
    CMD python3 /pasta/destino/container/arquivo-teste.py
    ```

    - `FROM ubuntu`: Define a imagem base como Ubuntu.
    - `RUN apt update && apt install -y python3 nano && apt clean`: Atualiza os pacotes e instala Python3 e Nano.
    - `COPY arquivo-teste.py /pasta/destino/container/arquivo-teste.py`: Copia o arquivo `arquivo-teste.py` do host para o container.
    - `CMD python3 /pasta/destino/container/arquivo-teste.py`: Define o comando padrão a ser executado quando o container iniciar.

2. Para construir a imagem, execute o seguinte comando no diretório onde o Dockerfile está localizado:

    ```bash
    docker build . -t imagem-container
    ```

    - `.`: Indica o diretório atual.
    - `-t imagem-container`: Nomeia a imagem como `imagem-container`.

---

## 🏗️ Criação de Imagens Multistage com Dockerfile

Imagens multistage permitem criar imagens Docker mais eficientes, reduzindo o tamanho final da imagem ao copiar apenas os artefatos necessários da fase de construção.

1. Crie um arquivo chamado `Dockerfile` e adicione o seguinte conteúdo:

    ```dockerfile
    # Fase de construção
    FROM golang:1.16 AS builder
    WORKDIR /app
    COPY . .
    RUN go build -o meu-app

    # Fase final
    FROM alpine:latest
    WORKDIR /root/
    COPY --from=builder /app/meu-app .
    CMD ["./meu-app"]
    ```

    - `FROM golang:1.16 AS builder`: Define a fase de construção usando a imagem Golang.
    - `WORKDIR /app`: Define o diretório de trabalho.
    - `COPY . .`: Copia todos os arquivos do diretório atual para o container.
    - `RUN go build -o meu-app`: Compila o aplicativo Go.
    - `FROM alpine:latest`: Define a fase final usando a imagem Alpine.
    - `COPY --from=builder /app/meu-app .`: Copia o binário compilado da fase de construção para a fase final.
    - `CMD ["./meu-app"]`: Define o comando padrão a ser executado quando o container iniciar.

2. Para construir a imagem, execute o seguinte comando no diretório onde o Dockerfile está localizado:

    ```bash
    docker build . -t meu-app
    ```

---

## 🛠️ Como Instalar o Docker Compose

Docker Compose é uma ferramenta para definir e gerenciar aplicativos Docker multi-container. Para instalar o Docker Compose:

1. Baixe a versão mais recente do Docker Compose:

    ```bash
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```

2. Aplique permissões executáveis ao binário:

    ```bash
    sudo chmod +x /usr/local/bin/docker-compose
    ```

3. Verifique a instalação:

    ```bash
    docker-compose --version
    ```

---

## 📝 Como Criar um Arquivo YAML para Docker Compose

Um arquivo YAML define os serviços, redes e volumes para um aplicativo Docker Compose. Aqui está um exemplo básico:

1. Crie um arquivo chamado `docker-compose.yml` e adicione o seguinte conteúdo:

    ```yaml
    version: '3'
    services:
      web:
        image: nginx
        ports:
          - "80:80"
      db:
        image: mysql
        environment:
          MYSQL_ROOT_PASSWORD: exemplo
    ```

    - `version: '3'`: Define a versão do Docker Compose.
    - `services`: Define os serviços do aplicativo.
    - `web`: Serviço web usando a imagem Nginx.
    - `ports`: Mapeia a porta 80 do host para a porta 80 do container.
    - `db`: Serviço de banco de dados usando a imagem MySQL.
    - `environment`: Define variáveis de ambiente para o serviço de banco de dados.

2. Para iniciar os serviços definidos no arquivo YAML, execute:

    ```bash
    docker-compose up
    ```

---

## 🌐 O que é Docker Swarm

Docker Swarm é uma ferramenta de orquestração de containers que permite gerenciar um cluster de nodes Docker como um único sistema. Com Docker Swarm, você pode implantar, gerenciar e escalar aplicativos em containers de forma fácil e eficiente.

### Comandos Básicos do Docker Swarm

1. Inicializar um Swarm:

    ```bash
    docker swarm init
    ```

2. Adicionar um node ao Swarm:

    ```bash
    docker swarm join --token <token> <manager-ip>:<manager-port>
    ```

3. Listar nodes no Swarm:

    ```bash
    docker node ls
    ```

4. Criar um serviço no Swarm:

    ```bash
    docker service create --name meu-servico -p 80:80 nginx
    ```

5. Listar serviços no Swarm:

    ```bash
    docker service ls
    ```

6. Escalar um serviço no Swarm:

    ```bash
    docker service scale meu-servico=3
    ```

7. Remover um serviço do Swarm:

    ```bash
    docker service rm meu-servico
    ```

---

## 🛠️ Configuração e Monitoramento

- Ver informações gerais sobre o Docker:
  
  ```bash
  docker info
  ```

- Exibir estatísticas de uso de CPU e memória dos containers:
  
  ```bash
  docker stats
  ```

- Inspecionar detalhes de um container:
  
  ```bash
  docker inspect nome-container
  ```

- Atualizar configurações de um container em execução:
  
  ```bash
  docker update nome-container -m 128m --cpus 0.3
  ```

---

## 🌐 Gerenciamento de Redes

- Listar redes disponíveis:

  ```bash
  docker network ls
  ```

- Criar uma nova rede:

  ```bash
  docker network create minha-rede
  ```

- Conectar um container a uma rede específica:

  ```bash
  docker network connect minha-rede nome-container
  ```

- Desconectar um container de uma rede:

  ```bash
  docker network disconnect minha-rede nome-container
  ```

---

## 📌 Resumo dos Comandos

| Comando | Descrição |
|---------|-------------|
| `docker search nome-imagem` | Busca imagens no Docker Hub |
| `docker pull nome-imagem` | Baixa uma imagem |
| `docker images` | Lista imagens disponíveis |
| `docker run nome-imagem` | Executa um container |
| `docker ps` | Lista containers em execução |
| `docker ps -a` | Lista todos os containers |
| `docker stop ID_CONTAINER` | Para um container |
| `docker start ID_CONTAINER` | Inicia um container parado |
| `docker rm ID_CONTAINER` | Remove um container |
| `docker rmi nome-imagem` | Remove uma imagem |
| `docker container prune` | Remove todos os containers parados |
| `docker cp arquivo.txt nome-container:/destino` | Copia arquivos para o container |
| `docker exec nome-container ls` | Executa comandos dentro do container |
| `docker volume create meu-volume` | Cria um volume nomeado |
| `docker run -dti --mount type=bind,src=/host,dst=/container` | Monta um diretório do host no container |
| `docker logs nome-container` | Exibe logs do container |
| `docker top nome-container` | Mostra processos dentro do container |
| `docker stats` | Monitora uso de CPU e memória |
| `docker update nome-container -m 128m --cpus 0.3` | Atualiza limites de recursos do container |
| `docker info` | Exibe informações gerais do Docker |
| `docker inspect nome-container` | Mostra detalhes de um container |
| `docker network ls` | Lista redes do Docker |
| `docker network connect minha-rede nome-container` | Conecta um container a uma rede |
| `docker network disconnect minha-rede nome-container` | Desconecta um container de uma rede |

---

Com isso, você tem um guia básico para começar a usar o Docker! 🚀
