## Server Setup

### 1. Install Server Software
- nginx
- git
- docker-compose
- application code

**CHECK**: IP address points to Nginx defaut page

### 2. DNS
Register Domain name and set A records to point to static IP of server hosting API

**CHECK**: domain name points to Nginx default page

### 3. OpenSSL Certbot 
SSL into server and run CertBot as per [instructions](https://certbot.eff.org/instructions)

If correctly installed should get this:

```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/drone-flying-online.space/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/drone-flying-online.space/privkey.pem
This certificate expires on 2023-04-15.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

### 4. Configuration file `/etc/nginx/sites-enabled/demo.conf`:

```
## Redirect http to https
server {
   listen 80;
   server_name example.yourdomain.com;
   return 307 https://example.yourdomain.com$request_uri;
 }
 
## Redirect www to https
server {
   listen 80;
   server_name www.example.yourdomain.com;
   return 307 https://example.yourdomain.com$request_uri;
}

 
server {
   listen 443 ssl;
   server_name example.yourdomain.com;
   ssl_certificate  /path/to/your/certificate;
   ssl_certificate_key  /path/to/your/certificate/key;
   ssl_prefer_server_ciphers on;

   ## Forward through original IP
   set_real_ip_from 0.0.0.0/0;
   real_ip_header X-Real-IP;
   real_ip_recursive on;

   location / {
        proxy_pass http://localhost:8080;

        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Host $remote_addr;
        proxy_set_header        X-Forwarded-Proto $scheme;
   }
}

```
Reload the Nginx server: `nginx -s reload`

**CHECK**: https://example.domain.com

### 5. Setup Firewall

Confirm ufw installed and disabled
```
ufw status
```

Reset all firewall rules
```
ufw reset
```

set defaults for incoming and outgoing
```
ufw default deny incoming
ufw default allow outgoing
```

enable SSH, https, http and nginx
```
ufw allow ssh
ufw allow 80
ufw allow 442
ufw allow 'Nginx Full'
```

Enable firewall and check 
```
ufw enable -y  && ufw status
```
