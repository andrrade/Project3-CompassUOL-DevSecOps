# Fase 5: Jenkins - Deploy no Kubernetes

Este documento detalha a extensão da pipeline Jenkins para incluir deploy automatizado no cluster Kubernetes local.

## 📋 Visão Geral

Nesta fase, a pipeline Jenkins será estendida para:
- Configurar acesso do Jenkins ao kubectl
- Adicionar stage de deploy no Kubernetes
- Automatizar o processo completo: Build → Push → Deploy
- Validar aplicação funcionando após deploy

## 🎯 Objetivos

- [ ] Configurar kubectl no Jenkins
- [ ] Configurar kubeconfig para acesso ao cluster
- [ ] Adicionar stage de deploy na pipeline
- [ ] Atualizar manifestos Kubernetes com nova imagem
- [ ] Validar deploy automatizado funcionando
- [ ] Testar pipeline completa end-to-end

## 🛠️ Pré-requisitos

### Software Necessário
- **Jenkins** configurado (Fase 4 concluída)
- **Cluster Kubernetes** local funcionando
- **kubectl** instalado e configurado
- **Manifestos K8s** criados (Fase 3 concluída)

### Validação do Ambiente
```bash
# Verificar cluster Kubernetes
kubectl cluster-info
kubectl get nodes

# Verificar Jenkins funcionando
curl http://localhost:8080

# Verificar pipeline anterior
# Jenkins → fastapi-hello-pipeline → última execução
```

## 📁 Estrutura de Arquivos

```
projeto/
├── jenkins/
│   ├── Jenkinsfile            # ← Atualizar com stage deploy
│   ├── kubectl-agent.yml      # ← Agent com kubectl (opcional)
│   └── kubeconfig            # ← Config cluster (se necessário)
├── k8s/
│   ├── deployment.yaml        # ← Existente da Fase 3
│   ├── service.yaml          # ← Existente da Fase 3
│   └── kustomization.yaml    # ← Opcional para substituições
├── assets/
│   └── fase05/
│       ├── img01.png         # ← Configuração kubectl Jenkins
│       ├── img02.png         # ← Jenkinsfile atualizado
│       ├── img03.png         # ← Credenciais kubeconfig
│       ├── img04.png         # ← Pipeline executando
│       ├── img05.png         # ← Stage deploy
│       ├── img06.png         # ← kubectl apply logs
│       ├── img07.png         # ← Pods atualizados
│       ├── img08.png         # ← Aplicação funcionando
│       ├── img09.png         # ← Pipeline completa
│       └── img10.png         # ← GitHub commit
└── README.md
```

## ⚙️ Configuração do kubectl no Jenkins

### Opção 1: Agent com kubectl (Recomendado)

**Localização:** `jenkins/kubectl-agent.yml`

```yaml
# TODO: Configurar agent Jenkins com kubectl
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: agent
spec:
  containers:
  - name: kubectl
    image: bitnami/kubectl:latest
    command:
    - sleep
    args:
    - 99d
    volumeMounts:
    - name: docker-sock
      mountPath: /var/run/docker.sock
    - name: kubeconfig
      mountPath: /root/.kube
  - name: docker
    image: docker:dind
    securityContext:
      privileged: true
    volumeMounts:
    - name: docker-sock
      mountPath: /var/run/docker.sock
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
  - name: kubeconfig
    secret:
      secretName: kubeconfig-secret
```

**Screenshot da configuração kubectl:**
![Configuração kubectl Jenkins](../../assets/fase05/img01.png)

### Opção 2: Instalar kubectl no Jenkins Container

```bash
# Acessar container Jenkins
docker exec -it jenkins-ci bash

# Instalar kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verificar instalação
kubectl version --client
```

## 🔐 Configuração do Kubeconfig

### 1. Obter Kubeconfig Local

```bash
# Copiar kubeconfig atual
cp ~/.kube/config ./jenkins/kubeconfig

# Verificar contexto
kubectl config current-context

# Listar contextos disponíveis
kubectl config get-contexts
```

### 2. Adicionar Credencial no Jenkins

**Caminho:** Manage Jenkins → Manage Credentials → Global

**Nova Credencial:**
- **Kind:** Secret file
- **ID:** `kubeconfig-file`
- **File:** Upload do arquivo `kubeconfig`
- **Description:** Kubernetes cluster configuration

**Screenshot da configuração kubeconfig:**
![Credenciais kubeconfig](../../assets/fase05/img03.png)

## 🚀 Atualização da Pipeline

### 1. Jenkinsfile Atualizado

**Localização:** `jenkins/Jenkinsfile`

