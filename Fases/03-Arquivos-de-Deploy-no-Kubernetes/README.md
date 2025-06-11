# Fase 3: Deploy no Kubernetes

Este documento detalha o processo de deployment da aplicação containerizada no cluster Kubernetes local.

## 📋 Visão Geral

Nesta fase, a imagem Docker criada na Fase 2 será deployada no Kubernetes, incluindo:
- Criação de manifests YAML para deployment e service
- Deploy no cluster Kubernetes local
- Exposição da aplicação via NodePort ou port-forward
- Validação do funcionamento da aplicação

## 🎯 Objetivos

- [ ] Criar arquivo YAML de Deployment
- [ ] Criar arquivo YAML de Service
- [ ] Aplicar manifestos no cluster Kubernetes
- [ ] Expor aplicação em localhost:30001
- [ ] Validar funcionamento da aplicação via Kubernetes

## 🛠️ Pré-requisitos

### Software Necessário
- **Kubernetes local** (Rancher Desktop ou Docker Desktop)
- **kubectl** configurado
- **Imagem Docker** publicada no Docker Hub (Fase 2 concluída)

### Validação do Ambiente
```bash
# Verificar cluster Kubernetes
kubectl cluster-info
kubectl get nodes

# Verificar acesso à imagem Docker
docker pull SEU_USUARIO/fastapi-hello:latest
```

## 📁 Estrutura de Arquivos

```
projeto/
├── k8s/
│   ├── deployment.yaml        # ← Arquivo a ser criado
│   ├── service.yaml          # ← Arquivo a ser criado
│   └── kustomization.yaml    # ← Opcional: para organização
├── assets/
│   └── fase03/
│       ├── img01.png         # ← Screenshot deployment.yaml
│       ├── img02.png         # ← Screenshot service.yaml
│       ├── img03.png         # ← Screenshot kubectl apply deployment
│       ├── img04.png         # ← Screenshot kubectl apply service
│       ├── img05.png         # ← Screenshot kubectl get pods
│       ├── img06.png         # ← Screenshot kubectl get services
│       ├── img07.png         # ← Screenshot aplicação funcionando
│       └── img08.png         # ← Screenshot GitHub commit
└── README.md
```

## ☸️ Configuração do Kubernetes

### 1. Criação do Deployment YAML

**Localização:** `k8s/deployment.yaml`

```yaml
# TODO: Criar deployment completo
# Exemplo de estrutura:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fastapi-hello-deployment
  labels:
    app: fastapi-hello
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fastapi-hello
  template:
    metadata:
      labels:
        app: fastapi-hello
    spec:
      containers:
      - name: fastapi-hello
        image: SEU_USUARIO/fastapi-hello:latest
        ports:
        - containerPort: 8000
        # TODO: Adicionar recursos, health checks, etc.
```

**Screenshot do deployment.yaml:**
![Deployment YAML](../../assets/fase03/img01.png)

### 2. Criação do Service YAML

**Localização:** `k8s/service.yaml`

```yaml
# TODO: Criar service completo
# Exemplo de estrutura:
apiVersion: v1
kind: Service
metadata:
  name: fastapi-hello-service
  labels:
    app: fastapi-hello
spec:
  type: NodePort
  ports:
  - port: 8000
    targetPort: 8000
    nodePort: 30001
  selector:
    app: fastapi-hello
```

**Screenshot do service.yaml:**
![Service YAML](../../assets/fase03/img02.png)

### 3. Aplicação do Deployment

```bash
# Navegar para o diretório k8s
cd k8s

# Aplicar deployment
kubectl apply -f deployment.yaml

# Verificar status do deployment
kubectl get deployments
kubectl get pods -l app=fastapi-hello
```

**Screenshot da aplicação do deployment:**
![Kubectl Apply Deployment](../../assets/fase03/img03.png)

**Screenshot do status dos pods:**
![Kubectl Get Pods](../../assets/fase03/img05.png)

### 4. Aplicação do Service

```bash
# Aplicar service
kubectl apply -f service.yaml

# Verificar status do service
kubectl get services
kubectl get svc fastapi-hello-service
```

**Screenshot da aplicação do service:**
![Kubectl Apply Service](../../assets/fase03/img04.png)

**Screenshot do status dos services:**
![Kubectl Get Services](../../assets/fase03/img06.png)

## 🌐 Acesso à Aplicação

### Opção 1: NodePort (Recomendado)

```bash
# Verificar porta do NodePort
kubectl get svc fastapi-hello-service

# Acessar aplicação
curl http://localhost:30001/docs
```

### Opção 2: Port-forward (Alternativo)

