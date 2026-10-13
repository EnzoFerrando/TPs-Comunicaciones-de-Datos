# Trabajo Práctico N°5

## Integrantes

- Enzo Nahuel Fernandez Mattio, 46225824, I.COMP
- Enzo Jesús Ferrando, 46320483, I.COMP
- Ignacio José Giraudo, 44900514, I.COMP
- Ezequiel Moreyra, 46226249, I.COMP

---

## Ejercicio 1: ICMP y primer contacto con Wireshark

#### 1.a)

ICMP es un protocolo que se usa para enviar mensajes de control y diagnóstico de la red, por ejemplo para avisar errores o comprobar si un equipo responde. No transporta los datos de una aplicación como TCP o UDP.

#### 1.b)

ICMP viaja dentro de un paquete IP. En IPv4, el campo `Protocol` vale `1` cuando el contenido es ICMP. A su vez, el paquete IP va dentro de una trama Ethernet.

#### 1.c)

`ping` envía mensajes ICMP **Echo Request** para comprobar si un destino responde. Si llegan, el destino contesta con **Echo Reply**. Se distinguen por el campo `Type`: `8` para Request y `0` para Reply.

#### 1.d)

Un mensaje Echo tiene, como mínimo, `Type`, `Code`, `Checksum`, `Identifier` y `Sequence Number`. Esos campos forman una cabecera de 8 bytes. También puede llevar datos o *payload*; en nuestro ping llevó 56 bytes.

---

### Configuración de red utilizada

Consultamos `ip addr` e `ip route` en Linux. La interfaz activa fue la Wi-Fi `wlp4s0`:

| Dato | Valor |
|---|---|
| Dirección IPv4 | `192.168.1.11` |
| Máscara | `/24` (`255.255.255.0`) |
| Gateway por defecto | `192.168.1.1` |
| MAC de `wlp4s0` | `80:30:49:d2:3a:0b` |

![Configuración de red obtenida con ip addr e ip route](images/cfg_red.png)

### Captura de los pings

Iniciamos una captura en `wlp4s0` y ejecutamos:

```bash
ping -4 -c 4 192.168.1.1
ping -4 -c 4 8.8.8.8
```

Después aplicamos el filtro de visualización `icmp`. En la captura aparecen cuatro Echo Request y cuatro Echo Reply para cada destino. Para verlos por separado sirven `icmp.type == 8`, `icmp.type == 0` e `icmp && ip.addr == 8.8.8.8`.

![Echo Request y Echo Reply en Wireshark](images/WireShark.png)

Para el análisis siguiente elegimos el Echo Request **n.º 9542** hacia `8.8.8.8` y su Echo Reply **n.º 9543**.

### Capas del Echo Request n.º 9542

| Capa | Origen o identificador | Destino | Campo que indica qué viene adentro |
|---|---|---|---|
| Ethernet II | MAC `80:30:49:d2:3a:0b` | MAC `e0:1f:ed:4f:52:f8` | `Type = 0x0800` indica IPv4. |
| Internet Protocol Version 4 | IP `192.168.1.11` | IP `8.8.8.8` | `Protocol = 1` indica ICMP. |
| Internet Control Message Protocol | Identificador `0xc278`, secuencia `1` | No tiene dirección de destino propia. | `Type = 8` indica Echo Request; no identifica otro protocolo interior. |
| Datos / payload | No tiene origen propio. | No tiene destino propio. | No hay otro protocolo; son datos del ping. |

#### Análisis de la captura

**a) MAC destino.** El Request a `8.8.8.8` salió hacia la MAC `e0:1f:ed:4f:52:f8`, que es la del gateway `192.168.1.1`. El ping al gateway usó esa misma MAC destino. La MAC sirve para llegar al siguiente equipo de la red local; la IP permite identificar el destino final, aunque esté fuera de nuestra red.

