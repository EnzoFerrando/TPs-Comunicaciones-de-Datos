# TP3

# Integrantes :

- Enzo Nahuel Fernandez Mattio, 46225824, I.COMP
- Enzo Jesús Ferrando, 46320483, I.COMP
- Ignacio José Giraudo, 44900514, I.COMP
- Ezequiel Moreyra, 46226249, I.COMP

---

# Punto 1

## 1.a

**¿Qué función cumple la capa de enlace dentro del modelo OSI? ¿Qué tipo de comunicación resuelve?**

La capa de enlace (capa 2 del modelo OSI) se encarga de la comunicación entre dispositivos que están directamente conectados dentro de la misma red local, como una LAN. Su trabajo es tomar los bits que llegan de la capa física y organizarlos en tramas con inicio y fin bien definidos, direccionar esas tramas usando direcciones MAC, detectar errores de transmisión, y coordinar cómo varios dispositivos comparten un mismo medio físico sin interferirse entre sí.

---

## 1.b

**¿Qué es una dirección MAC? ¿En qué se diferencia de una dirección IP?**

Una dirección MAC es un identificador físico único asignado a la placa de red de un dispositivo, expresado en 6 bytes en hexadecimal, que viene grabado de fábrica en el hardware. Se diferencia de la dirección IP principalmente en su alcance y propósito: la MAC opera en la capa de enlace (capa 2) y solo tiene sentido dentro de la red local, cambiando en cada salto porque cada router reemplaza la MAC al reenviar el paquete. Por otro lado, la IP opera en la capa de red (capa 3), se asigna lógicamente y se mantiene constante desde el origen hasta el destino final, sin importar cuántas redes intermedias atraviese.

---

## 1.c

**¿Qué es una trama Ethernet? Identificar sus principales campos y explicar brevemente para qué sirve cada uno.**

Una trama Ethernet es la unidad de datos que se transmite en una red Ethernet a nivel de capa de enlace, y encapsula el paquete que viene de capa 3 (típicamente IP) agregándole sus propios encabezados. Comienza con un preámbulo que sincroniza al receptor para que empiece a leer correctamente, seguido de la dirección MAC de destino y la dirección MAC de origen, que identifican respectivamente a quién va dirigida la trama y quién la envía dentro de la red local. Luego viene el campo EtherType, que indica qué protocolo de capa superior está encapsulado en el contenido. Después se encuentra el payload o datos, que normalmente es un paquete IP completo con sus propios encabezados. Finalmente, la trama termina con el FCS (Frame Check Sequence), un checksum que permite detectar si la trama llegó corrompida en el camino.

---

## 1.d

**¿Qué información permite determinar qué protocolo de capa superior está transportando una trama Ethernet?**

Esto se determina mediante el campo EtherType del encabezado Ethernet, un valor de 2 bytes que funciona como etiqueta indicando qué protocolo va encapsulado en el contenido de la trama. Por ejemplo, el valor 0x0800 indica que adentro hay un paquete IPv4, 0x86DD indica IPv6, y 0x0806 indica ARP. Cuando el dispositivo receptor lee la trama, revisa ese campo para saber cómo interpretar correctamente lo que sigue.

---

# Punto 2

**Usando Wireshark, capturar tráfico generado por su propia computadora mientras acceden a una página web o ejecutan alguna aplicación que utilice la red**

![imagen1.png](images/imagen1.png)

---

## 2.a

**Seleccionar una trama Ethernet e identificar las direcciones MAC de origen y destino. ¿A qué dispositivos creen que corresponden?**

Al seleccionar la trama número 168 de la captura, dentro de la sección Ethernet II se identifican dos direcciones MAC: la dirección de destino es 50:2e:91:4c:da:7e y la dirección de origen es 90:f9:b7:1d:8b:3e. La MAC de origen corresponde a la placa de red de la propia computadora, es decir, la interfaz wifi utilizada para capturar el tráfico. La MAC de destino, en cambio, corresponde al router de la red local, y no al servidor remoto con el que nos estamos comunicando, ya que las direcciones MAC solo tienen alcance dentro de la red local y el siguiente salto para llegar a internet es siempre el router.

---

## 2.b

**Dentro de la misma trama, identificar el paquete IP. ¿Cuáles son las direcciones IP de origen y destino? (no importa si son versión 4 o versión 6)**

Dentro de esa misma trama, en la sección correspondiente al paquete IP (Internet Protocol Version 4), se observa que la dirección IP de origen es 140.82.114.25 y la dirección IP de destino es 192.168.100.20. La IP de origen pertenece a un servidor remoto en internet (pudimos identificar que pertenece a GitHub), mientras que la IP de destino es la dirección privada asignada a la computadora dentro de la red local. En este caso, el paquete corresponde a una respuesta que envía el servidor hacia el equipo, por lo que el sentido de la comunicación es del servidor hacia la computadora local.

---

## 2.c

**Comparar las direcciones MAC y las direcciones IP encontradas. ¿Representan lo mismo?**

Al comparar ambos tipos de direcciones concluimos que no representan lo mismo. Las direcciones MAC identifican a los dos dispositivos que están directamente conectados en el tramo local de la red, en este caso la computadora y el router, ya que Ethernet solo resuelve la comunicación entre vecinos inmediatos dentro de la misma red local. Las direcciones IP, en cambio, identifican el origen y el destino reales de la comunicación de extremo a extremo, es decir, el servidor remoto de GitHub y la computadora, sin importar la cantidad de routers o redes intermedias que el paquete deba atravesar para llegar a destino. Esto demuestra claramente que la trama Ethernet nunca contiene la dirección MAC del servidor remoto, sino únicamente la del siguiente salto dentro de la red local, mientras que la dirección IP se mantiene constante durante todo el recorrido del paquete.

---

## 2.d

**Observar el campo EtherType. ¿Qué protocolo está encapsulado dentro de la trama analizada?**

El campo EtherType de la trama analizada indica el valor IPv4 (0x0800), lo cual señala que el protocolo encapsulado dentro de dicha trama Ethernet es IPv4. Esto es coherente con el resto de la información visible en la captura, donde efectivamente se observa un paquete de Internet Protocol Version 4 seguido de un segmento TCP dentro del contenido de la trama.
