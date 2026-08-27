# TP2

# Integrantes :

- Enzo Nahuel Fernandez Mattio, 46225824, I.COMP
- Enzo Jesús Ferrando, 46320483, I.COMP
- Ignacio José Giraudo, 44900514, I.COMP
- Ezequiel Moreyra, 46226249, I.COMP

---

# Punto 1

![image.png](image.png)

## 1.a

**¿Qué fenómeno físico se está representando en la Figura? ¿Cuáles son las características principales del mismo?**

El fenómeno representado es el efecto Doppler. Ocurre cuando hay un movimiento entre un emisor y un receptor de onda. Esto produce que la frecuencia percibida cambie, si ambos se acercan, la frecuencia aumenta; si se alejan, disminuye. 

---

## 1.b

**Recordando las bandas de transmisión vistas en el TP01, investigar:**

- **¿A qué tipos de transmisión afecta más este fenómeno?**
    
    El efecto Doppler afecta especialmente a las transmisiones que utilizan frecuencias altas y cuando existe una gran velocidad relativa entre transmisor y receptor, como en comunicaciones satelitales o móviles.
    
- **¿Cuáles son más resilientes al mismo?**
    
    Las transmisiones en bandas de menor frecuencia son, ya que para una misma velocidad el desplazamiento absoluto de frecuencia producido por el efecto Doppler es menor.
    

---

## 1.c

**Investigar:**

- **¿Cuáles son las razones por las cuales no se debe encender el celular arriba de un avión?**
    
    Se solicita mantener el celular en modo avión para evitar/reducir posibles interferencias con los sistemas de comunicación y navegación del avion.
    
- **¿Tiene algo que ver el fenómeno descrito en los puntos anteriores?**
    
    El efecto Doppler no es la razón principal de esto, aunque está relacionado con las comunicaciones de un avión porque, debido a su alta velocidad, las señales de radio pueden experimentar desplazamientos de frecuencia por este efecto.
    

---

# Punto 2

![image.png](image%201.png)

## 2.a

**¿Qué fenómeno físico se está representando en la Figura? ¿Cuáles son las características principales del mismo?**

Lo que se está representando es el ruido/interferencia electromagnética. Se ve cómo una fuente externa (en este caso, simbolizada por el obrero con la maquina) se superpone a la señal original y la distorsiona en una parte de la misma.

Características principales:

- Es una señal no deseada que se suma a la señal útil, degradando su forma y contenido de información.
- Suele ser aleatorio e impredecible.
- Puede originarse por múltiples fuentes: ruido térmico, interferencia electromagnética de equipos cercanos (motores, microondas), etc.
- El ruido se agrega a la señal y puede afectar solo una parte del trayecto o del espectro, como se ve en el gráfico.
- Cuando el ruido es suficientemente intenso en relación a la señal, provoca que el receptor interprete mal los símbolos transmitidos, generando errores de bit.

---

## 2.b

**Recordando las bandas de transmisión vistas en el TP01, investigar:**

- **¿A qué tipos de transmisión afecta más este fenómeno?**
    
    El ruido afecta más a transmisiones con baja SNR que viajan por trayectos con muchas fuentes de interferencia (zonas urbanas, otras transmisiones en frecuencias cercanas, etc.).
    
- **¿Cuáles son más resilientes al mismo?**
Son más resilientes las modulaciones simples (BPSK) y las transmisiones con mayor separación entre símbolos.

---

## 2.c

- **¿Qué es la SNR?**
    
    La SNR es la relación entre la potencia de la señal útil y la potencia del ruido presente en el canal, generalmente expresada en dB.
    
- **¿Tiene algo que ver con el concepto de BER que vimos en el TP01?**
    
    Tiene una relación inversa con el BER: a mayor SNR, menor probabilidad de que el ruido haga que un símbolo se interprete mal en el receptor, por lo tanto menor BER.
    

---

# Punto 3

**Resumir brevemente y para ir pensando:**

- **¿Cómo ayudan los sistemas de transmisión digital a detectar y corregir errores producidos por ruido en el canal?**
    
    Los sistemas digitales agregan redundancia controlada a los datos (bits de paridad, checksums, CRC, etc.). El receptor recalcula esa redundancia y la compara con lo recibido: si no coincide, detecta que hubo error, y en los códigos correctores puede reconstruir el bit erróneo sin pedir retransmisión.
    
- **¿Cómo ayudan a compensar cambios en la frecuencia?**
    
    Se usan mecanismos de sincronización (PLL, recuperación de portadora, recuperación de reloj) que ajustan continuamente la frecuencia y fase del receptor para engancharse a la señal entrante, compensando desajustes de oscilador entre transmisor y receptor.
    

---

# Punto 4

