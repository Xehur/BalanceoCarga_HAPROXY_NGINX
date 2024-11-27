# Guía Instalación y Ejecución

## Introducción
  Para llevar a cabo las pruebas necesarias para comprobar el balanceo de carga realizado por NGINX frente HAProxy, es necesario antes de nada instalar los siguientes paquetes. Hay que tener en cuenta que estas pruebas han sido realizadas desde un Linux nativo y de 
  forma local (usando servidores web locales creados por nosotros a modo de ejemplo). 
  
  En un entorno real donde se vea involucrado el acceso a Internet, pueden verse afectados distintos valores en las pruebas de balanceo de carga ya que es un factor que depende bastante de la calidad de la red y el tráfico de la misma. Sin embargo, los valores 
  obtenidos en esta prueba nos ayudan a comparar NGINX y HAProxy como 
  balanceadores de carga, que es el objetivo de este trabajo.

## Requisitos
  :Sistema Opertativo Ubuntu
  :Permisos de superusuario `sudo` disponibles.
  :Instalar las siguientes herramientas: `nginx`. `haproxy` y `curl` 

  Antes de nada, realizar `sudo apt update && apt upgrade` para comprobar que todos los paquetes están actualizados.
  
  Para instalar las herramientas basta con hacer: `sudo apt install -y nginx haproxy curl`. Para comprobar que se han instalado correctamente: `nginx -v`, `haproxy -v` y `curl --version`.
  
  También será necesario contar python3 para los servidores, ya que python permite crear servidores web simples de manera muy sencilla. Instalar python3: `sudo apt install python3` y comprobar instalación `python3 --version`.
  
  Por último, para obtener resultados de tiempo de las pruebas de balanceo para comparar, usaremos  el Benchmark de Apache. Se puede instalar con `sudo apt install apache2-utils`.


## Creacion Servidores
 Haremos nuestras pruebas usando 3 servidores web de ejemplo para balancear la carga entre ellos. Para crear dichos servidores, basta con crearnos un directorio al que llamaremos backend y crear un script en python el cual lo haremos redirigiendo directamente con un echo la salida del comando al script. Copiamos el primero que hemos
 creado que en nuestro caso es server1.py para crear más servidores.
 
```
mkdir -p ~/backends
cd ~/backends
echo -e "from http.server import SimpleHTTPRequestHandler, HTTPServer\nclass MyHandler(SimpleHTTPRequestHandler):\n    def do_GET(self):\n        self.send_response(200)\n        self.send_header('Content-type', 'text/plain')\n        self.end_headers()\n        self.wfile.write(b'Server running on port\\n' %  
self.server.server_address[1])\nHTTPServer(('0.0.0.0', 8081), MyHandler).serve_forever()" > server1.py
cp server1.py server2.py server3.py`
```

## Ejecución Servidores
  Para ejecutar cada servidor, basta con realizar:  `python3 <nombre_script_servidor> &`. Un ejemplo sería `python3 servidor1.py &`. Es importante añadir el '&' para que lo ejecute en segundo plano y no haga falta usar más terminales. 


## Configurar NGINX como balanceador de carga
  Primero, podemos comprobar el estado del servicio NGINX con el comando `sudo systemctl status nginx` 
  
  Después, navegamos a la ruta donde se encuentra el archivo de configuración para balancear carga de nginx, esto lo hacemos con el comando `sudo nano /etc/nginx/conf.d/loadbalancer.conf`. Se ha usado nano como editor de texto pero se podría usar el que se prefiera. 
  A continuación, agregamos este bloque al archivo de configuraciíon que estamos editando:
  
```
upstream backend_servers {
    server 127.0.0.1:8081;
    server 127.0.0.1:8082;
    server 127.0.0.1:8083;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend_servers;
    }
}
```
  Por defecto, nginx usa como algoritmo de balanceo de carga un roundrobin, para ver la configuracion de los diferentes algoritmos de carga que se usarán, referirse a [Algoritmos usados](./AlgoritmosCarga)
  
> Con lo anterior indicamos el puerto donde se levantan nuestros servidores, su IP, el puerto en el que escuchan las solicitudes, 80 para HTTP, y la localización donde deben enviarse las solicitudes (definido por la directiva proxy_pass, indicando que se envíe a backend_servers, definidos arriba).

  Guardamos y dejamos de editar el archivo y reiniciamos el servicio nginx para que se aplique la configuración con el comando: `sudo systemctl restart nginx`. Si queremos comprobar que están funcionando los servidores, realizar: `curl http://localhost/` y se mostrará   un html de bienvenida. Recordar que localhost = 127.0.0.1.
  Si realizamos varias veces dicho comando, se notará que hay cierta alternancia entre las respuestas de los 3 servidores, no siempre responde el mismo ya que estamos aplicando balanceo de carga. 

  ## Configurar HAProxy  como balanceador de carga
   Primero, podemos comprobar el estado del servicio HAProxy con el comando  `sudo systemctl status haproxy`. Para editar la configuración del balanceo de carga, accedemos y editamos el archivo haproxy.cfg con el comando `sudo nano /etc/haproxy/haproxy.cfg` 

   ```
frontend http_front
    bind *:80
    default_backend backend_servers

backend backend_servers
    balance roundrobin
    server server1 127.0.0.1:8081 check
    server server2 127.0.0.1:8082 check
    server server3 127.0.0.1:8083 check

   ```
  Guardamos y cerramos el archivo de configuración y para que esto se aplique reiniciamos el servicio con `sudo systemctl restart haproxy`. Probamos la configuración de la misma manera con `curl http://localhost/`

  ## Ejecución Pruebas
  En este apartado, haremos uso del Apanche Benchmark para realizar las pruebas y obtener los datos necesarios para comparar el balanceo de carga de ambos servicios. 

  
  






















   
   
