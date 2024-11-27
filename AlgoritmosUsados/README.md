# ALGORTIMOS EMPLEADOS 

# RoundRobin (RR)

 Distribuye las solicitudes de forma secuencial y equitativa entre todos los servidores que se encuentren disponibles. Es **simple** de implementar y funciona bien cuando todos los servidores tienen mismas capacidades. **Sin embargo**, no tiene en cuenta la carga actual de los  servidores.

# IP HASH

 Utiliza la dirección IP base del cliente para decidir a que servidor backend enviar la solicitud. **Calcula un hash especifico para cada cliente** y en base a ese hash numérico, se asignan las solicitudes a los servidores. Por ejemplo hash 1 está asignada a servidor 1 -> Ya sabe que si el hash es 1, enviar la solicitud a servidor 1.  
 
 Como **ventaja**, garantiza que cada cliente siempre se le asignará el mismo servidor, lo cual es útil si las sesiones son persistentes. Como **desventaja**, si un servidor falla puede producir desequilibrio en la carga ya que el hash asignado a ese servidor no valdría, además   de que al igual que RR, no toma en cuenta la carga actual de los servidores. 
 
# LEAST-CONNECTIONS
  Su funcionamiento es **simple**, se basa enviar las solicitudes al servidor con **menor cantidad de conexiones activas**. Es dinámico y maneja bien la carga pero puede suponer un gran costo adicional en términos de cálculo si hay demasiados servidores. 

# WEIGHT
  Similar al RR, solo que ahora permite asignar pesos a los servidores. Contra más alto es el peso, significa que ese servidor es el que tiene mayor prioridad de recibir solicitudes, por lo que lo suyo es que el peso escale acorde a la potencia del servidor. Es simple 
  y permite **aprovechar los recursos de los servidores** pero no tiene en cuenta la carga dinámica en tiempo real.

# HASH (consistente)
  Parecido a IP HASH solo que emplea una **clave para calcular el hash en vez de la IP del usuario**. Reduce el trabajo de reubicar las solicitudes si un servidor cambia de localización y al ser consistente, supone una **mejora sobre el hash normal**. 
  Sin embargo,suponebastante trabajo el hecho de eliminar varios servidores y recalcular sus hash redirigiendo a su vez las solicitudes a otros servidores.


| **Algoritmo**          | **Descripción**                                                                                     | **Ventajas**                                                                                   | **Desventajas**                                                                                   | **Casos de Uso Comunes**                                               |
|-------------------------|---------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| **Round Robin**         | Distribuye las solicitudes de forma secuencial entre los servidores en orden cíclico.             | - Simple de implementar.<br>- Distribución equitativa si los servidores son iguales.          | - No considera la carga actual.<br>- Puede saturar servidores si la carga no es uniforme.         | - Servidores con capacidades similares.<br>- Escenarios simples.       |
| **IP Hash**             | Asigna solicitudes al servidor en función de un hash de la IP del cliente.                       | - Persistencia de cliente-servidor.<br>- Reenvía siempre al mismo servidor para una misma IP. | - Desequilibrio si la distribución de IPs no es uniforme.<br>- Reasignación masiva al cambiar nodos. | - Persistencia de sesión.<br>- Sistemas distribuidos.                  |
| **Least Connections**   | Envía las solicitudes al servidor con menos conexiones activas en ese momento.                   | - Equilibra la carga en tiempo real.<br>- Eficiente con tiempos de procesamiento variables.    | - Mayor carga de cálculo.<br>- No considera la potencia de los servidores (sin pesos adicionales). | - Tráfico irregular.<br>- Servidores de distintas capacidades.         |
| **Weighted Round Robin**| Extensión de Round Robin que asigna más solicitudes a servidores con mayor peso (capacidad).     | - Utiliza mejor los recursos.<br>- Ideal para servidores heterogéneos.                       | - No considera la carga actual.<br>- Necesita configuración manual de los pesos.                  | - Servidores con diferentes capacidades de hardware.                   |
| **Hash**                | Usa un valor (IP, cookie, etc.) para calcular un hash que asigna la solicitud a un servidor.     | - Mapeo consistente.<br>- Persistencia del cliente en el mismo servidor.                     | - Desequilibrio si los datos no están distribuidos uniformemente.<br>- Reasignaciones masivas sin *consistent hashing*. | - Persistencia de cliente.<br>- Sistemas de cacheo o almacenamiento distribuido. |

---