**b) Request y Reply.** Entre los paquetes 9542 y 9543 se invierten las MAC de origen y destino, y también las IP (`192.168.1.11` ↔ `8.8.8.8`). La MAC de origen del Reply es la del gateway, porque es el equipo que nos entrega esa trama en nuestra red. En ICMP cambia `Type` de `8` a `0` y cambia el checksum. En IP cambian el TTL, el identificador, el checksum y la marca `DF`, que solo aparece en el Request. Se mantienen `Type = 0x0800` en Ethernet, `Protocol = 1` y la longitud en IP, `Code = 0`, el identificador ICMP `0xc278`, la secuencia `1` y los datos. El identificador y la secuencia permiten asociar la respuesta con la solicitud correspondiente.

**c) Payload.** Está después de la cabecera ICMP. Tiene **56 bytes**: empieza con 16 bytes variables y sigue con el patrón hexadecimal `10 11 12 ... 37`. En el Reply esos 56 bytes volvieron iguales.

**d) TTL.** El Request salió con TTL **64** y el Reply de `8.8.8.8` llegó con TTL **117**. Son paquetes creados por equipos distintos y cada router reduce el TTL en uno durante el recorrido. Por eso no tienen por qué mostrar el mismo valor.

**e) Encapsulación.** Para el paquete n.º 9542, los tamaños observados fueron:

```text
Trama Ethernet: 98 bytes
└── Cabecera Ethernet II: 14 bytes
    └── Paquete IPv4: 84 bytes
        ├── Cabecera IPv4: 20 bytes
        └── Mensaje ICMP: 64 bytes
            ├── Cabecera Echo Request: 8 bytes
            └── Datos del ping: 56 bytes
```

La suma es `14 + 20 + 8 + 56 = 98` bytes, igual a la longitud que muestra Wireshark para esa trama.


---

## Ejercicio 3: TCP y UDP "a mano" con ncat

#### 3.a)
Establecer una conexión es que los dos extremos acuerden un estado compartido antes de enviar datos.
Una conexión TCP existe solo en los extremos, como estado guardado por el sistema operativo de cada host. Los cables y los routers solo ven paquetes IP sueltos y no saben que pertenecen a una conexión.

#### 3.b)
Un puerto es un número de 16 bits que indica a qué proceso dentro de un host va dirigido el tráfico. 
El par (IP, puerto) identifica un extremo de comunicación (un socket). Una conexión TCP se identifica por un par de origen y otro de destino, es decir (IP origen, puerto origen, IP destino, puerto destino).

#### 3.c) 
Si un proceso está escuchando en un puerto significa que el proceso reservó ese puerto y le pidió al sistema operativo que acepte conexiones en él. Desde ese momento, el SO responde con SYN-ACK a los SYN que llegan a ese puerto y entrega las conexiones establecidas al proceso. Si nadie escucha, el SO rechaza el intento.

#### Experimento TCP
Se abrieron dos terminales, una para el cliente y otra para el servidor. En el servidor se ejecutó `ncat -v -l 12000` y en el cliente `ncat -v 127.0.0.1 12000`. En Wireshark se filtró con `tcp.port == 12000`.

![TCP1](images/TCP_1.png)

La conexión se establece apenas se ejecuta el cliente, sin escribir nada. Los 3 paquetes que aparecen son:

```
519	cliente (47638) → servidor (12000)	[SYN]
520	servidor → cliente	[SYN, ACK]
521	cliente → servidor	[ACK]
```

Luego se enviaron mensajes desde ambas terminales:

![TCP2](images/TCP_2.png)

Cada mensaje viaja en un segmento con flags [PSH, ACK] y el otro lado lo confirma con un [ACK] sin datos (Len=0). Por ejemplo, "hola desde el cliente" tiene 21 caracteres pero el segmento muestra Len=23: los 2 bytes extra son el \r\n que agrega Windows al presionar Enter. Los números de ACK acompañan a los datos: después de recibir esos 23 bytes, el servidor responde con Ack=24, es decir, para pedir que le envien desde el byte 24.

Finalmente se cerró el cliente con Ctrl+C:

![TCP3](images/TCP_3.png)

