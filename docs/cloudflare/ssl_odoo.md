# SSL Odoo + Cloudflare

Proceso SSL Odoo Cloudflare  
1. Montado __odoo16002__ via Odoofy.  
__$root__: `/usr/bin/odoofy build odoo.json`  
2. Montado tunnel cloudflare en maquina para direccionar 16002.xxx.es ➝ 127.0.0.1:16002  
3. Instalar nginx   
`sudo apt -y install nginx`  
`sudo nano /etc/nginx/sites-enabled/16002.___.es`  
4. Nginx odoo proxy config  
`sudo nginx -t` _(Test config)_   
`systemctl restart nginx`  
5. Odoo cert creation  
`sudo openssl genpkey -algorithm RSA -out /etc/ssl/private/odoo_cert_key.key -aes256`  
__Odoo$443!__  
`sudo openssl req -new -key /etc/ssl/private/odoo_cert_key.key -out /etc/ssl/certs/odoo_cert.csr`  
`openssl x509 -req -days 365 -in /etc/ssl/certs/odoo_cert.csr -signkey /etc/ssl/private/odoo_cert_key.key -out /etc/ssl/certs/certificate.crt`
`sudo openssl rsa -in /etc/ssl/private/odoo_cert_key.key -out /etc/ssl/private/odoo_cert_pub.key`

### Nginx odoo proxy config
```nginx
upstream odoo16 {
    server 127.0.0.1:16002;
}

upstream odoochat {
    server 127.0.0.1:16002;
}

server {
    listen 80;
    server_name 16002.___.es;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name 16002.___.es;
    ssl_certificate /etc/ssl/certs/certificate.crt;
    ssl_certificate_key /etc/ssl/private/odoo_cert_key.key;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSAAES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384';
    access_log /var/log/nginx/odoo.access.log;
    error_log /var/log/nginx/odoo.error.log;
    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    location / {
        proxy_pass http://odoo16;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }

    location /longpolling {
        proxy_pass http://odoochat;
    }

    location ~* /web/static/ {
        proxy_cache_valid 200 60m;
        proxy_buffering on;
        expires 864000;
        proxy_pass http://odoo16;
    }
}
```

<style>
.search-results {
  display: none;
}
</style>