# Pipeline CI/CD com Scanner de Segurança Trivy

## 📋 Visão Geral

Esta pipeline Jenkins implementa um processo completo de CI/CD com scanner de vulnerabilidades para aplicações containerizadas. O projeto inclui build de imagens Docker para frontend e backend, push para DockerHub, análise de segurança com Trivy e deploy automatizado no Kubernetes.

## 🏗️ Arquitetura da Pipeline

### Estágios da Pipeline

1. **Build Images** - Construção paralela das imagens Docker
2. **Push Images** - Upload das imagens para o DockerHub
3. **Security Scan** - Análise de vulnerabilidades com Trivy ⚡
4. **Deploy** - Deploy no cluster Kubernetes
5. **Verify** - Verificação do status do deployment

## 🔒 Scanner de Segurança com Trivy

### Funcionalidades Implementadas

- **Instalação Automática**: O Trivy é instalado automaticamente se não estiver presente no sistema
- **Scan Paralelo**: Análise simultânea das imagens frontend e backend
- **Relatórios Detalhados**: Geração de relatórios em formato JSON e tabela
- **Contagem de Vulnerabilidades**: Classificação automática por severidade (CRITICAL, HIGH, MEDIUM, LOW, UNKNOWN)
- **Não-Bloqueante**: Pipeline continua mesmo com vulnerabilidades encontradas (`--exit-code 0`)

### Como Funciona o Scanner

```bash
# Instalação do Trivy (se necessário)
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b .

# Scan da imagem com saída em tabela
trivy image --format table --exit-code 0 andrrade/meu-frontend:BUILD_ID

# Scan da imagem com saída em JSON para análise
trivy image --format json --quiet andrrade/meu-frontend:BUILD_ID > frontend-scan.json
```

### Relatório de Vulnerabilidades

O scanner gera automaticamente um resumo das vulnerabilidades encontradas:

```
Frontend - Total: 25 (UNKNOWN: 2, LOW: 10, MEDIUM: 8, HIGH: 4, CRITICAL: 1)
Backend - Total: 18 (UNKNOWN: 1, LOW: 7, MEDIUM: 6, HIGH: 3, CRITICAL: 1)
```

## 📊 Análise de Segurança

### Tipos de Severidade

- **CRITICAL**: Vulnerabilidades críticas que devem ser corrigidas imediatamente
- **HIGH**: Vulnerabilidades de alta prioridade
- **MEDIUM**: Vulnerabilidades de prioridade média
- **LOW**: Vulnerabilidades de baixa prioridade
- **UNKNOWN**: Vulnerabilidades sem classificação definida

## 📚 Referências

