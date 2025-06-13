> [!IMPORTANT]
> Ao ativar o ngrok, mantenha o terminal aberto, pois fechá-lo encerrará o túnel.

1. Tenha o ngrok instalado e faça ele direcionar para a porta onde está o jenkins (no meu caso, ele estava dentro da VM):

![img01](../../../assets/fase07-extra-webhook-github/img01.png)

```sh
ngrok http http://192.168.121.152:8080/
```

2. Copie o link que foi gerado:

![img02](../../../assets/fase07-extra-webhook-github/img02.png)

3. No github, vá em settings

![img03](../../../assets/fase07-extra-webhook-github/img03.png)

4. Clique em webhooks

![img04](../../../assets/fase07-extra-webhook-github/img04.png)

5. Clique em Add webhook

![img05](../../../assets/fase07-extra-webhook-github/img05.png)

6. Cole o link gerado pelo ngrok e adicione `/github-webhook/`. Depois, selecione application/json

![img06](../../../assets/fase07-extra-webhook-github/img06.png)

7. Clique em Add webhook

![img07](../../../assets/fase07-extra-webhook-github/img07.png)

8. Confira se está com um certinho, de que está funcionando corretamente

![img08](../../../assets/fase07-extra-webhook-github/img08.png)

9. Clique no `.` do seu teclado para abrir o vscode no navegador

![img09](../../../assets/fase07-extra-webhook-github/img09.png)

10. Altere uma parte do seu código

![img10](../../../assets/fase07-extra-webhook-github/img10.png)

11. Commite a alteração

![img11](../../../assets/fase07-extra-webhook-github/img11.png)

12. O jenkins irá buildar automaticamente

![img12](../../../assets/fase07-extra-webhook-github/img12.png)
