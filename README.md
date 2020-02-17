# 3cx-nginx-webclient-as-root-path

> Instructions to set webclient as default app when fire to root domain, and define /admin as admin path, type https://mycompany.my3cx.com and return webclient login page instead admin login page.
> (Tested on a linux server with 3CX v16 only) [English here](https://raw.githubusercontent.com/abelmferreira/3cx-nginx-webclient-as-root-path/master/README.en.md)

Instruções para definir o webclient como app padrão quando usuário digitar o endereço root do seu servidor 3cx, por exemplo quando digitar https://minhaempresa.my3cx.com em vez de cair no login do painel administrativo, o acesso será redirecionado para o login do webclient.

Reduzindo os chamados de usuários que não se atentam quanto a URL e reclamam que não estão conseguindo fazer login.

Para acessar o painel administrativo terá que digitar /admin.

> Para seguir com as configurações é necessário ter acesso root ao servidor 3cx (Somente servidores Linux)

## Configurações

### Acesse o servidor 3cx por SSH

```bash
# Crie um link simbólico com o comando abaixo
sudo ln -s /var/lib/3cxpbx/Data/Http/wwwroot admin
```

### Em seguida edite o arquivo /etc/nginx/sites-enabled/3cxpbx

```bash
# Edite o arquivo com seu editor preferido
sudo nano /etc/nginx/sites-enabled/3cxpbx
```

> **Atenção!** Os erros neste arquivo podem tornar o cliente da web e o console de administração inacessíveis, tenha cuidado!

#### Adicione os blocos de código abaixo no mesmo nível dos outros blocos locations existentes, aproximadamente na linha 32, [arquivo de exemplo neste link](https://raw.githubusercontent.com/abelmferreira/3cx-nginx-webclient-as-root-path/master/3cxpbx.conf).

```bash
location /admin {
    include "/var/lib/3cxpbx/Bin/nginx/conf/shared-headers.conf";
    index index.html;
    root "/var/lib/3cxpbx/Data/Http/wwwroot/admin";
    try_files $uri $uri/ @proxy;
}

location = / {
    return 307 $scheme://$host/webclient;
}
```

### Salve, teste se a sintaxe esta correta e recarregue o nginx para aplicar as alterações

```bash
# Teste a sintaxe antes
sudo nginx -t

# Recarregue o nginx
sudo nginx -s reload
```