```groovy
// TODO: Atualizar Jenkinsfile com stage de deploy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REPOSITORY = 'SEU_USUARIO/fastapi-hello'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-file'
        BUILD_NUMBER = "${env.BUILD_NUMBER}"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                script {
                    echo 'Building Docker image...'
                    // TODO: Implementar build
                    // dockerImage = docker.build("${DOCKER_REPOSITORY}:${IMAGE_TAG}", "./backend")
                    // docker.build("${DOCKER_REPOSITORY}:latest", "./backend")
                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    echo 'Pushing Docker image...'
                    // TODO: Implementar push
                    // docker.withRegistry("https://${DOCKER_REGISTRY}", "${DOCKER_CREDENTIALS_ID}") {
                    //     dockerImage.push("${IMAGE_TAG}")
                    //     dockerImage.push("latest")
                    // }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploying to Kubernetes...'
                    // TODO: Implementar deploy
                    withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                        // sh "kubectl --kubeconfig=${KUBECONFIG} apply -f k8s/"
                        // sh "kubectl --kubeconfig=${KUBECONFIG} set image deployment/fastapi-hello-deployment fastapi-hello=${DOCKER_REPOSITORY}:${IMAGE_TAG}"
                        // sh "kubectl --kubeconfig=${KUBECONFIG} rollout status deployment/fastapi-hello-deployment"
                    }
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                script {
                    echo 'Verifying deployment...'
                    // TODO: Implementar verificação
                    withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                        // sh "kubectl --kubeconfig=${KUBECONFIG} get pods -l app=fastapi-hello"
                        // sh "kubectl --kubeconfig=${KUBECONFIG} get svc fastapi-hello-service"
                        // 
                        // // Aguardar pods estarem prontos
                        // sh "kubectl --kubeconfig=${KUBECONFIG} wait --for=condition=ready pod -l app=fastapi-hello --timeout=300s"
                        // 
                        // // Testar aplicação
                        // sh "curl -f http://localhost:30001/ || exit 1"
                    }
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                script {
                    echo 'Cleaning up local images...'
                    // TODO: Limpeza
                    // sh "docker rmi ${DOCKER_REPOSITORY}:${IMAGE_TAG} || true"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo 'Pipeline succeeded! Application deployed successfully.'
            // TODO: Notificações de sucesso
        }
        failure {
            echo 'Pipeline failed! Check logs for details.'
            // TODO: Notificações de falha
            script {
                // Rollback em caso de falha
                withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                    // sh "kubectl --kubeconfig=${KUBECONFIG} rollout undo deployment/fastapi-hello-deployment || true"
                }
            }
        }
    }
}
```

**Screenshot do Jenkinsfile atualizado:**
![Jenkinsfile atualizado](../../assets/fase05/img02.png)

## 🔧 Melhorias nos Manifestos K8s

### 1. Deployment com Strategy

**Atualização em:** `k8s/deployment.yaml`

```yaml
# TODO: Adicionar estratégia de deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fastapi-hello-deployment
  labels:
    app: fastapi-hello
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: fastapi-hello
  template:
    metadata:
      labels:
        app: fastapi-hello
      annotations:
        # Para forçar restart em cada deploy
        deployment.kubernetes.io/revision: "${BUILD_NUMBER}"
    spec:
      containers:
      - name: fastapi-hello
        image: SEU_USUARIO/fastapi-hello:latest  # Será substituído pelo Jenkins
        ports:
        - containerPort: 8000
        # TODO: Adicionar health checks
        livenessProbe:
          httpGet:
            path: /
            port: 8000
          initialDelaySeconds: 30
        readinessProbe:
          httpGet:
            path: /
            port: 8000
          initialDelaySeconds: 5
        # TODO: Adicionar recursos
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
```

### 2. Kustomization (Opcional)

**Localização:** `k8s/kustomization.yaml`

```yaml
# TODO: Configurar kustomization para substituições
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- deployment.yaml
- service.yaml

images:
- name: SEU_USUARIO/fastapi-hello
  newTag: latest  # Será substituído pelo Jenkins
```

## 🏃 Execução da Pipeline Completa

### 1. Execução Manual

```bash
# Via interface Jenkins
Dashboard → fastapi-hello-pipeline → Build Now
```

**Screenshot da pipeline executando:**
![Pipeline executando](../../assets/fase05/img04.png)

**Screenshot do stage deploy:**
![Stage Deploy](../../assets/fase05/img05.png)

**Screenshot dos logs kubectl:**
![Kubectl Apply Logs](../../assets/fase05/img06.png)

### 2. Verificação do Deploy

```bash
# Verificar pods atualizados
kubectl get pods -l app=fastapi-hello

# Verificar deployment
kubectl get deployment fastapi-hello-deployment

# Verificar rollout
kubectl rollout status deployment/fastapi-hello-deployment
```

**Screenshot dos pods atualizados:**
![Pods Atualizados](../../assets/fase05/img07.png)

### 3. Teste da Aplicação

```bash
# Testar aplicação
curl http://localhost:30001/
curl http://localhost:30001/docs

# Verificar saúde
curl http://localhost:30001/health  # Se disponível
```

**Screenshot da aplicação funcionando:**
![Aplicação Funcionando](../../assets/fase05/img08.png)

