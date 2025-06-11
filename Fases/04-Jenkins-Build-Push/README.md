# Fase 4: Jenkins - Build e Push

Este documento detalha a configuração de uma pipeline CI/CD no Jenkins para automatizar o build e push da imagem Docker.

## 📋 Visão Geral

Nesta fase, será criada uma pipeline Jenkins que:
- Monitora mudanças no repositório GitHub
- Executa build automatizado da imagem Docker
- Realiza push da imagem para o Docker Hub
- Pode ser executada manualmente ou por webhook

## 🎯 Objetivos

- [ ] Instalar e configurar Jenkins
- [ ] Criar pipeline de CI/CD
- [ ] Configurar stage de build
- [ ] Configurar stage de push
- [ ] Integrar com GitHub (webhook)
- [ ] Validar pipeline completa

## 🛠️ Pré-requisitos

### Software Necessário
- **Docker** instalado e funcionando
- **Repositório GitHub** com código (Fases 1-3 concluídas)
- **Conta Docker Hub** ativa
- **Acesso de administrador** para instalação

### Credenciais Necessárias
- **GitHub Token** (para webhooks)
- **Docker Hub credenciais** (username/password ou token)

## 📁 Estrutura de Arquivos

```
projeto/
├── jenkins/
│   ├── Jenkinsfile            # ← Pipeline as Code
│   └── docker-compose.yml     # ← Jenkins local (opcional)
├── assets/
│   └── fase04/
│       ├── img01.png          # ← Jenkins instalação
│       ├── img02.png          # ← Jenkins dashboard
│       ├── img03.png          # ← Criação da pipeline
│       ├── img04.png          # ← Configuração credenciais
│       ├── img05.png          # ← Pipeline executando
│       ├── img06.png          # ← Stage de build
│       ├── img07.png          # ← Stage de push
│       ├── img08.png          # ← Pipeline concluída
│       ├── img09.png          # ← Webhook GitHub
│       └── img10.png          # ← GitHub commit
└── README.md
```

## 🚀 Instalação do Jenkins

### Opção 1: Docker Compose (Recomendado)

**Localização:** `jenkins/docker-compose.yml`

```yaml
# TODO: Configurar Docker Compose para Jenkins
version: '3.8'
services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins-ci
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - DOCKER_HOST=unix:///var/run/docker.sock
    restart: unless-stopped

volumes:
  jenkins_home:
```

```bash
# Iniciar Jenkins
cd jenkins
docker-compose up -d

# Verificar logs para senha inicial
docker-compose logs jenkins
```

**Screenshot da instalação:**
![Jenkins Instalação](../../assets/fase04/img01.png)

### Opção 2: Instalação Local

```bash
# Ubuntu/Debian
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins

# macOS
brew install jenkins-lts
```

## ⚙️ Configuração Inicial do Jenkins

### 1. Acesso Inicial