## 4.a

- **¿Qué significa sincronización en una comunicación digital?**
    
    En comunicaciones digitales, es el proceso mediante el cual el emisor y el receptor coordinan sus relojes y tiempos para asegurar que los datos se transmitan, reciban e interpreten correctamente, evitando pérdidas o distorsiones de la información.
    
- **Investigar la diferencia entre:**
    - **Sincronización de bits.**
        
        El receptor debe identificar con total precisión el instante exacto en el que comienza y termina cada bit individual. Esto le permite realizar el muestreo justo en el centro del intervalo de tiempo del bit para determinar correctamente si viaja un 1 o un 0.
        
    - **Sincronización de trama.**
        
        Consiste en identificar con exactitud el bit de inicio y el bit de finalización de todo el bloque para poder interpretar dónde termina el control (encabezado) y dónde empiezan los datos reales de usuario (carga útil).
        

---

## 4.b

- **¿Qué es una trama (frame)?**
    
    Es la Unidad de Datos de Protocolo correspondiente a la Capa de Enlace de Datos. Es un bloque estructurado de bits que encapsula la información de las capas superiores junto con la información de control necesaria para su transporte seguro a través del medio físico. 
    
- **¿Qué diferencias existen entre el encabezado (header), la carga útil (payload) y el tráiler (trailer)?**
    - Encabezado **:** Se ubica al inicio de la trama. Contiene bytes destinados exclusivamente al control del enlace, direcciones físicas (MAC de origen y destino), campos de longitud, números de secuencia y el tipo de protocolo de red que se está utilizando.
    - Carga útil: Es la sección intermedia de la trama. Contiene los datos reales que se desean transmitir entre los extremos (por lo general, es el paquete proveniente de la Capa de Red, como un paquete IP).
    - Tráiler: Se ubica al final de la trama. Su función principal es contener los bits de redundancia para la detección de errores en el canal, como el campo de Secuencia de Verificación de Trama basado en códigos como el CRC.

---

## 4.c

- **¿Qué función puede cumplir un preámbulo antes de una trama?**
    
    Un preámbulo es una secuencia de bits conocida y predefinida que se envía antes del contenido útil de la trama, y cumple varias funciones a nivel de capa física:
    
    - Sincronización de reloj: suele ser un patrón repetitivo que le permite al receptor adaptarse al tiempo de la señal entrante y ajustar su reloj interno antes de que llegue la información real.
    - Detección de inicio de trama: le avisa al receptor que viene una trama, permitiéndole diferenciar entre ruido de fondo/silencio del canal y el comienzo real de una transmisión.
    - Ajuste de ganancia/umbral de decisión:  le da al receptor una referencia para calibrar sus niveles de decisión (qué voltaje interpreta como 1 y cuál como 0) antes de que llegue el dato importante.
    - Estabilización del medio físico: en el caso de medios inalámbricos, también ayuda a estabilizar el oscilador y detectar la presencia de una señal válida por sobre el ruido del canal.
- **¿Es necesariamente parte de la información que se quiere transmitir?**
    
    No, el preámbulo no forma parte del payload o información útil que el usuario quiere enviar. Son datos de control a nivel de capa física, cuyo único propósito es preparar al receptor para decodificar correctamente lo que viene después. 
    

---

## 4.d

- **Investigar al menos tres formas mediante las cuales un protocolo puede determinar dónde termina una trama:**
    1. Longitud fija:
        
        Todas las tramas tienen un tamaño predeterminado y conocido de antemano por ambas partes, entonces el receptor simplemente cuenta bits/bytes.
        
    2. Un campo que indique la longitud:
        
        La trama incluye en su header un campo que indica explícitamente cuántos bytes/bits tiene el payload, y el receptor lee ese valor para saber cuánto leer.
        
    3. Caracteres o secuencias delimitadoras:
        
        Se usan bytes o patrones especiales de inicio y fin de trama, y si ese patrón aparece dentro de los datos reales se aplica bit/byte stuffing para evitar ambigüedad.
        

---

# Punto 5

## 5.a

- **Identificar la carga útil correspondiente a su grupo y documentar, tanto en el informe como en una pestaña destinada a ello en la planilla compartida.**

El payload obtenido por nuestro grupo fue: w

![image.png](image%202.png)

## 5.b

- **Sabiendo que SEQ es el número de secuencia, reorganizar los paquetes de todos los grupos y
reconstruir la información final (concatenar los caracteres).**

Concatenando todos los caracteres de los grupos en orden, la información final que encontramos fue: [https://www.youtube.com/shorts/dbbe_ln6Lnw](https://www.youtube.com/shorts/dbbe_ln6Lnw), siendo un short de YouTube indicando donde estaba escondido el premio.