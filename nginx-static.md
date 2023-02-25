# NGinx Configuration
Default config file for Nginx 

```
server {

        server_name example.com;
        root /var/www/html/;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        listen [::]:443 ssl ipv6only=on; # managed by Certbot
        listen 443 ssl; # managed by Certbot
        ssl_certificate /etc/letsencrypt/live/eample.com/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}



server {
        listen 80 default_server;
        listen [::]:80 default_server;

        server_name example.com;


        if ($host = example.com) {
                return 301 https://$host$request_uri;
        } # managed by Certbot


        return 404; # managed by Certbot
}
```
