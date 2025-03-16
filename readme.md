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

O Docker Hub (https://hub.docker.com/) é um repositório onde você pode encontrar diversas imagens prontas.

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

---

Com isso, você tem um guia básico para começar a usar o Docker! 🚀
