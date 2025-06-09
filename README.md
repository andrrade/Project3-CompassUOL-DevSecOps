<p align="center">
  <img src="./assets/logos/projeto3.png" alt="Logo do Projeto 3" width="400">
</p>
<br>

# Deploy automatizado de API com FastAPI, Jenkins e Kubernetes

<div align="center">
  <a href="https://fastapi.tiangolo.com/" target="_blank">
    <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/fastapi/fastapi-original.svg" alt="FastAPI" width="50" title="FastAPI"/></a>
  <a href="https://www.docker.com/" target="_blank">
    <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/docker/docker-original.svg" alt="Docker" width="50" title="Docker"/></a>
  <a href="https://hub.docker.com/" target="_blank">
    <img src="https://img.shields.io/badge/Docker%20Hub-0db7ed?style=for-the-badge&logo=docker&logoColor=white" alt="Docker Hub" title="Docker Hub"/></a>
  <a href="https://www.jenkins.io/" target="_blank">
    <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/jenkins/jenkins-original.svg" alt="Jenkins" width="50" title="Jenkins"/></a>
  <a href="https://kubernetes.io/" target="_blank">
    <img src="https://raw.githubusercontent.com/kubernetes/kubernetes/master/logo/logo.png" alt="Kubernetes" width="50" title="Kubernetes"/></a>
</div>

# Sumário 📝

- [Enunciado](#enunciado)

- [Fase 1](#fase-1)  
  - [x] Criar um repositório de código no Github para inserir a aplicação de exemplo  
  - [x] Criar conta no Docker Hub  
  - [x] Verificar acesso ao cluster Kubernetes local  
  - [ ] Validar execução local com uvicorn  

- [Fase 2](#fase-2)  
  - [ ] Criar o Dockerfile  
  - [ ] Fazer build: `docker build -t usuario/fastapi-hello:latest`  
  - [ ] Fazer push: `docker push usuario/fastapi-hello:latest`  
  - [ ] Versionar o Dockerfile junto com o código da aplicação no GitHub  

- [Fase 3](#fase-3)  
  - [ ] Criar o YAML de deployment da aplicação e aplicá-lo no cluster  
  - [ ] Criar o YAML de service do deployment e aplicá-lo no cluster  

- [Fase 4](#fase-4)  
  - [ ] Criar a pipeline no Jenkins  
  - [ ] Realizar o stage de build  
  - [ ] Realizar o stage de push  

- [Fase 5](#fase-5)  
  - [ ] Jenkins precisa acessar o `kubectl` (usar agent com `kubectl` e `kubeconfig` configurados)  
  - [ ] Adicionar etapa de deploy no `Jenkinsfile`  
  - [ ] Testar a pipeline completa  

- [Fase 6](#fase-6)  
  - [ ] Criar `README.md`  

- [Extras](#extras)  
  - [ ] Criar uma etapa após o push da imagem de container, realizar o scanner de vulnerabilidades com o **Trivy**  
  - [ ] Criar um webhook com o **Slack** ou **Discord** para avisar quando a pipeline for atualizada no ambiente Kubernetes  
  - [ ] Subir o **SonarQube** em ambiente Docker, conectá-lo ao Jenkins e enviar o código para análise **SAST**  
  - [ ] Utilizar **Helm Chart** para implantar a aplicação no Kubernetes  

<p align="center">
  <br>
  <img src="./assets/logos/compassUol.png" alt="CompassUOL Logo" width="250">
</p>
