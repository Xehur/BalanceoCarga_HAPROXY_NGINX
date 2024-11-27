# Configuración HAProxy
Omitimos partes repetidas

```
backend backend_servers
    balance uri # Balanceo basado en hash de la URI
    hash-type consistent # Asegura consistencia si se agregan/eliminan servidores
    server server1 127.0.0.1:8081 check
    server server2 127.0.0.1:8082 check
    server server3 127.0.0.1:8083 check
```

# Configuración Nginx

```
upstream backend_servers {
    hash $http_user_agent consistent; # Balanceo basado en hash del User-Agent
    server 127.0.0.1:8081;
    server 127.0.0.1:8082;
    server 127.0.0.1:8083;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend_servers/;
    proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