- [Documentação oficial do Trivy](https://aquasecurity.github.io/trivy/)

---

## Jenkins file com o trivy adicionado:

```jenkinsfile
pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = "andrrade"
        BUILD_TAG = "${env.BUILD_ID}"
        DISCORD_WEBHOOK = "https://discordapp.com/api/webhooks/1382761573411721216/7M3tXv4XD7_H3xEjYUJndbOm9sGWkPABuLSvXssREJmWckZ6tYSqn9LYrUN0eFjKEgDX"
    }

    stages {
        stage('Build Images') {
            parallel {
                stage('Frontend') {
                    steps {
                        script {
                            frontendapp = docker.build("${DOCKERHUB_REPO}/meu-frontend:${BUILD_TAG}", '-f ./frontend/Dockerfile ./frontend')
                        }
                    }
                }
                stage('Backend') {
                    steps {
                        script {
                            backendapp = docker.build("${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}", '-f ./backend/Dockerfile ./backend')
                        }
                    }
                }
            }
        }
        
        stage('Push Images') {
            parallel {
                stage('Push Frontend') {
                    steps {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                                frontendapp.push('latest')
                                frontendapp.push("${BUILD_TAG}")
                            }
                        }
                    }
                }
                stage('Push Backend') {
                    steps {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                                backendapp.push('latest')
                                backendapp.push("${BUILD_TAG}")
                            }
                        }
                    }
                }
            }
        }
        
        stage('Security Scan') {
            parallel {
                stage('Scan Frontend') {
                    steps {
                        script {
                            // Instala o Trivy se não existir
                            sh '''
                                if ! command -v trivy &> /dev/null; then
                                    curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b .
                                fi
                            '''
                            // Usa ./trivy caso trivy global não exista
                            def trivyCmd = sh(script: 'command -v trivy', returnStatus: true) == 0 ? 'trivy' : './trivy'

                            // Executa scan sem falhar o build mesmo com vulnerabilidades
                            sh """
                                ${trivyCmd} image --format table --exit-code 0 ${DOCKERHUB_REPO}/meu-frontend:${BUILD_TAG}
                            """

                            // Gera relatório JSON para análise de severidades
                            sh """
                                ${trivyCmd} image --format json --quiet ${DOCKERHUB_REPO}/meu-frontend:${BUILD_TAG} > frontend-scan.json
                                
                                python3 << 'EOF'
import json
try:
    with open('frontend-scan.json', 'r') as f:
        data = json.load(f)
    
    critical = high = medium = low = unknown = 0
    for result in data.get('Results', []):
        for vuln in result.get('Vulnerabilities', []):
            severity = vuln.get('Severity', 'UNKNOWN').upper()
            if severity == 'CRITICAL': 
                critical += 1
            elif severity == 'HIGH': 
                high += 1
            elif severity == 'MEDIUM': 
                medium += 1
            elif severity == 'LOW': 
                low += 1
            else: 
                unknown += 1
    
    total = critical + high + medium + low + unknown
    print(f"Frontend - Total: {total} (UNKNOWN: {unknown}, LOW: {low}, MEDIUM: {medium}, HIGH: {high}, CRITICAL: {critical})")
except Exception as e:
    print(f"Frontend - Scan error: {e}")
EOF
                            """
                        }
                    }
                }
                
                stage('Scan Backend') {
                    steps {
                        script {
                            sh '''
                                if ! command -v trivy &> /dev/null; then
                                    curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b .
                                fi
                            '''
                            def trivyCmd = sh(script: 'command -v trivy', returnStatus: true) == 0 ? 'trivy' : './trivy'
                            
                            sh """
                                ${trivyCmd} image --format table --exit-code 0 ${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}
                            """
                            
                            sh """
                                ${trivyCmd} image --format json --quiet ${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG} > backend-scan.json
                                
                                python3 << 'EOF'
import json
try:
    with open('backend-scan.json', 'r') as f:
        data = json.load(f)
    
    critical = high = medium = low = unknown = 0
    for result in data.get('Results', []):
        for vuln in result.get('Vulnerabilities', []):
            severity = vuln.get('Severity', 'UNKNOWN').upper()
            if severity == 'CRITICAL': 
                critical += 1
            elif severity == 'HIGH': 
                high += 1
            elif severity == 'MEDIUM': 
                medium += 1
            elif severity == 'LOW': 
                low += 1
            else: 
                unknown += 1
    
    total = critical + high + medium + low + unknown
    print(f"Backend - Total: {total} (UNKNOWN: {unknown}, LOW: {low}, MEDIUM: {medium}, HIGH: {high}, CRITICAL: {critical})")
except Exception as e:
    print(f"Backend - Scan error: {e}")
EOF
                            """
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            when {
                not { 
                    anyOf {
                        equals expected: 'FAILURE', actual: currentBuild.result
                        equals expected: 'ABORTED', actual: currentBuild.result
                    }
                }
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://192.168.1.81:6443']) {
                    sh """
                        cp ./k8s/deployment.yaml ./k8s/deployment.tmp.yaml
                        sed -i 's|{{FRONTEND_TAG}}|${BUILD_TAG}|g' ./k8s/deployment.tmp.yaml
                        sed -i 's|{{BACKEND_TAG}}|${BUILD_TAG}|g' ./k8s/deployment.tmp.yaml
                        kubectl apply -f ./k8s/deployment.tmp.yaml
                        kubectl rollout status deployment/frontend-app
                        kubectl rollout status deployment/backend-app
                    """
                }
            }
        }
        
        stage('Verify') {
            when {
                not { 
                    anyOf {
                        equals expected: 'FAILURE', actual: currentBuild.result
                        equals expected: 'ABORTED', actual: currentBuild.result
                    }
                }
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://192.168.1.81:6443']) {
                    sh '''
                        kubectl get pods -l app=frontend-app
                        kubectl get pods -l app=backend-app
                        kubectl get services
                    '''
                }
            }
        }
    }

    post {
        always {
            chuckNorris()
            sh 'rm -f ./trivy ./k8s/deployment.tmp.yaml frontend-scan.json backend-scan.json'
        }
        success {
            echo '🚀 Deploy realizado com sucesso!'
            echo "✅ Frontend: ${DOCKERHUB_REPO}/meu-frontend:${BUILD_TAG}"
            echo "✅ Backend: ${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}"
            
            discordSend(
                description: """
**✅ Deploy Realizado com Sucesso!**
🚀 **Job:** ${JOB_NAME}
🔢 **Build:** #${BUILD_NUMBER}
⏱️ **Duração:** ${currentBuild.durationString}

**🌐 Aplicação Disponível:**
🎨 **Frontend:** http://localhost:30000
🔧 **Backend:** http://localhost:30001
📚 **Docs:** http://localhost:30001/docs

**🐳 Imagens Docker:**
• Frontend: `${DOCKERHUB_REPO}/meu-frontend:${BUILD_TAG}`
• Backend: `${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}`

🔗 **Logs:** ${BUILD_URL}
""",
                footer: "Jenkins CI/CD Pipeline",
                link: env.BUILD_URL,
                result: "SUCCESS",
                title: "✅ Pipeline Executada com Sucesso",
                webhookURL: env.DISCORD_WEBHOOK
            )
        }
        failure {
            echo '❌ Build falhou!'
            
            discordSend(
                description: """
**❌ Build Falhou!**
🚀 **Job:** ${JOB_NAME}
🔢 **Build:** #${BUILD_NUMBER}
⏱️ **Duração:** ${currentBuild.durationString}
🔍 **Verificar logs:** ${BUILD_URL}console
""",
                footer: "Jenkins CI/CD Pipeline",
                link: env.BUILD_URL,
                result: "FAILURE",
                title: "❌ Pipeline Falhou",
                webhookURL: env.DISCORD_WEBHOOK
            )
        }
        unstable {
            echo '⚠️ Build instável!'
            
            discordSend(
                description: """
**⚠️ Build Instável!**
🚀 **Job:** ${JOB_NAME}
🔢 **Build:** #${BUILD_NUMBER}
⏱️ **Duração:** ${currentBuild.durationString}
🔍 **Verificar logs:** ${BUILD_URL}console
""",
                footer: "Jenkins CI/CD Pipeline",
                link: env.BUILD_URL,
                result: "UNSTABLE",
                title: "⚠️ Pipeline Instável",
                webhookURL: env.DISCORD_WEBHOOK
            )
        }
        aborted {
            echo '🛑 Build cancelado!'
            
            discordSend(
                description: """
**🛑 Build Cancelado!**
🚀 **Job:** ${JOB_NAME}
🔢 **Build:** #${BUILD_NUMBER}
⏱️ **Duração:** ${currentBuild.durationString}
""",
                footer: "Jenkins CI/CD Pipeline",
                link: env.BUILD_URL,
                result: "ABORTED",
                title: "🛑 Pipeline Cancelada",
                webhookURL: env.DISCORD_WEBHOOK
            )
        }
    }
}
```

Provas de que está funcionando:

![img01](../../../assets/fase06-extra-trivy/img01.png)

![img02](../../../assets/fase06-extra-trivy/img02.png)

![img03](../../../assets/fase06-extra-trivy/img03.png)

![img04](../../../assets/fase06-extra-trivy/img04.png)

![img05](../../../assets/fase06-extra-trivy/img05.png)

![img06](../../../assets/fase06-extra-trivy/img06.png)

![img07](../../../assets/fase06-extra-trivy/img07.png)

![img08](../../../assets/fase06-extra-trivy/img08.png)

![img09](../../../assets/fase06-extra-trivy/img09.png)
