server {
    listen 443 ssl;
    server_name sirghiinicolai.com www.sirghiinicolai.com;

    root /var/www/sirghiinicolai.com/dist;
    index index.html;

    # SSL (managed by Certbot)
    ssl_certificate /etc/letsencrypt/live/sirghiinicolai.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/sirghiinicolai.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    # Main site
    location / {
        try_files $uri $uri/ =404;
    }

    # Webhook endpoint (CI/CD)
    location /webhook {
        proxy_pass http://127.0.0.1:9000/hooks/sirghii-deploy;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 80;
    server_name sirghiinicolai.com www.sirghiinicolai.com/dist;

    # Force redirect to HTTPS
    return 301 https://$host$request_uri;
}