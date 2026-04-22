# 🐳 Docker Notes & Cheat Sheet

Este repositório contém anotações completas sobre Docker, incluindo conceitos fundamentais, comandos essenciais, boas práticas e ferramentas relacionadas.

## 📌 Problema: “It works on my machine”
Diferenças entre ambientes causadas por:
- Ferramentas de gerenciamento
- Configurações distintas
- Dependências de hardware

## 💡 Solução com Docker
Docker utiliza imagens e containers para garantir consistência entre ambientes.

## 🧱 Conceitos Fundamentais

### 📦 Imagens
- Criadas a partir de um Dockerfile
- Compostos por camadas (layers)
- Incluem tudo que a aplicação precisa

### 📦 Containers
- Instâncias executáveis de imagens
- Isolados e leves

## ⚖️ Container vs Virtual Machine

Virtual Machine:
- Virtualiza hardware
- Mais pesada

Container:
- Compartilha kernel
- Mais leve e rápido

## 🔬 Anatomia de um Container
- Namespaces → isolamento
- Cgroups → controle de CPU, memória e rede

## 🚀 Vantagens
- Facilidade de configuração
- Portabilidade
- Consistência

## 🧪 Comandos Essenciais

Rodar container:
docker run -d --name=my-container -p 3000:3000 my-image

Logs:
docker logs my-container

Build:
docker build -t image-name .

Listar imagens:
docker images

Executar comando:
docker exec -it CONTAINER_ID bash

## 💾 Persistência de Dados

Bind Mount:
-v /host:/container

Volumes:
docker volume create my_volume

## 📦 Registries
- Docker Hub
- AWS ECR
- GitHub Registry

## ⚙️ Docker Compose

Subir aplicação:
docker-compose up

Parar:
docker-compose down

## ✅ Boas Práticas
- Evitar latest
- Usar imagens confiáveis
- Não usar root

## 🧠 Resumo
Docker facilita deploy, escalabilidade e padronização de ambientes.
