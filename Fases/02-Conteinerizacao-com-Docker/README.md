# Fase 2: Containerização com Docker

Este documento detalha o processo de containerização da aplicação FastAPI e sua publicação no Docker Hub.

## 📋 Visão Geral

Nesta fase, a aplicação será empacotada em um container Docker, permitindo:
- Execução consistente em diferentes ambientes
- Facilidade de deployment
- Versionamento de imagens
- Distribuição através do Docker Hub

> [!IMPORTANT]
> Embora tenha sido recomendado usar o nome `fastapi-hello:latest`,  optei por fazer o push e pull com outro nome.

## 🎯 Objetivos

- [X] Criar Dockerfile para a aplicação
- [X] Realizar build da imagem Docker
- [X] Publicar imagem no Docker Hub
- [X] Versionar Dockerfile no repositório GitHub

## 🛠️ Pré-requisitos

Antes de começar, certifique-se de ter instalado:
- Docker
- Docker Compose
- Conta no Docker Hub

### Validação do Ambiente
```bash
docker --version
docker-compose --version
```

![img01](../../assets/fase02/img01.png)

## 📁 Estrutura do Projeto

```
projeto/
├── backend/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── requirements.txt
│   ├── main.py
│   └── ...
├── frontend/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── package.json
│   ├── src/
│   └── ...
└── docker-compose.yml
```

## 🐳 Containerização

### 1. Criação do Dockerfile no Backend

**Localização:** Dentro da pasta `backend` crie um `Dockerfile`

```dockerfile
FROM python:3.10-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 2. Criação do Dockerfile no Frontend

**Localização:** Dentro da pasta `frontend` crie um `Dockerfile`

```dockerfile
FROM node:18-alpine as build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 3000

# Configuração customizada do nginx para porta 3000
RUN echo 'server { \
    listen 3000; \
    server_name localhost; \
    location / { \
        root /usr/share/nginx/html; \
        index index.html index.htm; \
        try_files $uri $uri/ /index.html; \
    } \
}' > /etc/nginx/conf.d/default.conf

CMD ["nginx", "-g", "daemon off;"]
```

### 3. Criação do Docker Compose

**Localização:** Na pasta raiz do projeto (anterior ao backend e frontend), crie o `docker-compose.yml`

```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    container_name: docker-frontend
    restart: unless-stopped
    networks:
      - app-network

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    container_name: docker-backend
    restart: unless-stopped
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```

### 4. Build da Imagem

Usando docker-compose (na pasta raiz):

```bash
docker-compose build
```

![img02](../../assets/fase02/img02.png)

Verificar imagens criadas:

```bash
docker images
```

![img03](../../assets/fase02/img03.png)

### 5. Teste Local do Container

Executar usando docker-compose:
```bash
docker-compose up -d
```

![img04](../../assets/fase02/img04.png)

Verificar containers rodando:
```bash
docker ps
```

![img05](../../assets/fase02/img05.png)

Testar endpoints:
```bash
curl http://localhost:8000
curl http://localhost:3000
```

![img06](../../assets/fase02/img06.png)

**Acessando no navegador:**
- Backend API: http://localhost:8000/docs
- Frontend: http://localhost:3000

![img07](../../assets/fase02/img07.png)

![img08](../../assets/fase02/img08.png)

Parar containers:
```bash
docker-compose down
```

### 6. Publicação no Docker Hub

> [!IMPORTANT]
> Substitua `<username>` pelo seu nome de usuário no Docker Hub

```bash
# Login no Docker Hub
docker login -u <username>
```

![img09](../../assets/fase02/img09.png)

```bash
# Tag das imagens para o Docker Hub
docker tag aplicacao-exemplo-project3-frontend <username>/teste-local-docker-front:latest
docker tag aplicacao-exemplo-project3-backend <username>/teste-local-docker-backend:latest

# Push para o Docker Hub
docker push <username>/teste-local-docker-front:latest
docker push <username>/teste-local-docker-backend:latest
```

![img10](../../assets/fase02/img10.png)

Verificar publicação:

![img11](../../assets/fase02/img11.png)

### 7. Limpeza (Opcional)

Para remover containers e imagens locais após o push:

```bash
# Parar e remover containers
docker stop docker-frontend docker-backend
docker rm docker-frontend docker-backend

# Remover imagens locais
docker rmi <username>/teste-local-docker-backend
docker rmi <username>/teste-local-docker-front
```

## ✅ Entregáveis Concluídos

| Objetivo | Status | Evidência |
|----------|--------|-----------|
| Criar Dockerfile para a aplicação | ✅ | `backend/Dockerfile`, `frontend/Dockerfile` e `dockercompose.yaml` criados |
| Realizar build da imagem Docker | ✅ | Imagens `docker-backend` e `docker-frontend` buildadas |
| Publicar imagem no Docker Hub | ✅ | Imagens `teste-local-docker-backend` e `teste-local-docker-frontend` publicadas |
| Imagem publicada no Dockerhub | ✅ | ![img](../../assets/fase02/img11.png) |
| Versionar Dockerfile no repositório GitHub | ✅ | Arquivos Docker commitados no [repositório](https://github.com/andrrade/Aplicacao-Exemplo-Project3) |
