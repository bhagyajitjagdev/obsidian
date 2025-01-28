## Docker Compose File
```yaml
services:
  traefik:
    image: traefik:v2.10
    container_name: traefik
    restart: always
    network_mode: host
    command:
      - "--api.insecure=false"
      - "--providers.file.directory=/etc/traefik/"
      - "--providers.file.watch=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.letsencrypt.acme.httpChallenge.entryPoint=web"
      - "--certificatesresolvers.letsencrypt.acme.email=help@getintelekt.ai"
      - "--certificatesresolvers.letsencrypt.acme.storage=/acme.json"
    volumes:
      - "./traefik.yml:/etc/traefik/traefik.yml"
      - "./acme.json:/acme.json"

  nginx-app:
    image: nginx:alpine
    container_name: nginx-app
    restart: always
    volumes:
      - ./websites:/usr/share/nginx/html:ro
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    networks:
      - app_network

networks:
  app_network:
    driver: bridge
```

_Create file `acme.json` with permission `chmod 600 acme.json`_
_Create file `nginx.conf` and `traefik.yml`_
_Create a folder called `websites`_

## nginx.conf
```conf
server {
    listen 80;
    server_name subdomain.domain.tld;

    root /usr/share/nginx/html/<website folder>;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    error_page 404 /index.html;
}
```
_add as many as static website you need_

## traefik.yml
```yaml
# Global configuration
entryPoints:
  web:
    address: ":80"
    # HTTP redirect
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
  websecure:
    address: ":443"

# Provider configuration
providers:
  file:
    directory: /etc/traefik/
    watch: true

# Certificate resolver configuration
certificatesResolvers:
  letsencrypt:
    acme:
      email: help@getintelekt.ai
      storage: acme.json
      httpChallenge:
        entryPoint: web

# HTTP configuration
http:
  routers:
    app:
      rule: "Host(`app.domain.tld`)"
      service: app-service
      entryPoints:
        - websecure
      tls:
        certResolver: letsencrypt

	app2:
      rule: "Host(`appw2.domain.tld`)"
      service: nginx-service
      entryPoints:
        - websecure
      tls:
        certResolver: letsencrypt

  services:
    nginx-service:
      loadBalancer:
         servers:
           - url: "http://nginx-app:80"

    app-service:
      loadBalancer:
        servers:
          - url: "http://localhost:<PORT>"
```

_the `app` will be the server running on any `port`_
_if you are serving any static files website then copy the folder into `websites` folder and add the service like `app2`_


