# Forense de Red (Wireshark)

## ¿Qué es?
Es aplicar análisis forense al tráfico de una red. Existen herramientas que capturan los paquetes que viajan por una red y los guardan en archivos (formato `.pcap` o `.pcapng`), que después se pueden analizar en detalle para encontrar información sensible, actividad maliciosa, o simplemente reconstruir qué pasó.

## La herramienta central: Wireshark
Wireshark es la herramienta esencial para capturar, analizar y examinar tráfico de red. Permite:
- Descomponer la comunicación en paquetes individuales.
- Analizar protocolos como HTTP, TCP/IP, DNS, FTP, etc.
- Identificar direcciones IP de origen y destino.
- **Reconstruir sesiones completas** para entender qué se transmitió.

## Flujo típico para resolver un reto con un archivo `.pcap`

1. **Abran el archivo** en Wireshark (`File > Open`).
2. **Miren el panorama general**: ¿qué protocolos aparecen? (columna "Protocol"). Si ven HTTP, FTP, o Telnet, es buena señal — son protocolos sin cifrar, así que el contenido se puede leer directamente.
3. **Usen el filtro de Wireshark** para enfocarse en lo relevante. Algunos filtros útiles:
   - `http` — solo tráfico HTTP
   - `ftp` — solo tráfico FTP
   - `ip.addr == 192.168.1.5` — solo tráfico de/hacia una IP específica
   - `tcp.port == 21` — solo tráfico de un puerto específico
4. **"Follow TCP Stream"**: clic derecho sobre un paquete → `Follow > TCP Stream`. Esto reconstruye toda la conversación entre dos extremos como si fuera texto legible — es EXTREMADAMENTE útil y suele ser el camino más directo a la flag.
5. **Exportar objetos transferidos**: `File > Export Objects > HTTP` (o FTP) — si se transfirió un archivo (imagen, documento) durante la captura, Wireshark puede extraerlo directamente sin tener que reconstruirlo a mano.

## Qué buscar específicamente
- Credenciales en texto plano (usuarios/contraseñas en protocolos como FTP, HTTP sin HTTPS, Telnet).
- Archivos transferidos que puedan contener la flag.
- Peticiones DNS sospechosas (a veces se usa DNS para exfiltrar datos poco a poco).
- Contenido de formularios enviados por POST en HTTP.

## Tip importante
No hace falta revisar los paquetes uno por uno manualmente — casi siempre la combinación de **filtro + Follow TCP Stream + Export Objects** resuelve el reto mucho más rápido que leer paquete por paquete.
