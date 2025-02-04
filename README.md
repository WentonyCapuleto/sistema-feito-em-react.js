# A pasta do front end está separada da pasta do backend e precisara colocar o frontend dentro da pasta do backend que é a pasta testeareaarena

# Projeto: React + Laravel + MySQL + Docker + Kubernetes

## 📌 Descrição
Este projeto realiza a migração de dados de uma API para um banco MySQL, sincroniza diariamente novos registros e fornece um frontend com Autocomplete.

## 🚀 Tecnologias Usadas
- **Backend:** Laravel 11, MySQL, Redis
- **Frontend:** React.js, Material UI
- **Infraestrutura:** Docker, Kubernetes, GitHub Actions

## 🛠️ Configuração
### 1️⃣ Clonar o repositório
```sh
git clone https://github.com/WentonyCapuleto/sistema-feito-em-react.js
cd meu-projeto

name: Deploy Automático

on:
  push:
    branches:
      - main

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Clonar repositório
        uses: actions/checkout@v3

      - name: Configurar Docker
        uses: docker/setup-buildx-action@v2

      - name: Construir e testar backend
        run: |
          docker build -t backend-test ./backend
          docker run --rm backend-test php artisan test

      - name: Construir e testar frontend
        run: |
          docker build -t frontend-test ./frontend
          docker run --rm frontend-test yarn test

  deploy:
    runs-on: ubuntu-latest
    needs: build-and-test

    steps:
      - name: Login no DockerHub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Construir e enviar imagens Docker
        run: |
          docker build -t meu-usuario/backend:latest ./backend
          docker push meu-usuario/backend:latest
          docker build -t meu-usuario/frontend:latest ./frontend
          docker push meu-usuario/frontend:latest

      - name: Aplicar Kubernetes
        run: |
          kubectl apply -f kubernetes/backend-deployment.yml
          kubectl apply -f kubernetes/frontend-deployment.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: meu-usuario/backend:latest
        ports:
        - containerPort: 3000

====================
Documentação do Projeto
====================

.. toctree::
   :maxdepth: 2
   :caption: Conteúdo:

   backend
   frontend
   kubernetes

====================
Backend - Laravel API
====================

## Endpoints Disponíveis:
- **GET /items** → Retorna uma lista paginada de itens.
- **POST /items** → Adiciona um novo item no banco de dados.

## Jobs Automatizados:
- **Sincronização Diária** → Atualiza novos dados da API JSONPlaceholder.
