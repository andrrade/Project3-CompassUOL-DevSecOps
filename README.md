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

- [Enunciado]()
- [Fase 1]()
  [X] Criar um repositório de código no Github para inserir a aplicação de exemplo
  [X] Criar conta no Docker Hub
  [X] Verificar acesso ao cluster Kubernetes local
  [ ] Validar execução local com uvicorn
- [Fase 2]()
  [ ]Criar o dockerfile
  [ ] Fazer build: docker build -t usuario/fastapi-hello:latest
  [ ] Fazer push: docker push usuario/fastapi-hello:latest
  [ ] Versionar o dockerfile junto com o código da aplicação no github
- [Fase 3]()
  [ ] Criar o yaml de deployment da aplicação e aplicá-lo no cluster
  [ ] Criar o yaml de service do deploymento e aplicá-lo no cluster
- [Fase 4]()
  [ ]Criar a pipeline no Jenkins
  [ ]Realizar o stage de build
  [ ]Realizar o stage de push
- [Fase 5]()
  [ ] Jenkins precisa acessar o kubectl (usar agent com kubectl e kubeconfig configurados)
  [ ] Adicionar etapa de deploy no Jenkinsfile
  [ ]Testar a pipeline completa
- [Fase 6]()
  [ ]Criar README.md
- [Extras]()
  [ ] Criar uma etapa após o push da imagem de container, realizar o scanner de vulnerabilidades, utilizando o Trivy
  [ ] Criar um webhook com o Slack ou Discord para avisar quando a pipeline for atualizar no ambiente Kubernetes
  [ ] Subir o Sonarqube em ambiente Docker e conectá-lo com o Jenkins e enviar todo o código da aplicação para a análise SAST
  [ ] Utilizar Helm Chart para implantar a aplicação no Kubernetes

<p align="center">
  <br>
  <img src="./assets/logos/compassUol.png" alt="CompassUOL Logo" width="250">
</p>
