# munin_server

```
apt update
apt upgrade

ssh:
nano /etc/ssh/sshd_config 
Port xx22
PubkeyAuthentication yes
PasswordAuthentication no

wireguard:
apt install wireguard wireguard-dkms wireguard-tools

munin:
apt install munin munin-node munin-plugins-extra libwww-perl tcpdump ufw


fail2ban:
apt install fail2ban
nano /etc/fail2ban/jail.d/defaults-debian.conf
port = xx22
service fail2ban restart


nginx:
apt install nginx
rm /etc/nginx/sites-enabled/default 
nano /etc/nginx
server {
  server_name xxx.clients.your-server.de;
  
  access_log on;
  access_log /var/log/nginx/access.log main;
 
 location / {
#    access_log on;
#    access_log /var/log/nginx/domain.access.log main;
    allow x.x.0.0/16;
    allow x.x.x.x;
    deny all;
    root /var/cache/munin/www;
    auth_basic "Munin HTTP Basic Auth";
    auth_basic_user_file /etc/apache2/.htpasswd;
  }

  listen 443 ssl; # managed by Certbot
  ssl_certificate /etc/letsencrypt/live/xxx.clients.your-server.de/fullchain.pem; # managed by Certbot
  ssl_certificate_key /etc/letsencrypt/live/xxx.clients.your-server.de/privkey.pem; # managed by Certbot
  include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
   access_log on;
   access_log /var/log/nginx/access.log main;

    if ($host = xxx.clients.your-server.de) {
        return 301 xxx.clients.your-server.de$request_uri;
    } # managed by Certbot

    if ($host = x.x.x.x) { # server ip
        return 301 xxx.clients.your-server.de$request_uri;
    }

  listen 80;
  server_name xxx.clients.your-server.de;
    return 404; # managed by Certbot
}

server {
  access_log on;
  access_log /var/log/nginx/access.log main;
  
    if ($host = x.x.x.x) { # server ip
        return 301 xxx.clients.your-server.de$request_uri;
    }

  listen 443;
  server_name x.x.x.x; # server ip
    return 404;
}

server {
  server_name 127.0.0.1;
  listen 127.0.0.1:80;
  location /nginx_status {
    stub_status on;
    access_log off;
    allow 127.0.0.1;
    deny all;
  }
}
```
