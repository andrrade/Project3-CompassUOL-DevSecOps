<p align="center">
  <img src="./assets/logos/projeto3.png" alt="Logo do Projeto 3" width="400">
</p>
<br>

# Deploy automatizado de API com FastAPI, Jenkins e Kubernetes

<div align="center">
  <img src="https://techstack-generator.vercel.app/kubernetes-icon.svg" alt="kubernetes icon" width="60" height="60" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/jenkins/jenkins-original.svg" alt="jenkins logo" height="50" />
  <img src="https://techstack-generator.vercel.app/docker-icon.svg" alt="docker icon" width="60" height="60" />
  <img src="https://raw.githubusercontent.com/gilbarbara/logos/main/logos/fastapi.svg" alt="fastapi logo" width="55" height="55" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nodejs/nodejs-original.svg" height="40" alt="nodejs logo" />
  <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/react/react-original.svg" alt="react logo" width="40" height="40">
</div>

<br>

<div align="center">
  <a href="https://kubernetes.io/" target="_blank">
    <img src="https://img.shields.io/badge/Kubernetes-326ce5?style=for-the-badge&logo=kubernetes&logoColor=white" alt="Kubernetes" title="Kubernetes"/></a>
  <a href="https://www.jenkins.io/" target="_blank">
    <img src="https://img.shields.io/badge/Jenkins-d24939?style=for-the-badge&logo=jenkins&logoColor=white" alt="Jenkins" title="Jenkins"/></a>
  <a href="https://www.docker.com/" target="_blank">
    <img src="https://img.shields.io/badge/Docker-0db7ed?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" title="Docker"/></a>
  <a href="https://fastapi.tiangolo.com/" target="_blank">
    <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI" title="FastAPI"/></a>
  <a href="https://nodejs.org/" target="_blank">
    <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white" alt="Node.js" title="Node.js"/></a>
  <a href="https://reactjs.org/" target="_blank">
    <img src="https://img.shields.io/badge/React-20232a?style=for-the-badge&logo=react&logoColor=61dafb" alt="React" title="React"/></a>
  <a href="https://hub.docker.com/" target="_blank">
    <img src="https://img.shields.io/badge/Docker_Hub-003f8c?style=for-the-badge&logo=docker&logoColor=white" alt="Docker Hub" title="Docker Hub"/></a>
</div>

---

# 📁 Repo da Aplicação

Os arquivos da aplicação utilizados neste projeto estão disponíveis aqui:
<br>
🔗 **[Repo Aplicação – Aplicacao-Exemplo-Project3](https://github.com/andrrade/Aplicacao-Exemplo-Project3)**

---

## 💻 Tecnologias Utilizadas

* **FastAPI**: Framework web em Python.
* **Docker**: Para conteinerização da aplicação.
* **Docker Hub**: Registro público de imagens.
* **Jenkins**: Ferramenta de CI/CD.
* **Kubernetes (Rancher Desktop)**: Orquestrador de contêineres utilizado localmente.
* **Node.js**: Runtime JavaScript usado no backend.
* **React**: Biblioteca JavaScript para criação do frontend.

Para este projeto, utilizei o sistema operacional [Pop!\_OS](https://system76.com/pop/).

## Sumário 📝

- [Enunciado](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/PB-Projeto-DevOps.pdf)

- [Fase 1](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/Fases/01-Preparacao-do-Projeto/README.md)  
  - [X] Criar um repositório de código no Github para inserir a aplicação de exemplo  
  - [X] Criar conta no Docker Hub  
  - [X] Verificar acesso ao cluster Kubernetes local  
  - [X] Validar execução local com uvicorn  

- [Fase 2](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/Fases/02-Conteinerizacao-com-Docker/README.md)  
  - [X] Criar o Dockerfile  
  - [X] Fazer build
  - [X] Fazer push
  - [X] Versionar o Dockerfile junto com o código da aplicação no GitHub  

- [Fase 3](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/Fases/03-Arquivos-de-Deploy-no-Kubernetes/README.md)  
  - [X] Criar o YAML de deployment da aplicação e aplicá-lo no cluster  
  - [X] Criar o YAML de service do deployment e aplicá-lo no cluster  

- [Fase 4 e 5](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/tree/main/Fases/04-05-Jenkins-Deploy-no-Kubernetes)  
  - [X] Criar a pipeline no Jenkins  
  - [X] Realizar o stage de build  
  - [X] Realizar o stage de push  
  - [X] Jenkins precisa acessar o `kubectl` (usar agent com `kubectl` e `kubeconfig` configurados)  
  - [X] Adicionar etapa de deploy no `Jenkinsfile`  
  - [X] Testar a pipeline completa 

- Fase 6
  - [X] Criar `README.md`
    - [X] Passos para reproduzir o projeto.
    - [X] Tecnologias usadas.
    - [X] Print do Jenkins.
    - [X] Apresentar pipeline funcionando: build, push e deploy.  

- [Extras](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/tree/main/Fases/07-Desafios-Extras)  

  - [X] Criar uma etapa após o push da imagem de container, realizar o scanner de vulnerabilidades com o **Trivy**. 
  - [Clique aqui](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/Fases/07-Desafios-Extras/fase06-extra-trivy/README.md).
  - [X] Criar um webhook com o **GitHub** 
  - [Clique aqui](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/Fases/07-Desafios-Extras/fase07-extra-webhook-github/README.md)
  - [X] Criar um webhook com o **Slack** ou **Discord** para avisar quando a pipeline for atualizada no ambiente Kubernetes. 
  - [Clique aqui](https://github.com/andrrade/Project3-CompassUOL-DevSecOps/blob/main/Fases/07-Desafios-Extras/fase08-extra-webhook-discord/README.md)
  - [ ] Subir o **SonarQube** em ambiente Docker, conectá-lo ao Jenkins e enviar o código para análise **SAST**  
  - [ ] Utilizar **Helm Chart** para implantar a aplicação no Kubernetes  

---

## Apresentação Final

Mostrando projeto completo com todas as fases funcionando + extras (webhook github, webhook discord, trivy e frontend estilizado):

![vídeo completo](./assets/video-final.mp4)

https://github.com/user-attachments/assets/d4800163-a698-40f9-bc85-fbec00967c58

<p align="center">
  <br>
  <img src="./assets/logos/compassUol.png" alt="CompassUOL Logo" width="250">
</p>

---

## 🧡 Obrigada pela atenção!

<p align="center">
  <br>
  <img src="./assets/chuck_norris.png" alt="chuch-norris">
</p>