El cliente inicia el cierre con [FIN, ACK], el servidor lo confirma con [ACK] y luego envía su propio [FIN, ACK], que el cliente confirma con [ACK]. Cada lado cierra su dirección por separado, por eso son 4 paquetes.

En total, el intercambio completo usó 15 paquetes: 3 de handshake, 8 de datos y confirmaciones (4 mensajes) y 4 de cierre.

#### Experimento UDP

Se capturó en la interfaz de loopback con el filtro `udp.port == 12001`. Se ejecutó primero el servidor con `ncat -v -u -l 12001` y luego el cliente con `ncat -v -u 127.0.0.1 12001`.

Al ejecutar el cliente, antes de escribir nada, no apareció ningún paquete en la captura. Esto es porque UDP no tiene conexión, el "Connected to 127.0.0.1:12001" que muestra ncat es solo local, el programa deja configurado el destino pero no envía nada hasta que se escribe un mensaje. En TCP, en cambio, ya se habían visto 3 paquetes de handshake en este punto.

Luego se envió un mensaje desde cada terminal:

![UDP1](images/UDP_1.png)


Cada mensaje generó un único datagrama, y no hay ningún paquete que lo confirme. 

```
194  cliente (52156) → servidor (12001)  23 bytes de datos
200  servidor (12001) → cliente (52156)  24 bytes de datos
```

Finalmente se cerró el cliente con Ctrl+C y no apareció ningún paquete nuevo.

#### a) 
En TCP vimos 3 paquetes antes de escribir: [SYN] del cliente, [SYN, ACK] del servidor y [ACK] del cliente, que forman el three-way handshake. En UDP no apareció ningún paquete, porque no tiene conexión.

#### b)
En UDP cada mensaje generó un único datagrama, y no hubo nada parecido a un ACK: cuando el servidor respondió, lo hizo con otro datagrama con datos, no con una confirmación. En TCP cada mensaje generó un segmento [PSH, ACK] con datos, seguido de un [ACK] sin datos del otro lado, así que cada mensaje costó 2 paquetes.

#### c)
El encabezado UDP tiene 4 campos de 2 bytes (puerto origen, puerto destino, longitud y checksum) y ocupa 8 bytes.

![UDP2](images/UDP_2.png)

El encabezado TCP tiene puerto origen, puerto destino, número de secuencia, número de ACK, longitud de encabezado, flags, ventana, checksum, puntero urgente y opciones, y ocupa 20 bytes (en un segmento de datos).

![TCP4](images/TCP_4.png)

#### d)
Al cerrar el cliente, en TCP hubo 4 paquetes, mientras que en UDP no apareció ningún paquete.

#### e) 
Para enviar un mensaje en un solo sentido, TCP necesitó 9 paquetes (3 de handshake, 1 de datos, 1 de ACK y 4 de cierre) y UDP necesitó 1. En las capturas completas, TCP usó 15 paquetes para 4 mensajes y UDP usó 2 para 2 mensajes. 
Con los paquetes extra, TCP compra entrega confiable, confirmando la recepción, entrega ordenada, control de flujo, etc. UDP no ofrece nada de eso pero es más liviano.

#### f)
En TCP, el cliente manda un [SYN] (paquete 140) y el sistema operativo responde enseguida con un [RST, ACK] (paquete 141). Es una negativa: no hay handshake y ncat avisa que la conexión fue rechazada. Los paquetes siguientes son Windows reintentando el SYN, y cada intento recibe otro RST.

En UDP, el cliente manda el datagrama (paquete 260) y vuelve un ICMP `Destination unreachable (Port unreachable)` (paquete 261). ncat lo muestra como "An existing connection was forcibly closed by the remote host".

![UDP_TCP](images/UDP_TCP.png)

---

## Ejercicio 4: Servidor TCP mínimo con sockets

