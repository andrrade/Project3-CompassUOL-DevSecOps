# Fase 1: Preparação do projeto

Nesta fase, execute as seguintes atividades:

1. Crie um repositório no GitHub para hospedar a aplicação de exemplo.
2. Crie uma conta no Docker Hub.
3. Verifique o acesso ao cluster Kubernetes local.
4. Valide a execução local da aplicação com `uvicorn`.

**Entregáveis:** Código rodando localmente, repositório GitHub criado e ambiente preparado.

---

Para este projeto, utilize o sistema operacional [Pop!\_OS](https://system76.com/pop/).

### Passos:

1. Crie um repositório no GitHub:
   [Como criar um repositório](https://docs.github.com/pt/repositories/creating-and-managing-repositories/creating-a-new-repository)

2. Crie uma conta no Docker Hub:
   [Como criar conta no Docker Hub](https://docs.docker.com/accounts/create-account/)

3. Instale e configure o [Rancher Desktop](https://docs.rancherdesktop.io/getting-started/installation/) para usar o Kubernetes localmente.

4. Teste o Kubernetes localmente com os comandos:

```sh
kubectl version --client
kubectl get pods
```

![Imagem 01](../../assets/fase01/img01.png)
![Imagem 02](../../assets/fase01/img02.png)
![Imagem 03](../../assets/fase01/img03.png)

---

5. Valide a execução local com `uvicorn`:

* Clone apenas a pasta `backend` do repositório:

  ```
  git clone --depth 1 --filter=blob:none --sparse https://github.com/andrrade/Project3-CompassUOL-DevSecOps.git
  cd Project3-CompassUOL-DevSecOps
  git sparse-checkout set backend
  cd backend
  ```

* Instale o Python (confirme a versão com):

  ```
  python3 --version
  ```

* Crie e ative o ambiente virtual:

  ```
  python3 -m venv venv
  source venv/bin/activate
  ```

  *Se usar shell fish, ative com:*

  ```
  source venv/bin/activate.fish
  ```

![Imagem 04](../../assets/fase01/img04.png)

* Instale as dependências:

  ```
  pip install -r requirements.txt
  ```

![Imagem 05](../../assets/fase01/img05.png)

* Execute a aplicação com:

  ```
  python -m uvicorn main:app --reload
  ```

![Imagem 06](../../assets/fase01/img06.png)

* Abra no navegador o endereço:

  ```
  http://localhost:8000/docs
  ```

![Imagem 07](../../assets/fase01/img07.png)

* Teste todas as rotas disponíveis (não feche o terminal enquanto testa).

---

6. Exemplo de teste de rotas e respostas:

![Imagem 08](../../assets/fase01/img07.png)
![Imagem 09](../../assets/fase01/img07.png)
![Imagem 10](../../assets/fase01/img10.png)
![Imagem 11](../../assets/fase01/img11.png)

---

Se todas as requisições responderem com status **200 OK**, significa que o backend está rodando normalmente de forma local.
