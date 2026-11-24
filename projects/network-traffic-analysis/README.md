# Análisis de tráfico de red (DNS, UDP e ICMP) / Network Traffic Analysis (DNS, UDP, and ICMP)

Caso práctico de ciberseguridad basado en una captura con `tcpdump` ante fallos de acceso al sitio web `www.yummyrecipesforme.com`.

---

## Español

### 1. Resumen del registro `tcpdump` y protocolos implicados
* **Protocolos observados:** Se emplea **UDP** como transporte para consultas **DNS** hacia el servidor `203.0.113.2` con el fin de resolver la dirección IP de `yummyrecipesforme.com`, mientras que **ICMP** devuelve notificaciones de error cuando el paquete no puede entregarse.
* **Detalles de la captura:** Las peticiones salientes parten de `192.51.100.15.52444` hacia `203.0.113.2.domain` con el identificador `35084`, el indicador `+` (banderas UDP) y `A?` (consulta de registro A). Tras ellas, `203.0.113.2` responde con paquetes `ICMP` hacia `192.51.100.15` reportando `udp port 53 unreachable` en distintos instantes.
* **Interpretación:** Como el puerto `53` corresponde a DNS y figura como inalcanzable, el servidor DNS no está respondiendo o no tiene un servicio escuchando en ese puerto, impidiendo la resolución del dominio.

### 2. Análisis del incidente y posible causa raíz
* **Primer reporte:** Hoy, con registro inicial a las `13:24:32.192571`.
* **Escenario y síntomas:** Varios clientes reportaron imposibilidad para cargar `www.yummyrecipesforme.com` y un mensaje de error de puerto de destino inalcanzable tras esperar la respuesta.
* **Estado actual:** Escalado a supervisión directa y en investigación por parte de ingeniería de seguridad para restablecer el acceso.
* **Hallazgos de la investigación:** Al reproducir el problema con `tcpdump`, se confirmaron peticiones UDP al puerto 53 seguidas de respuestas ICMP `udp port 53 unreachable`.
* **Siguientes pasos:** Comprobar si el servidor DNS está caído o sin el servicio activo, revisar reglas de *firewall* que puedan bloquear el puerto 53 y coordinar la restauración o ajuste con los responsables del sistema.
* **Presunta causa raíz:** Un servidor DNS inoperativo o sin respuesta, ya sea por un ataque de denegación de servicio (DoS) o por una mala configuración (como un bloqueo del puerto 53 en el *firewall*).

---

## English

### 1. `tcpdump` Log Summary and Protocols Involved
* **Protocols observed:** **UDP** carries **DNS** queries to server `203.0.113.2` to resolve the IP address for `yummyrecipesforme.com`, while **ICMP** returns error messages when delivery fails.
* **Log details:** Outbound packets go from `192.51.100.15.52444` to `203.0.113.2.domain` with query ID `35084`, a `+` flag marker, and `A?` (DNS A record query). Replies from `203.0.113.2` to `192.51.100.15` use `ICMP` to report `udp port 53 unreachable` across timestamps.
* **Interpretation:** Because port `53` serves DNS and is reported unreachable, the DNS server is likely unresponsive or has no service listening on that port, blocking domain resolution.

### 2. Incident Analysis and Suspected Root Cause
* **First reported:** Today, with initial timestamp `13:24:32.192571`.
* **Scenario and symptoms:** Multiple users reported being unable to reach `www.yummyrecipesforme.com` and seeing a destination port unreachable error after waiting for the page to load.
* **Current status:** Escalated to direct supervision and actively investigated by security engineers to restore access.
* **Investigation findings:** Reproducing the issue under `tcpdump` showed outbound UDP traffic to port 53 returning ICMP `udp port 53 unreachable` messages.
* **Next steps:** Verify whether the DNS server is down or not listening on port 53, check firewall rules blocking port 53, and coordinate remediation with system and security staff.
* **Suspected root cause:** An unresponsive or down DNS server due to a Denial of Service (DoS) attack or a misconfiguration such as port 53 being blocked on a firewall.