#### 4.a)
En el servidor, `socket()`, `bind()` y `listen()` son operaciones locales: crean el socket, lo asocian a `127.0.0.1:12000` y dejan el puerto escuchando. No mandan nada por la red.
`accept()` espera a que se conecte un cliente y devuelve un socket nuevo para esa conexión. Con ese socket recibe el mensaje con `recv()`, responde con `sendall()` y sigue leyendo hasta que el cliente cierra (cuando `recv()` devuelve vacío). Al final cierra los dos sockets.

#### 4.b)
El cliente crea su socket y con `connect()` se conecta al servidor, ahí el sistema operativo hace el three-way handshake. El puerto del cliente lo elige el sistema operativo y el script lo muestra con `getsockname()`. Después envía el mensaje con `sendall()`, recibe la respuesta con `recv()` y cierra con `close()`, que manda el FIN.
Si nadie escucha en el puerto, `connect()` falla por el RST (como en el 3.f), y si no hay respuesta corta por timeout a los 5 segundos.

#### 4.c)
Capturamos en loopback con el filtro `tcp.port == 12000` y ejecutamos el servidor y el cliente en dos terminales:

```bash
python tcp_server.py
python tcp_client.py 127.0.0.1 "Hola desde mi programa"
```

![SOCKET1](images/SOCKET_1.png)

Al cliente le tocó el puerto 64034. El servidor recibió 22 bytes y respondió con 32.

![SOCKET2](images/SOCKET_2.png)

```
1	cliente (64034) → servidor (12000)	[SYN]
2	servidor → cliente	[SYN, ACK]
3	cliente → servidor	[ACK]
4	cliente → servidor	[PSH, ACK]	Len=22
5	servidor → cliente	[ACK]
6	servidor → cliente	[PSH, ACK]	Len=32
7	cliente → servidor	[ACK]
8	cliente → servidor	[FIN, ACK]
9	servidor → cliente	[ACK]
10	servidor → cliente	[FIN, ACK]
11	cliente → servidor	[ACK]
```

Son 11 paquetes: 3 de handshake, 4 de datos con sus ACK y 4 de cierre. El mensaje tiene Len=22, justo los caracteres del texto, porque el script no agrega el \r\n del Enter como pasaba con ncat. La respuesta tiene 32 bytes porque el servidor le suma "Recibido: " adelante.
Al ser loopback, Wireshark muestra un encabezado Null/Loopback en lugar de Ethernet II, por eso los ACK miden 44 bytes (4 de loopback, 20 de IP y 20 de TCP).

#### 4.d)

| Llamada | ¿Dónde se ejecuta? | ¿Genera tráfico? | Segmentos que observamos |
|---|---|---|---|
| `socket()` | servidor y cliente | No | - |
| `bind()` | servidor | No | - |
| `listen()` | servidor | No | - |
| `connect()` | cliente | Sí | 1 `[SYN]`, 2 `[SYN, ACK]` y 3 `[ACK]` |
| `accept()` | servidor | No | - (la conexión ya estaba hecha en el paquete 3) |
| `sendall()` | servidor | Sí | 6 `[PSH, ACK]` con 32 bytes y 7 `[ACK]` |
| `recv()` | servidor | No | - (los datos ya habían llegado en el paquete 4) |
| `close()` | ambos | Sí | 8 `[FIN, ACK]`, 9 `[ACK]`, 10 `[FIN, ACK]` y 11 `[ACK]` |

El `sendall()` del cliente no está en la tabla, pero es el que genera el paquete 4 (22 bytes) y su ACK, el 5.
Solo `connect()`, `sendall()` y `close()` generan tráfico. El handshake y los ACK los maneja el sistema operativo, por eso `accept()` y `recv()` no mandan nada: solo le entregan al programa lo que ya llegó.

#### ¿Hacía falta usar puertos distintos para TCP y UDP?
No. TCP y UDP tienen puertos independientes, el sistema operativo los distingue por el campo `Protocol` de IP (6 para TCP y 17 para UDP). Un servidor TCP y uno UDP podrían usar los dos el 12000. Usar puertos distintos solo hace más fácil leer las capturas.

---