# 3cx-nginx-webclient-as-root-path

Instructions to set webclient as default app when fire to root domain, and define /admin as admin path, type https://mycompany.my3cx.com and return webclient login page instead admin login page.

Type /admin to access the admin console.

> You will need root privilege to make this changes (Tested only on linux server with 3CX v16)

## Configurations steps

### Connect over SSH to 3CX server

```bash
# Create a symbolic link
sudo ln -s /var/lib/3cxpbx/Data/Http/wwwroot admin
```

### Then edit the nginx config file /etc/nginx/sites-enabled/3cxpbx

```bash
# Edit with your preferred text editor
sudo nano /etc/nginx/sites-enabled/3cxpbx
```

> **Attention!** Errors in this file may turn webclient and admin console inaccessible, be careful!

#### Near line 32, add the follow code block, [example here](https://raw.githubusercontent.com/abelmferreira/3cx-nginx-webclient-as-root-path/master/3cxpbx.conf).

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

### Save, test and reload nginx server

```bash
# Sintax test
nginx -t

# Reload nginx
nginx -s reload
```
