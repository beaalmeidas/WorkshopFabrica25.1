# 🚀 Guia de Docker para Iniciantes: Dockerizando um Projeto Django

## 📌 Introdução
Docker é uma ferramenta que permite criar ambientes isolados para aplicações, garantindo que rodem da mesma forma em qualquer máquina. Neste guia, vamos dockerizar um projeto Django do zero, criando um **Dockerfile** e um **docker-compose.yml**.

---

## 🛠️ 1. Instalando o Docker

Se ainda não tem o Docker instalado, baixe e instale:
- [Docker Desktop (Windows e macOS)](https://www.docker.com/products/docker-desktop)
- [Docker Engine (Linux)](https://docs.docker.com/engine/install/)

Para testar a instalação, rode:
```sh
docker --version
docker-compose --version
```

Se os comandos retornarem as versões instaladas, o Docker está funcionando. 🎉

---

## 📂 2. Estrutura do Projeto Django
Certifique-se de que seu projeto Django tenha esta estrutura:
```
meu_projeto/
│── app/
│── manage.py
│── meu_projeto/
│── requirements.txt
│── Dockerfile
│── docker-compose.yml
```
Se não tiver o `requirements.txt`, gere-o com:
```sh
pip freeze > requirements.txt
```

---

## 🐳 3. Criando o Dockerfile
O `Dockerfile` define como o ambiente do Django será configurado.

Crie um arquivo chamado **Dockerfile** na raiz do projeto e adicione:
```dockerfile
# Usa a imagem oficial do Python
FROM python:3.11

# Define o diretório de trabalho
WORKDIR /app

# Copia os arquivos do projeto para dentro do container
COPY . /app/

# Instala as dependências
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Expõe a porta 8000
EXPOSE 8000

# Comando para rodar o servidor Django
CMD ["gunicorn", "-b", "0.0.0.0:8000", "meu_projeto.wsgi:application"]
```
> **Substitua** `meu_projeto` pelo nome real do seu projeto Django.

---

## 🛠️ 4. Criando o `docker-compose.yml`
Esse arquivo define os serviços do projeto (Django + Banco de Dados).

Crie um arquivo **docker-compose.yml** e adicione:
```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    container_name: postgres_db
    restart: always
    environment:
      POSTGRES_DB: meu_banco
      POSTGRES_USER: usuario
      POSTGRES_PASSWORD: senha
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  web:
    build: .
    container_name: django_app
    restart: always
    depends_on:
      - db
    environment:
      DATABASE_URL: postgres://usuario:senha@db:5432/meu_banco
    ports:
      - "8000:8000"
    volumes:
      - .:/app
    command: ["gunicorn", "-b", "0.0.0.0:8000", "meu_projeto.wsgi:application"]

volumes:
  postgres_data:
```
> **Substitua** `meu_projeto` pelo nome do seu projeto Django.

---

## ⚙️ 5. Configurando o Banco de Dados no `settings.py`

No `settings.py`, altere as configurações do banco:
```python
import os
import dj_database_url

DATABASES = {
    'default': dj_database_url.config(default=os.getenv('DATABASE_URL'))
}
```
Isso faz com que o Django leia a variável `DATABASE_URL` do `docker-compose.yml`.

---

## 🚀 6. Rodando o Projeto com Docker
Agora, execute os seguintes comandos:

1️⃣ **Construir a imagem Docker:**
```sh
docker-compose build
```

2️⃣ **Subir os containers em background:**
```sh
docker-compose up -d
```

3️⃣ **Rodar as migrações do banco de dados:**
```sh
docker-compose exec web python manage.py migrate
```

4️⃣ **Criar um superusuário para acessar o Django Admin:**
```sh
docker-compose exec web python manage.py createsuperuser
```

Agora, acesse **http://localhost:8000/** e pronto! 🎉

---

## 🔄 Comandos Úteis no Docker

✅ **Verificar logs do container:**
```sh
docker-compose logs -f web
```

✅ **Parar os containers:**
```sh
docker-compose down
```

✅ **Remover containers e volumes:**
```sh
docker-compose down -v
```

---

## Guias importantes para Docker:
- https://docs.docker.com/
- https://ubsocial.github.io/posts/docker/docker.html