```bash
# Criar port-forward
kubectl port-forward svc/fastapi-hello-service 30001:8000

# Acessar aplicação
curl http://localhost:30001/docs
```

**Screenshot da aplicação funcionando:**
![Aplicação Funcionando](../../assets/fase03/img07.png)

## ✅ Validação e Testes

### Comandos de Verificação

```bash
# Verificar status geral
kubectl get all -l app=fastapi-hello

# Verificar logs dos pods
kubectl logs -l app=fastapi-hello

# Testar endpoints
curl http://localhost:30001/
curl http://localhost:30001/docs
curl http://localhost:30001/health  # Se disponível
```

### Testes Funcionais

- [ ] Aplicação responde em `http://localhost:30001`
- [ ] Documentação Swagger acessível em `/docs`
- [ ] Todas as rotas da API funcionando
- [ ] Múltiplas réplicas rodando (se configurado)
- [ ] Logs dos containers sem erros

## 🔧 Comandos Úteis

### Debugging
```bash
# Descrever recursos
kubectl describe deployment fastapi-hello-deployment
kubectl describe service fastapi-hello-service
kubectl describe pod POD_NAME

# Verificar logs
kubectl logs -f deployment/fastapi-hello-deployment

# Acessar container
kubectl exec -it POD_NAME -- /bin/bash
```

### Limpeza
```bash
# Remover recursos
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml

# Ou remover por label
kubectl delete all -l app=fastapi-hello
```

## 📝 Versionamento no GitHub

### 1. Commit dos arquivos Kubernetes

```bash
# Adicionar arquivos Kubernetes
git add k8s/

# Commit das mudanças
git commit -m "feat: add Kubernetes deployment and service manifests"

# Push para o repositório
git push origin main
```

**Screenshot do commit no GitHub:**
![GitHub Commit](../../assets/fase03/img08.png)

## 🔧 Troubleshooting

### Problemas Comuns

**Pods não iniciam:**
- [ ] Verificar imagem Docker existe e é acessível
- [ ] Conferir recursos disponíveis no cluster
- [ ] Validar sintaxe do YAML
- [ ] Verificar logs: `kubectl logs POD_NAME`

**Service não responde:**
- [ ] Confirmar seletores do service correspondem aos labels do pod
- [ ] Verificar porta target correta
- [ ] Testar conectividade: `kubectl port-forward`

**NodePort não acessível:**
- [ ] Verificar se porta 30001 está livre
- [ ] Confirmar configuração do cluster local
- [ ] Validar firewall/proxy settings

### Comandos de Diagnóstico

```bash
# Verificar conectividade interna
kubectl run test-pod --image=busybox --rm -it -- sh
# Dentro do pod: wget -qO- http://fastapi-hello-service:8000

# Verificar DNS interno
kubectl run test-dns --image=busybox --rm -it -- nslookup fastapi-hello-service
```

## 📊 Entregáveis

| Item | Status | Evidência |
|------|--------|-----------|
| Deployment YAML criado | ⏳ | Screenshot + arquivo no GitHub |
| Service YAML criado | ⏳ | Screenshot + arquivo no GitHub |
| Deploy no Kubernetes | ⏳ | Screenshots kubectl get pods/services |
| Aplicação exposta localhost:30001 | ⏳ | Screenshot da aplicação funcionando |
| Versionamento GitHub | ⏳ | Commit com manifestos K8s |

## 🎯 Configurações Opcionais

### Melhorias Recomendadas

**Health Checks:**
```yaml
# Adicionar ao deployment.yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8000
  initialDelaySeconds: 30
readinessProbe:
  httpGet:
    path: /health
    port: 8000
  initialDelaySeconds: 5
```

**Resource Limits:**
```yaml
# Adicionar ao deployment.yaml
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "200m"
```

**ConfigMap/Secrets:**
```bash
# Exemplo para configurações
kubectl create configmap app-config --from-literal=ENV=production
```

## 🔗 Links Úteis

- **Repositório GitHub:** [Inserir link do repositório]
- **Imagem Docker Hub:** [Inserir link da imagem]
- **Aplicação Local:** http://localhost:30001
- **Documentação Kubernetes:** https://kubernetes.io/docs/

## 📞 Próximos Passos

Com o deploy no Kubernetes concluído, você estará pronto para:
- **Fase 4:** Configuração de Helm Charts
- **Fase 5:** CI/CD com GitHub Actions
- **Fase 6:** Monitoramento com Prometheus/Grafana
- **Fase 7:** Configuração de Ingress

---

**Data de criação:** [Inserir data]  
**Última atualização:** [Inserir data]  
**Responsável:** [Seu nome]