Tenha o ngrok instalado e faça ele direcionar para a porta onde está o jenkins (no meu caso, ele estava dentro da VM):

```sh
ngrok http http://192.168.121.152:8080/
```

![img01](../../../assets/fase07-extra-webhook-github/img01.png)

Copie o link que foi gerado:

![img02](../../../assets/fase07-extra-webhook-github/img02.png)

No github, vá em settings

![img03](../../../assets/fase07-extra-webhook-github/img03.png)

Clique em webhooks e Add webhook

![img04](../../../assets/fase07-extra-webhook-github/img04.png)

Cole o link gerado pelo ngrok e adicione /github-webhook/ no final

![img05](../../../assets/fase07-extra-webhook-github/img05.png)

Selecione application/json e depois clique em Add webhook

![img06](../../../assets/fase07-extra-webhook-github/img06.png)

Confira se está com um certinho, de que está funcionando corretamente

![img07](../../../assets/fase07-extra-webhook-github/img07.png)

Clique no `.` do seu teclado para abrir o vscode no navegador

![img08](../../../assets/fase07-extra-webhook-github/img08.png)

Altere uma parte do seu código

![img09](../../../assets/fase07-extra-webhook-github/img09.png)

Commite a alteração

![img10](../../../assets/fase07-extra-webhook-github/img10.png)

O jenkins irá buildar automaticamente

![img11](../../../assets/fase07-extra-webhook-github/img11.png)

![img12](../../../assets/fase07-extra-webhook-github/img12.png)
