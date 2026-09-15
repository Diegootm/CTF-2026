# Fundamentos de Redes y el Modelo OSI

## ¿Por qué aprender esto?
Todo lo que hacen en Web, Forense de red, y buena parte de Miscelánea depende de entender cómo viaja la información entre dos computadoras. No hace falta ser ingeniero de redes — con el panorama general de este archivo alcanza para entender el resto de la carpeta y sacarle mejor provecho a Wireshark, nmap, etc.

## El modelo OSI: 7 capas
Es un modelo teórico que describe cómo se organiza la comunicación de red, dividiéndola en 7 capas. Cada capa se apoya en la de abajo y le da servicio a la de arriba.

| Capa | Nombre | Qué hace | Ejemplos |
|---|---|---|---|
| 7 | Aplicación | Lo que usa el usuario final directamente | HTTP, FTP, DNS, SSH |
| 6 | Presentación | Formato/cifrado de los datos | SSL/TLS, compresión |
| 5 | Sesión | Establece y mantiene la conexión entre dos programas | Sesiones de NetBIOS, RPC |
| 4 | Transporte | Entrega de datos extremo a extremo, confiable o no | TCP, UDP |
| 3 | Red | Direccionamiento y enrutamiento entre redes distintas | IP, ICMP |
| 2 | Enlace de datos | Comunicación dentro de la misma red local | Ethernet, switches, direcciones MAC |
| 1 | Física | Los medios físicos reales | Cables, Wi-Fi, señales eléctricas |

**No hace falta memorizar el modelo completo de memoria.** Lo importante es tener el concepto: cada capa resuelve un problema distinto, y cuando algo falla o se ataca, saber "en qué capa" ocurre ayuda mucho a entender qué herramienta usar.

## Versión simplificada que usan en la práctica: TCP/IP
En la práctica, la mayoría de herramientas y documentación hablan del modelo **TCP/IP** (4 capas), que agrupa algunas capas OSI:

| Capa TCP/IP | Equivale a (OSI) | Protocolos típicos |
|---|---|---|
| Aplicación | 5, 6, 7 | HTTP, DNS, FTP, SSH |
| Transporte | 4 | TCP, UDP |
| Internet | 3 | IP, ICMP |
| Acceso a la red | 1, 2 | Ethernet, Wi-Fi |

## Direcciones IP y MAC (lo mínimo)
- **Dirección IP**: identifica un dispositivo dentro de una red (o en internet). Puede ser IPv4 (`192.168.1.5`) o IPv6 (más larga, cada vez más común).
- **Dirección MAC**: identifica físicamente una tarjeta de red, es única por fabricante/dispositivo, y solo se usa dentro de la red local (capa 2) — no viaja fuera de ella.

Esta distinción importa porque **muchos ataques de red local (ver [06-Ataques-Comunes-en-Redes](06-Ataques-Comunes-en-Redes.md)) explotan justamente cómo se traduce una dirección IP a una MAC dentro de la red local.**

## Puertos: cómo un mismo dispositivo atiende varios servicios
Una IP identifica al dispositivo, pero un dispositivo puede correr muchos servicios distintos a la vez (un servidor web, un servidor FTP, SSH). Cada servicio "escucha" en un **puerto** distinto (un número del 0 al 65535). Por eso, cuando escanean una IP con nmap, en realidad están buscando qué puertos tienen un servicio escuchando detrás.

Puertos comunes que van a ver todo el tiempo:

| Puerto | Servicio |
|---|---|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP (correo) |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 3389 | RDP (escritorio remoto Windows) |