```bash
# Acessar Jenkins
http://localhost:8080

# Obter senha inicial (Docker)
docker exec jenkins-ci cat /var/jenkins_home/secrets/initialAdminPassword

# Obter senha inicial (Local)
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

**Screenshot do dashboard Jenkins:**
![Jenkins Dashboard](../../assets/fase04/img02.png)

### 2. Plugins Necessários

Instalar os seguintes plugins:
- **Docker Pipeline**
- **GitHub Integration Plugin**
- **Pipeline: GitHub Groovy Libraries**
- **Credentials Binding Plugin**
- **Webhook Step Plugin**

### 3. Configuração de Credenciais

**Caminho:** Manage Jenkins → Manage Credentials → Global

**Credenciais necessárias:**
1. **Docker Hub:**
   - Kind: Username with password
   - ID: `dockerhub-credentials`
   - Username: [Seu username Docker Hub]
   - Password: [Sua senha/token Docker Hub]

2. **GitHub (opcional para webhook):**
   - Kind: Secret text
   - ID: `github-token`
   - Secret: [Seu GitHub Personal Access Token]

**Screenshot da configuração de credenciais:**
![Credenciais Jenkins](../../assets/fase04/img04.png)

## 🔧 Criação da Pipeline

### 1. Nova Pipeline

**Caminho:** New Item → Pipeline → Nome: `fastapi-hello-pipeline`

**Screenshot da criação:**
![Criação Pipeline](../../assets/fase04/img03.png)

### 2. Configuração da Pipeline

**Pipeline Definition:** Pipeline script from SCM
- **SCM:** Git
- **Repository URL:** https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
- **Branch:** */main
- **Script Path:** jenkins/Jenkinsfile

### 3. Jenkinsfile

**Localização:** `jenkins/Jenkinsfile`

```groovy
// TODO: Criar Jenkinsfile completo
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REPOSITORY = 'SEU_USUARIO/fastapi-hello'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        BUILD_NUMBER = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                // TODO: Implementar checkout do código
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                // TODO: Implementar build da imagem
                script {
                    echo 'Building Docker image...'
                    // docker.build("${DOCKER_REPOSITORY}:${BUILD_NUMBER}", "./backend")
                    // docker.build("${DOCKER_REPOSITORY}:latest", "./backend")
                }
            }
        }
        
        stage('Push') {
            steps {
                // TODO: Implementar push para Docker Hub
                script {
                    echo 'Pushing Docker image...'
                    // docker.withRegistry("https://${DOCKER_REGISTRY}", "${DOCKER_CREDENTIALS_ID}") {
                    //     dockerImage.push("${BUILD_NUMBER}")
                    //     dockerImage.push("latest")
                    // }
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                // TODO: Limpeza de imagens locais
                script {
                    echo 'Cleaning up local images...'
                    // sh "docker rmi ${DOCKER_REPOSITORY}:${BUILD_NUMBER} || true"
                    // sh "docker rmi ${DOCKER_REPOSITORY}:latest || true"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```

## 🏃 Execução da Pipeline

### 1. Execução Manual

```bash
# Via interface Jenkins
Dashboard → fastapi-hello-pipeline → Build Now
```

**Screenshot da pipeline executando:**
![Pipeline Executando](../../assets/fase04/img05.png)

**Screenshot do stage de build:**
![Stage Build](../../assets/fase04/img06.png)

**Screenshot do stage de push:**
![Stage Push](../../assets/fase04/img07.png)

**Screenshot da pipeline concluída:**
![Pipeline Concluída](../../assets/fase04/img08.png)

### 2. Execução por Webhook (Opcional)

**Configuração no Jenkins:**
- Pipeline → Configure → Build Triggers
- ✅ GitHub hook trigger for GITScm polling

**Configuração no GitHub:**
- Repository → Settings → Webhooks → Add webhook
- **Payload URL:** http://SEU_JENKINS:8080/github-webhook/
- **Content type:** application/json
- **Events:** Just the push event

**Screenshot do webhook GitHub:**
![Webhook GitHub](../../assets/fase04/img09.png)

## ✅ Validação e Testes

### Comandos de Verificação

```bash
# Verificar imagem no Docker Hub
docker pull SEU_USUARIO/fastapi-hello:latest

# Verificar tags da imagem
curl -s https://registry.hub.docker.com/v2/repositories/SEU_USUARIO/fastapi-hello/tags/
```

### Testes da Pipeline

- [ ] Pipeline executa sem erros
- [ ] Stage de build completa com sucesso
- [ ] Stage de push completa com sucesso
- [ ] Imagem aparece no Docker Hub com nova tag
- [ ] Logs da pipeline são claros e informativos
- [ ] Webhook dispara pipeline automaticamente (se configurado)

## 🔧 Troubleshooting

### Problemas Comuns

**Pipeline falha no build:**
- [ ] Verificar Dockerfile está correto
- [ ] Confirmar Docker daemon acessível no Jenkins
- [ ] Validar permissões do usuário Jenkins

**Pipeline falha no push:**
- [ ] Verificar credenciais Docker Hub
- [ ] Confirmar conectividade com registry
- [ ] Validar nome do repositório

**Webhook não funciona:**
- [ ] Verificar URL do webhook
- [ ] Confirmar Jenkins acessível externamente
- [ ] Validar configuração do GitHub

### Comandos de Diagnóstico

```bash
# Verificar logs do Jenkins
docker logs jenkins-ci

# Testar conectividade Docker
docker info

# Verificar plugins instalados
# Via interface: Manage Jenkins → Manage Plugins
```

## 📊 Melhorias da Pipeline

### Funcionalidades Avançadas

**Testes Automatizados:**
```groovy
stage('Test') {
    steps {
        script {
            sh 'docker run --rm ${DOCKER_REPOSITORY}:${BUILD_NUMBER} python -m pytest'
        }
    }
}
```

**Notificações:**
```groovy
post {
    success {
        emailext to: 'team@empresa.com',
                 subject: 'Build Success: ${env.JOB_NAME}',
                 body: 'Build completed successfully!'
    }
}
```

**Análise de Segurança:**
```groovy
stage('Security Scan') {
    steps {
        script {
            sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy ${DOCKER_REPOSITORY}:${BUILD_NUMBER}'
        }
    }
}
```

## 📝 Versionamento no GitHub

### 1. Commit dos arquivos Jenkins

```bash
# Adicionar arquivos Jenkins
git add jenkins/

# Commit das mudanças
git commit -m "feat: add Jenkins pipeline for CI/CD automation"

# Push para o repositório
git push origin main
```

**Screenshot do commit no GitHub:**
![GitHub Commit](../../assets/fase04/img10.png)

## 📊 Entregáveis

| Item | Status | Evidência |
|------|--------|-----------|
| Jenkins instalado e configurado | ⏳ | Screenshot dashboard Jenkins |
| Pipeline criada | ⏳ | Screenshot configuração pipeline |
| Stage de build funcionando | ⏳ | Screenshot execução build |
| Stage de push funcionando | ⏳ | Screenshot execução push |
| Imagem no Docker Hub atualizada | ⏳ | Screenshot Docker Hub + nova tag |
| Jenkinsfile versionado | ⏳ | Commit no GitHub |

## 🔗 Links Úteis

- **Jenkins Local:** http://localhost:8080
- **Repositório GitHub:** [Inserir link do repositório]
- **Docker Hub Repository:** [Inserir link da imagem]
- **Documentação Jenkins:** https://www.jenkins.io/doc/
- **Pipeline Syntax:** https://www.jenkins.io/doc/book/pipeline/syntax/

## 📞 Próximos Passos

Com a pipeline Jenkins configurada, você estará pronto para:
- **Fase 5:** Deploy automatizado no Kubernetes
- **Fase 6:** Testes automatizados na pipeline
- **Fase 7:** Monitoramento e alertas
- **Fase 8:** GitOps com ArgoCD

---

**Data de criação:** [Inserir data]  
**Última atualização:** [Inserir data]  
**Responsável:** [Seu nome]