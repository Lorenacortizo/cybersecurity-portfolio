# Informe de incidente de ciberseguridad: Análisis de ataques de red / Cybersecurity Incident Report: Analyze Network Attacks

## Sección 1: Identificar el tipo de ataque que pudo causar esta interrupción de la red / Section 1: Identify the Type of Attack That May Have Caused This Network Interruption

### Español
* **Posible explicación del error de tiempo de espera:** Una explicación probable para el mensaje de error de tiempo de espera de conexión (*connection timeout*) del sitio web es un ataque de **denegación de servicio (DoS)**.
* **Qué muestran los registros de Wireshark:** Los registros (`wireshark-tcp-http-log.xlsx`) muestran que el servidor web (`192.0.2.1`, puerto `443`) deja de responder al tráfico legítimo de los empleados (`198.51.100.0/24`) tras verse desbordado por una ráfaga continua de paquetes de solicitud `TCP [SYN]` provenientes de una única dirección IP externa (`203.0.113.0`, puerto origen `54770`). A partir de la entrada número `125` del registro, el servidor queda completamente saturado y solo se registran peticiones `SYN` entrantes del atacante sin respuesta.
* **Tipo de evento:** Este incidente corresponde a un ataque **DoS** directo (al originarse desde una sola fuente) conocido como **inundación SYN (*SYN flood*)**.

### English
* **Potential explanation for the connection timeout error:** One potential explanation for the website's connection timeout error message is a **Denial of Service (DoS)** attack.
* **What the Wireshark logs show:** The logs (`wireshark-tcp-http-log.xlsx`) show that the web server (`192.0.2.1`, port `443`) stops responding to legitimate employee traffic (`198.51.100.0/24`) after being overloaded with continuous `TCP [SYN]` packet requests originating from a single external IP address (`203.0.113.0`, source port `54770`). From log entry `125` onward, the server is completely overwhelmed and only incoming attacker `SYN` packets are recorded.
* **Type of event:** This event is a direct **DoS** attack (originating from a single source) called **SYN flooding**.

---

## Sección 2: Explicar cómo el ataque provoca el mal funcionamiento del sitio web / Section 2: Explain How the Attack Is Causing the Website to Malfunction

### Español

#### 1. El protocolo de enlace de tres vías de TCP (*TCP Three-Way Handshake*)
Cuando los visitantes legítimos intentan establecer una conexión segura (`HTTPS`, puerto `443`) con el servidor web para cargar la página (`GET /sales.html`), se produce un intercambio de tres pasos mediante el protocolo TCP:
1. **SYN (*Synchronize*):** El equipo del visitante envía un paquete `SYN` al servidor web solicitando iniciar una conexión.
2. **SYN-ACK (*Synchronize-Acknowledge*):** El servidor web responde con un paquete `SYN, ACK` aceptando la solicitud y **reservando recursos de memoria/conexión** a la espera de completar el enlace.
3. **ACK (*Acknowledge*):** El equipo del visitante envía un paquete final `ACK` confirmando el permiso para conectarse, momento en el cual se establece la sesión y comienza el tráfico `HTTP/HTTPS`.

#### 2. Qué ocurre durante una inundación SYN (*SYN Flood*)
En un ataque de inundación SYN, el actor malicioso envía un volumen masivo de paquetes `SYN` en muy pocos segundos sin intención de completar el enlace de tres vías (o saturando la cola de conexiones semiabiertas). El servidor web intenta responder con `SYN, ACK` y reserva recursos para cada solicitud falsa hasta agotar toda su capacidad disponible, dejando sin recursos a las peticiones TCP legítimas.

#### 3. Impacto observado en los registros y consecuencias para la organización
* **Degradación progresiva y caída total:** En los primeros segundos del registro (entradas `55–62`), los empleados aún logran completar el *handshake* y recibir respuestas `HTTP 200 OK`. Sin embargo, a medida que aumenta la frecuencia de paquetes `SYN` desde `203.0.113.0`, comienzan a aparecer fallos en las conexiones legítimas: paquetes `TCP [RST, ACK]` (reinicio de conexión) y errores `HTTP/1.1 504 Gateway Time-out` (el servidor tarda demasiado en responder). A partir del registro `125`, el servidor deja de responder por completo.
* **Impacto operativo:** Los empleados de la agencia de viajes no pueden acceder al portal interno de ventas (`sales.html`) para consultar paquetes vacacionales, paralizando la atención al cliente y las ventas.

#### 4. Contención inicial y medidas recomendadas a futuro
* **Contención aplicada:** Desconexión temporal del servidor para liberar la cola de conexiones y bloqueo de la IP `203.0.113.0` en el cortafuegos.
* **Recomendaciones de mitigación:** Dado que un atacante puede falsificar direcciones IP origen (*IP spoofing*) para evadir un bloqueo estático por IP, se recomienda habilitar **SYN Cookies** en el servidor/cortafuegos, configurar límites de tasa de conexión (*rate limiting*), reducir el tiempo de espera (*timeout*) de conexiones semiabiertas y evaluar soluciones de mitigación anti-DoS/DDoS perimetrales.

---

### English

#### 1. The TCP Three-Way Handshake
When legitimate visitors try to establish a secure connection (`HTTPS`, port `443`) with the web server to load the webpage (`GET /sales.html`), a three-step handshake occurs using the TCP protocol:
1. **SYN (*Synchronize*):** A `SYN` packet is sent from the source visitor to the destination web server requesting to connect.
2. **SYN-ACK (*Synchronize-Acknowledge*):** The destination web server replies to the source with a `SYN, ACK` packet to accept the connection request and **reserves system resources** for the connection.
3. **ACK (*Acknowledge*):** A final `ACK` packet is sent from the source to the destination acknowledging permission to connect, completing the handshake so `HTTP/HTTPS` traffic can flow.

#### 2. What Happens When a Malicious Actor Sends Massive SYN Packets
In a SYN flood attack, a malicious actor sends a large number of `SYN` packets all at once without completing the handshake. This overwhelms the server's available resources reserved for pending connections, leaving no server resources available for legitimate TCP connection requests.

#### 3. What the Logs Indicate and Organizational Impact
* **Progressive degradation to total outage:** Early in the capture (entries `55–62`), employees still complete handshakes and receive `HTTP 200 OK` responses. As `SYN` requests from `203.0.113.0` flood the server every fraction of a second, legitimate traffic begins failing with `TCP [RST, ACK]` packets and `HTTP/1.1 504 Gateway Time-out` errors. From log entry `125` onward, the server stops responding altogether.
* **Business impact:** Travel agency employees cannot access the sales web page (`sales.html`) to look up vacation packages for customers, disrupting daily operations and revenue.

#### 4. Initial Containment and Recommended Next Steps
* **Immediate containment:** Temporarily taking the server offline to recover and blocking IP `203.0.113.0` on the company firewall.
* **Long-term hardening:** Because an attacker can spoof source IP addresses to bypass a single-IP firewall block, recommended defenses include enabling **SYN Cookies**, configuring connection **rate limiting**, tuning half-open connection timeouts, and deploying perimeter DoS/DDoS mitigation controls.