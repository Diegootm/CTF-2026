# Sniffing y Análisis de Tráfico

## ¿Qué es "sniffing"?
Es la práctica de **capturar el tráfico que circula por una red**, para poder analizarlo después (o en tiempo real). Ya vimos el análisis de archivos `.pcap` ya capturados en [02-Forense/05-Forense-de-Red-Wireshark](05-Forense-de-Red-Wireshark.md) — acá el foco está en **cómo se captura ese tráfico** en primer lugar, y en un poco más de profundidad de análisis.

## Wireshark: repaso rápido de filtros útiles
```
http                        # solo tráfico HTTP
ftp                         # solo tráfico FTP
dns                         # solo consultas DNS
ip.addr == 192.168.1.5      # tráfico de/hacia una IP específica
tcp.port == 21              # tráfico de un puerto específico
tcp.flags.syn == 1 && tcp.flags.ack == 0    # solo paquetes SYN (inicios de conexión)
```

## tcpdump: capturar tráfico desde la terminal
Cuando no hay interfaz gráfica disponible (ej. conectados por SSH a una máquina remota), `tcpdump` permite capturar tráfico directamente desde la línea de comandos.

```bash
# Capturar todo el tráfico de una interfaz y guardarlo en un archivo
sudo tcpdump -i eth0 -w captura.pcap

# Capturar solo tráfico de un host específico
sudo tcpdump -i eth0 host 192.168.1.5

# Capturar solo tráfico de un puerto específico
sudo tcpdump -i eth0 port 80
```
El archivo `.pcap` generado se puede abrir después directamente en Wireshark para un análisis más visual.

## Por qué "sniffing" no siempre funciona igual de fácil
En una red moderna con **switches** (a diferencia de los viejos hubs), cada dispositivo solo recibe el tráfico dirigido específicamente a él — no todo el tráfico de la red "pasa" por todos los cables como antes. Por eso, para poder capturar tráfico de **otros** dispositivos en una red local, generalmente hace falta algún tipo de ataque de intermediario (ver [06-Ataques-Comunes-en-Redes](06-Ataques-Comunes-en-Redes.md), sección ARP Spoofing) — sin eso, un sniffer normal solo ve el tráfico propio.

## Qué buscar al analizar tráfico capturado
- Protocolos sin cifrar (HTTP, FTP, Telnet) — el contenido se lee directamente.
- Credenciales enviadas en texto plano.
- Archivos transferidos (extraíbles con "Export Objects" en Wireshark).
- Patrones anómalos: muchísimas conexiones a un mismo puerto en poco tiempo (posible escaneo), tráfico hacia direcciones IP poco comunes o geográficamente inesperadas.

## Análisis de tráfico cifrado (HTTPS)
Si el tráfico capturado es HTTPS, el contenido en sí no se puede leer directamente sin la clave de cifrado — salvo que:
- Tengan acceso al log de claves de sesión del navegador (`SSLKEYLOGFILE`), que permite a Wireshark descifrar el tráfico HTTPS capturado en un entorno de laboratorio controlado.
- Se trate de un ataque de intermediario (MITM) donde el atacante controla un certificado válido para la víctima (ver siguiente archivo).
