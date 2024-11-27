# Configuración HAProxy
Omitimos partes repetidas

```
backend backend_servers
    balance roundrobin # Utilizamos RoundRobin por igualdad en la medicion
    server server1 127.0.0.1:8081 check weight 3 # Maneja el 50% del trafico
    server server2 127.0.0.1:8082 check weight 1 # Maneja el 16,5% del trafico
    server server3 127.0.0.1:8083 check weight 2 # Maneja el 33,3% del trafico
```

# Configuración Nginx

```
upstream backend_servers { # Utiliza RoundRobin como unico metodo
    server 127.0.0.1:8081 weight=3; # Maneja el 50% del trafico
    server 127.0.0.1:8082 weight=1; # Maneja el 16,5% del trafico
    server 127.0.0.1:8083 weight=2; # Maneja el 33,3% del trafico
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
