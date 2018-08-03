# Docker básico

## Conceitos básicos

### Imagem

Pacote pré-pronto que contém a aplicação e todas as suas dependências (como libs) para que possa ser executada.

Normalmente fica armazenada na máquina local, mas não faz nada se não for executada. Quando executada, gera pelo menos um container.

### Container

Gerada a partir da imagem, é onde o conteúdo da imagem e suas dependências rodam.

### Dockerfile

Arquivo de configuração que gera uma imagem. Nele é obrigatório definir uma imagem remota ou local e opcionalmente outras configurações, como comandos a serem executados após a inicialização do container, arquivos a serem copiados para dentro do container a ser criado, etc.

Exemplo de Dockerfile:

```dockerfile
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Para mais informações, ver documentação.

## Comandos úteis

### `docker image`

Gerencia imagens do docker, onde é possível listar (`ls`), baixar (`pull`) e remover (`rm`), entre outros.

Para mais informações, execute `docker image -h` .

### `docker build`

Cria uma imagem a partir de um `Dockerfile`.

Normalmente é utilizado como:

```sh
docker build -t name /path/to/Dockerfile

# Exemplo
docker build -t my-python-app .
```

### `docker run`

Após a build da imagem, é possível criar um container dessa imagem com o comando `run`.

```sh
docker run -p host-port:container-port image-name

# Exemplo
docker run -p 4000:80 my-python-app
```

O comando acima rodará a imagem `my-python-app`, mapeando a porta `80` do container para a porta `4000` do host. Assim seria possível acessar essa aplicação através da URL `localhost:4000`.
