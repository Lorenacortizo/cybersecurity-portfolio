# Análisis de tráfico de red (DNS, UDP e ICMP) / Network Traffic Analysis

## Escenario / Scenario
Un equipo de servicios informáticos recibe avisos de clientes que no pueden cargar el sitio web `www.yummyrecipesforme.com` y obtienen el error de puerto de destino inalcanzable. Al reproducir el fallo y capturar paquetes con `tcpdump`, se observan consultas salientes hacia el servidor DNS (`203.0.113.2`) y respuestas de error `ICMP` sobre el puerto UDP `53`.

An IT services team receives reports from users unable to reach `www.yummyrecipesforme.com` who see a destination port unreachable error. Reproducing the issue while capturing packets with `tcpdump` reveals outbound queries to DNS server `203.0.113.2` alongside `ICMP` error messages involving UDP port `53`.

## Objetivo de la práctica / Practice Objective
* Inspeccionar un registro de captura de red (`tcpdump`) para identificar los protocolos implicados (DNS, UDP e ICMP) y el significado de sus campos y marcas de tiempo.
* Interpretar qué indica el fallo sobre el puerto `53` respecto a la resolución de nombres de dominio y al estado del servidor DNS.
* Redactar un informe estructurado de incidente de ciberseguridad con los hallazgos, el estado de la investigación, las posibles causas y los siguientes pasos.

* Inspect a `tcpdump` network capture log to identify the protocols involved (DNS, UDP, and ICMP) and what their fields and timestamps show.
* Interpret what a failure on port `53` means for domain name resolution and DNS server availability.
* Produce a structured cybersecurity incident report covering findings, investigation status, potential causes, and next steps.

## Archivos del proyecto / Project Files
* `README.md`: Contexto del escenario y objetivo de la práctica.
* `tcpdump.log`: Registro de paquetes utilizado para el análisis.
* `incident-report.md`: Informe detallado del incidente (Partes 1 y 2).