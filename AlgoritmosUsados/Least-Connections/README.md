# Configuración HAProxy
Ponemos solo el bloque que referencia a los servers, el resto se repite

```
backend backend_servers
    balance leastconn # Linea modificada
    server server1 127.0.0.1:8081 check
    server server2 127.0.0.1:8082 check
    server server3 127.0.0.1:8083 check
```

# Configuración Nginx

```
upstream backend_servers {
    least_conn; # Linea modificada
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
