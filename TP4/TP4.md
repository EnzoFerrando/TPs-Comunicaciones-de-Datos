## Ejercicio3: LAN en un avión

i) Clase Turista: acceso solo a un sistema de entretenimiento (server local)
ii) Clase Business: acceso a sistema de entretenimiento e internet.
iii) Administración: acceso total.

### Diagrama Logico:

![Diagrama Lógico](images/DiagramaLogico.png)

### Diagrama Fisico

![Diagrama Físico](images/DiagramaFisico.png)

### Configuracion del switch

![Configuración del Switch](images/ConfigSw.png)


### Configuracion del Router Aircraft

```text
Router#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  up                    up 
GigabitEthernet0/0/0.1010.10.10.1      YES manual up                    up 
GigabitEthernet0/0/0.2010.10.20.1      YES manual up                    up 
GigabitEthernet0/0/0.9910.10.99.1      YES manual up                    up 
GigabitEthernet0/0/1   200.0.0.1       YES manual up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  down                  down 
GigabitEthernet0/0/3   unassigned      YES unset  down                  down 
```

### Configuracion del ISP

Probando con ping desde ISP hasta el Router Aircraft:

```text
Router#ping 200.0.0.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 200.0.0.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```


### Configuracion del server

En los servicios del server, editamos el archivo que dice index.html, con el contenido que queremos ver:

```text
<html>
  <h1>AirConnect Entertainment</h1>
  <p>Bienvenido a bordo. Disfrute nuestras películas y música.</p>
</html>

```

### Configuracion de las notebooks:

En cada notebook conectada al switch, seleccionamos DHCP, para que automaticamente se genere un ip.

### Pruebas:

PC Turista hacia servidor:

```text
C:\>ping 10.10.99.10

Pinging 10.10.99.10 with 32 bytes of data:

Reply from 10.10.99.10: bytes=32 time<1ms TTL=127
Reply from 10.10.99.10: bytes=32 time<1ms TTL=127
Reply from 10.10.99.10: bytes=32 time<1ms TTL=127
Reply from 10.10.99.10: bytes=32 time<1ms TTL=127

Ping statistics for 10.10.99.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

PC Turista accediendo al HTTPS del servidor:

![PC Turista HTTPS](images/PCTuristaHTTPS.png)

PC Turista a internet (bloqueado):

```text
C:\>ping 8.8.8.8

Pinging 8.8.8.8 with 32 bytes of data:

Request timed out.
Request timed out.
Request timed out.
Request timed out.

Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

PC Business accediendo al HTTPS del servidor:

![PC Business HTTPS](images/PCBusinessHTTPS.png)

PC Business a internet (permitido):

```text
C:\>ping 8.8.8.8

Pinging 8.8.8.8 with 32 bytes of data:

Reply from 8.8.8.8: bytes=32 time<1ms TTL=254
Reply from 8.8.8.8: bytes=32 time<1ms TTL=254
Reply from 8.8.8.8: bytes=32 time<1ms TTL=254
Reply from 8.8.8.8: bytes=32 time<1ms TTL=254

Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

Ping entre admin y todos:

```text
C:\>ping 10.10.99.10

Pinging 10.10.99.10 with 32 bytes of data:

Reply from 10.10.99.10: bytes=32 time<1ms TTL=128
Reply from 10.10.99.10: bytes=32 time<1ms TTL=128
Reply from 10.10.99.10: bytes=32 time<1ms TTL=128
Reply from 10.10.99.10: bytes=32 time<1ms TTL=128

Ping statistics for 10.10.99.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms


C:\>ping 8.8.8.8

Pinging 8.8.8.8 with 32 bytes of data:

Reply from 8.8.8.8: bytes=32 time<1ms TTL=254
Reply from 8.8.8.8: bytes=32 time<1ms TTL=254
Reply from 8.8.8.8: bytes=32 time<1ms TTL=254
Reply from 8.8.8.8: bytes=32 time<1ms TTL=254

Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms


C:\>ping 10.10.10.11

Pinging 10.10.10.11 with 32 bytes of data:

Reply from 10.10.10.11: bytes=32 time<1ms TTL=127
Reply from 10.10.10.11: bytes=32 time<1ms TTL=127
Reply from 10.10.10.11: bytes=32 time<1ms TTL=127
Reply from 10.10.10.11: bytes=32 time<1ms TTL=127

Ping statistics for 10.10.10.11:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms


C:\>ping 10.10.20.11

Pinging 10.10.20.11 with 32 bytes of data:

Reply from 10.10.20.11: bytes=32 time<1ms TTL=127
Reply from 10.10.20.11: bytes=32 time<1ms TTL=127
Reply from 10.10.20.11: bytes=32 time<1ms TTL=127
Reply from 10.10.20.11: bytes=32 time=1ms TTL=127

Ping statistics for 10.10.20.11:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```


### Conclusiones: 

La práctica permitió comprobar cómo separar el tráfico de forma simple y segura usando VLANs y un router. Con el etiquetado 802.1Q se dividieron las clases del avión en un mismo switch, mientras que las subinterfaces del router se encargaron del DHCP y del ruteo entre redes.
Por último, combinando ACLs y NAT se logró el control de acceso que pedía la consigna: Turista solo llega al servidor local sin poder salir a la red externa, mientras que Business y Admin comparten la salida a Internet traduciendo sus IPs privadas con una sola IP pública.

