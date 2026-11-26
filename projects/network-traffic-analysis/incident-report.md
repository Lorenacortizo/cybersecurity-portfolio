# Informe de incidente de ciberseguridad: Análisis de tráfico de red / Cybersecurity Incident Report: Network Traffic Analysis

## Parte 1: Resumen del problema en el registro de tráfico DNS e ICMP / Part 1: Summary of the Problem in the DNS and ICMP Traffic Log

### Español
* **Qué revela el protocolo UDP:** El navegador envía peticiones DNS encapsuladas en UDP desde el origen `192.51.100.15.52444` hacia el servidor DNS `203.0.113.2.domain` para resolver la dirección IP de `yummyrecipesforme.com`. Cada consulta muestra el identificador `35084`, el signo `+` (indicador de banderas en el mensaje UDP) y `A?` (solicitud de registro A para mapear el dominio a una IP).
* **Qué devuelve la respuesta ICMP:** Tras los envíos (registrados a partir de las `13:24:32.192571` y repetidos en bloques posteriores), se reciben paquetes `ICMP` desde `203.0.113.2` hacia `192.51.100.15` con el aviso `udp port 53 unreachable`.
* **Para qué sirve el puerto indicado:** El puerto `53` es el puerto estándar asociado a las consultas y respuestas del servicio DNS.
* **Problema más probable:** El servidor DNS no está respondiendo o no tiene un servicio escuchando en el puerto UDP `53`, por lo que el nombre de dominio no puede resolverse y la web no carga.

### English
* **What UDP shows:** The client sends DNS queries over UDP from `192.51.100.15.52444` to DNS server `203.0.113.2.domain` to resolve `yummyrecipesforme.com`. Entries include query ID `35084`, a `+` flag marker, and `A?` (an A record lookup mapping the domain name to an IP address).
* **What the ICMP reply shows:** Following the queries (starting at `13:24:32.192571` and repeating in later blocks), `ICMP` packets travel from `203.0.113.2` back to `192.51.100.15` reporting `udp port 53 unreachable`.
* **Purpose of the port in the error:** Port `53` is commonly used for DNS traffic.
* **Most likely issue:** The DNS server is unresponsive or has no service listening on UDP port `53`, preventing domain resolution and access to the site.

---

## Parte 2: Explicación del análisis y posible causa del incidente / Part 2: Data Analysis and Likely Cause of the Incident

### Español
* **Momento del incidente:** Registrado inicialmente hoy a las `13:24:32.192571` (1:24 p. m. en formato de 24 horas), con nuevas iteraciones posteriores en el registro.
* **Cómo se detectó y síntomas iniciales:** Varios usuarios comunicaron que no podían acceder a `www.yummyrecipesforme.com` y que veían el mensaje de error de puerto de destino inalcanzable tras esperar la carga.
* **Acciones de investigación y estado actual:** El equipo de ciberseguridad reprodujo el fallo y capturó tráfico con `tcpdump`; el caso se reportó a supervisión directa y actualmente lo gestionan ingenieros de seguridad mientras continúa la investigación.
* **Hallazgos clave:** Las capturas confirman intentos reiterados de resolución DNS por UDP al puerto `53` de `203.0.113.2` respondidos con `ICMP` `udp port 53 unreachable`.
* **Causa probable y siguientes pasos:** Como hipótesis principal, el servidor DNS podría estar caído o sin respuesta debido a un ataque de denegación de servicio (**DoS**) o a un cambio de configuración (como bloquear el puerto `53` en un *firewall*). Los siguientes pasos consisten en comprobar si el servidor DNS está operativo y escuchando en el puerto `53`, revisar las reglas del *firewall* para descartar bloqueos no deseados y coordinar la corrección con los responsables del sistema.

### English
* **When the incident occurred:** First logged today at `13:24:32.192571` (1:24 p.m. in 24-hour time), with additional occurrences later in the log.
* **How it became known and initial symptoms:** Customers reported being unable to reach `www.yummyrecipesforme.com` and seeing a destination port unreachable error after waiting for the page to load.
* **Investigation actions and current status:** Security staff reproduced the issue and captured traffic with `tcpdump`; it was reported to direct supervision and is now being handled by security engineers as investigation continues.
* **Key findings:** The capture shows repeated outbound UDP DNS queries to port `53` on `203.0.113.2` met with `ICMP` `udp port 53 unreachable` replies.
* **Likely cause and next steps:** A plausible cause is an unresponsive or down DNS server resulting from a Denial of Service (**DoS**) attack or a misconfiguration such as port `53` being blocked on a firewall. Next steps are to verify DNS server health and port `53` availability, check firewall rules for unintended blocking, and coordinate remediation with system administrators.