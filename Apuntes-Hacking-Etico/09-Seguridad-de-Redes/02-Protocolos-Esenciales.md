# Protocolos Esenciales: TCP, UDP e ICMP

## TCP (Transmission Control Protocol)
Es un protocolo **orientado a conexión y confiable**: antes de enviar datos, establece una conexión formal entre los dos extremos, y garantiza que los datos lleguen completos y en orden (si se pierde un paquete, lo reenvía).

### El "3-way handshake" (saludo de 3 pasos)
Así es como se establece toda conexión TCP, y es la base de por qué Wireshark muestra tanto tráfico "de más" antes de ver datos reales:
1. **SYN**: el cliente le dice al servidor "quiero conectarme".
2. **SYN-ACK**: el servidor responde "recibido, yo también quiero conectarme".
3. **ACK**: el cliente confirma "perfecto, empecemos".

Recién después de estos 3 pasos empieza a viajar la data real (ej. la petición HTTP). Este handshake es también la base de técnicas de escaneo de nmap (un escaneo SYN, por ejemplo, envía el primer paso y analiza la respuesta sin completar la conexión — más sigiloso).

### Por qué importa en CTF
- Casi todo lo que van a analizar en Wireshark (HTTP, FTP, SSH) corre sobre TCP.
- "Follow TCP Stream" en Wireshark (ver [05-Sniffing-y-Analisis-de-Trafico](05-Sniffing-y-Analisis-de-Trafico.md)) reconstruye justamente una conexión TCP completa como si fuera una conversación de texto.

## UDP (User Datagram Protocol)
Es lo opuesto a TCP: **no orientado a conexión y no confiable**. Envía los datos "a lo loco", sin garantizar que lleguen, sin ordenarlos, y sin retransmitir si algo se pierde. A cambio, es mucho más rápido porque no tiene toda la negociación de TCP.

### Dónde se usa
- DNS (consultas rápidas, donde perder una consulta ocasional no es grave — se vuelve a preguntar).
- Streaming de video/audio en vivo, videojuegos (priorizan velocidad sobre confiabilidad perfecta).
- SNMP (monitoreo de dispositivos de red).

### Por qué importa en CTF
Muchos escaneos de puertos por defecto (`nmap` sin flags especiales) solo revisan TCP — **es fácil olvidarse de escanear UDP** (`nmap -sU`) y perderse servicios importantes que solo corren ahí, como DNS o SNMP mal configurado.

## ICMP (Internet Control Message Protocol)
No transporta datos de aplicaciones — se usa para **mensajes de control y diagnóstico** de la red.

### Usos típicos
- `ping`: envía un mensaje ICMP "Echo Request" y espera un "Echo Reply" — así se comprueba que un host está activo y responde.
- `traceroute`: usa ICMP (o UDP, según el sistema) para mapear la ruta que siguen los paquetes hasta un destino, salto por salto.

### Por qué importa en CTF/seguridad
- Un firewall bien configurado a menudo bloquea ICMP hacia afuera, así que si el `ping` no responde no significa necesariamente que el host esté apagado.
- ICMP también se puede usar (de forma maliciosa) para exfiltrar datos de a poco ("ICMP tunneling"), un tema más avanzado.

## Resumen para elegir el protocolo correcto al analizar
Si en Wireshark ven mucho tráfico "SYN, SYN-ACK, ACK" antes de los datos → es TCP. Si ven paquetes sueltos sin ese patrón de negociación → probablemente UDP. Si ven paquetes de "Echo Request/Reply" → es ICMP, generalmente relacionado con diagnóstico de red, no con contenido de aplicación.
