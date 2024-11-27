# Configuración HAProxy

```
global
    log stdout format raw local0
    maxconn 4096
    daemon

defaults
    log     global
    option  httplog
    option  dontlognull
    retries 3
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http_in
    bind *:80
    default_backend servers

backend servers
    balance source  # Esto configura el balanceo con el algoritmo ip-hash
    server server1 172.0.0.1:8001 check
    server server2 172.0.0.1:8002 check
    server server3 172.0.0.1:8003 check
```

# Configuracion Nginx

```
upstream backend_servers {
    ip_hash;
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