**Screenshot da pipeline completa:**
![Pipeline Completa](../../assets/fase05/img09.png)

## ✅ Validação e Testes

### Comandos de Verificação

```bash
# Verificar imagem no pod
kubectl describe pod -l app=fastapi-hello | grep Image

# Verificar eventos do deployment
kubectl get events --sort-by=.metadata.creationTimestamp

# Verificar logs da aplicação
kubectl logs -l app=fastapi-hello --tail=50
```

### Testes End-to-End

- [ ] Pipeline executa todos os stages sem erro
- [ ] Imagem é buildada com tag do build number
- [ ] Imagem é enviada para Docker Hub
- [ ] Deploy no Kubernetes é executado
- [ ] Pods são atualizados com nova imagem
- [ ] Aplicação responde corretamente
- [ ] Rolling update funciona sem downtime

## 🔧 Troubleshooting

### Problemas Comuns

**kubectl não encontrado:**
- [ ] Verificar instalação kubectl no Jenkins
- [ ] Confirmar PATH configurado corretamente
- [ ] Testar com container agent

**Kubeconfig inválido:**
- [ ] Verificar arquivo kubeconfig correto
- [ ] Confirmar contexto atual
- [ ] Testar conectividade: `kubectl cluster-info`

**Deploy falha:**
- [ ] Verificar manifestos YAML válidos
- [ ] Confirmar imagem existe no registry
- [ ] Verificar recursos disponíveis no cluster

**Rollout trava:**
- [ ] Verificar health checks dos pods
- [ ] Confirmar recursos suficientes
- [ ] Verificar logs: `kubectl logs -l app=fastapi-hello`

### Comandos de Diagnóstico

```bash
# Debug do deployment
kubectl describe deployment fastapi-hello-deployment

# Debug dos pods
kubectl describe pod POD_NAME

# Verificar eventos
kubectl get events --field-selector involvedObject.name=fastapi-hello-deployment

# Debug de rede
kubectl get svc,endpoints
```

## 📊 Melhorias Avançadas

### 1. Blue-Green Deployment

```groovy
stage('Blue-Green Deploy') {
    steps {
        script {
            // TODO: Implementar blue-green
            // Criar deployment green
            // Testar deployment green
            // Switchover do service
            // Remover deployment blue
        }
    }
}
```

### 2. Canary Deployment

```yaml
# TODO: Configurar Istio ou Argo Rollouts
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: fastapi-hello-rollout
spec:
  strategy:
    canary:
      steps:
      - setWeight: 20
      - pause: {duration: 10s}
      - setWeight: 40
      - pause: {duration: 10s}
      - setWeight: 60
      - pause: {duration: 10s}
```

### 3. Smoke Tests

```groovy
stage('Smoke Tests') {
    steps {
        script {
            // TODO: Implementar testes pós-deploy
            sh '''
                kubectl wait --for=condition=ready pod -l app=fastapi-hello --timeout=300s
                curl -f http://localhost:30001/ || exit 1
                curl -f http://localhost:30001/docs || exit 1
            '''
        }
    }
}
```

## 📝 Versionamento no GitHub

### 1. Commit das atualizações

```bash
# Adicionar arquivos atualizados
git add jenkins/ k8s/

# Commit das mudanças
git commit -m "feat: add Kubernetes deploy stage to Jenkins pipeline"

# Push para o repositório
git push origin main
```

**Screenshot do commit no GitHub:**
![GitHub Commit](../../assets/fase05/img10.png)

## 📊 Entregáveis

| Item | Status | Evidência |
|------|--------|-----------|
| kubectl configurado no Jenkins | ⏳ | Screenshot configuração + teste |
| Kubeconfig credencial adicionada | ⏳ | Screenshot credenciais Jenkins |
| Stage deploy na pipeline | ⏳ | Screenshot Jenkinsfile atualizado |
| Pipeline completa funcionando | ⏳ | Screenshot execução end-to-end |
| Deploy automatizado validado | ⏳ | Screenshot pods atualizados |
| Aplicação funcionando pós-deploy | ⏳ | Screenshot teste aplicação |

## 🔗 Links Úteis

- **Jenkins Pipeline:** http://localhost:8080/job/fastapi-hello-pipeline/
- **Aplicação K8s:** http://localhost:30001
- **Documentação K8s:** http://localhost:30001/docs
- **Docker Hub:** [Inserir link da imagem]
- **Repositório GitHub:** [Inserir link do repositório]

## 📞 Próximos Passos

Com o deploy automatizado configurado, você estará pronto para:
- **Fase 6:** Testes automatizados na pipeline
- **Fase 7:** Monitoramento com Prometheus/Grafana
- **Fase 8:** GitOps com ArgoCD
- **Fase 9:** Multi-environment deployments

---

**Data de criação:** [Inserir data]  
**Última atualização:** [Inserir data]  
**Responsável:** [Seu nome]