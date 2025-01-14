## Installing Certbot

```bash
apt install certbot python3-certbot-apache ca-certificates curl
```

## Virtual Host Configuration

```bash
nano /etc/apache2/sites-available/000-default.conf
```

### For Hosted Server

```apache
<VirtualHost *:80>
        #STUDIO API DOMAIN

        ServerName <SERVER NAME>

        <Directory /var/www/html/>
             Options Indexes FollowSymLinks
             AllowOverride All
             Require all granted
        </Directory>

        ProxyPreserveHost On

        # Http Proxy Settings for server port
        ProxyPass / http://localhost:<PORT>/
        ProxyPassReverse / http://localhost:<PORT>/

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        # WebSocket Proxy Settings for /ws path
        <Location /ws>
                ProxyPass ws://localhost:<PORT>/ws
                ProxyPassReverse ws://localhost:<PORT>/ws
        </Location>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### For Static Files

```apache
<VirtualHost *:80>
        ServerName <SERVER NAME>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/<SERVER NAME>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

_Note: Create a folder in `/var/www/html` with the same as server name and move the static contents in there._

## Module Enable

```bash
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_wstunnel
a2enmod rewrite
a2enmod headers
a2enmod ssl
```

## Install Certificate

```bash
certbot --apache
```

---
# To Add a new Certificate

## Get the existing Certificate

```bash
certbot certificates
```

### Delete the existing one

```bash
certbot delete --cert-name <CERTIFICATE NAME>
```

_Note: Make sure to update the `000-default.conf` with the new sub/domain_

```apache
RewriteEngine on
RewriteCond %{SERVER_NAME} =<SERVER NAME>
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
```

_Make sure to delete these lines_
## Rerun Certificate

```bash
certbot --apache
```
