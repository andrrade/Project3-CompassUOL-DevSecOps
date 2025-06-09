Fase 5: Jenkins - Deploy no Kubernetes

Nesta fase o Jenkins será configurado para acessar o kubectl e acesso ao cluster local, assim como uma etapa de deploy será incluída no pipeline:

- Jenkins precisa acessar o kubectl (usar agent com kubectl e kubeconfig configurados)
- Adicionar etapa de deploy no Jenkinsfile
- Testar a pipeline completa

Entregáveis: Pipeline completo com deploy automatizado
