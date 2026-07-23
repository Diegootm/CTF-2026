**Prioridad:** 🔴 Crítica (instalar antes que nada) · 🟡 Importante · 🟢 Por si acaso
## 1. Herramientas indispensables (instalar sí o sí antes de empezar)

### Criptografía

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|CyberChef|"Navaja suiza" de cripto: decodificar Base64/Hex, XOR, cifrados clásicos, hashes, todo con interfaz visual|🔴|https://gchq.github.io/CyberChef/|
|dCode|Identificador y solucionador de decenas de cifrados clásicos (Vigenère, César, Playfair, etc.)|🔴|https://www.dcode.fr/|
|Python 3 + pycryptodome|Scriptear ataques (RSA débil, XOR, análisis de frecuencias, etc.)|🔴|`pip install pycryptodome` (viene con Python: https://www.python.org/)|
|RsaCtfTool|Ataques automáticos contra RSA mal implementado (n compartido, e pequeño, factorización débil)|🟡|https://github.com/RsaCtfTool/RsaCtfTool|
|John the Ripper|Crackeo de hashes y fuerza bruta de contraseñas|🟡|https://www.openwall.com/john/|
|Hashcat|Crackeo de hashes acelerado por GPU|🟡|https://hashcat.net/hashcat/|
|openssl (CLI)|Inspeccionar certificados, generar/verificar firmas, cifrar/descifrar rápido|🟡|Preinstalado en Linux (`apt install openssl` si falta)|

### Forense

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|Wireshark|Analizar capturas de tráfico (.pcap), seguir streams TCP, extraer archivos transferidos|🔴|https://www.wireshark.org/download.html|
|exiftool|Leer y extraer metadatos de imágenes, PDFs, documentos|🔴|https://exiftool.org/|
|binwalk|Detectar y extraer archivos/firmware embebidos dentro de otro archivo|🔴|https://github.com/ReFirmLabs/binwalk|
|Volatility 3|Análisis de volcados de memoria RAM (procesos, conexiones, malware en memoria)|🟡|https://github.com/volatilityfoundation/volatility3|
|Autopsy|Análisis forense de imágenes de disco (archivos borrados, timeline, metadatos)|🟡|https://www.sleuthkit.org/autopsy/|

### Web

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|Burp Suite (Community)|Interceptar y modificar peticiones HTTP, repetir requests, fuzzing básico|🔴|https://portswigger.net/burp/communitydownload|
|Navegador + DevTools|Inspeccionar cookies, JS, requests, almacenamiento local|🔴|Ya viene en Chrome/Firefox (F12)|
|curl|Probar endpoints rápido desde la terminal sin interfaz gráfica|🔴|Preinstalado en Linux|
|sqlmap|Automatizar detección y explotación de inyección SQL|🟡|https://sqlmap.org/|
|ffuf|Fuerza bruta de directorios, subdominios y parámetros ocultos|🟡|https://github.com/ffuf/ffuf|
|gobuster|Alternativa a ffuf para fuerza bruta de rutas/subdominios|🟢|https://github.com/OJ/gobuster|

### OSINT

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|Google/Yandex reverse image search|Rastrear el origen de una imagen|🔴|https://images.google.com y https://yandex.com/images|
|Wayback Machine|Ver versiones antiguas de un sitio web|🔴|https://web.archive.org/|
|exiftool|Metadatos de imágenes (geolocalización, dispositivo, fecha) — compartido con forense|🔴|https://exiftool.org/|
|Sherlock|Buscar un mismo nombre de usuario en decenas de redes sociales|🟡|https://github.com/sherlock-project/sherlock|
|crt.sh|Buscar certificados SSL para descubrir subdominios|🟡|https://crt.sh/|
|Whois|Información de dominios (propietario, fechas de registro)|🟡|https://who.is/|

### Reversing

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|Ghidra|Desensamblador/decompilador gratuito de NSA, el estándar para reversing|🔴|https://ghidra-sre.org/|
|file / strings|Identificar tipo de archivo y extraer texto legible de un binario|🔴|Preinstalado en Linux|
|gdb + pwndbg o GEF|Depurador de binarios en Linux con extensiones pensadas para CTF|🟡|pwndbg: https://github.com/pwndbg/pwndbg — GEF: https://github.com/hugsy/gef|
|Cutter|Interfaz gráfica de radare2, alternativa más liviana a Ghidra|🟢|https://cutter.re/|

### Esteganografía

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|exiftool|Revisar metadatos, siempre el primer paso|🔴|https://exiftool.org/|
|StegOnline|Analizar imágenes capa por capa, canales de color, LSB, sin instalar nada|🔴|https://stegonline.georgeom.net/upload|
|zsteg|Detectar datos ocultos en imágenes PNG/BMP automáticamente|🟡|https://github.com/zed-0xff/zsteg|
|steghide|Extraer/ocultar datos en JPG/BMP/WAV/AU (con o sin contraseña)|🟡|http://steghide.sourceforge.net/|
|binwalk|También sirve aquí para encontrar archivos ocultos dentro de imágenes|🟡|https://github.com/ReFirmLabs/binwalk|

### Miscelánea (transversal)

|Herramienta|Para qué sirve|Prioridad|Enlace / acceso|
|---|---|---|---|
|CyberChef|También cubre gran parte de "misc" (encodings raros, compresión, etc.)|🔴|https://gchq.github.io/CyberChef/|
|Python 3|Para casi cualquier reto que requiera automatizar algo|🔴|https://www.python.org/|
|7-Zip / unrar / unzip|Descomprimir formatos poco comunes o con contraseña|🔴|https://www.7-zip.org/ (Linux: `apt install p7zip-full unrar unzip`)|

---

## 2. Herramientas secundarias (menos usadas, pero tenerlas "por si acaso")

|Herramienta|Área|Cuándo se usa|Prioridad|Enlace / acceso|
|---|---|---|---|---|
|Nmap|Web/Misc|Si algún reto expone un servicio de red que hay que descubrir/escanear|🟢|https://nmap.org/download.html|
|Netcat (nc)|Web/Reversing/Misc|Conectarse a shells remotas o servicios tipo `nc host puerto` (muy común en pwn/misc)|🟡|Preinstalado en Linux|
|pwntools (Python)|Reversing/Pwn|Automatizar exploits de binarios|🟢|https://github.com/Gallopsled/pwntools|
|Radare2|Reversing|Alternativa a Ghidra en terminal, más rápida para binarios pequeños|🟢|https://github.com/radareorg/radare2|
|Foremost / PhotoRec|Forense|Recuperar archivos borrados o "carving" de archivos dentro de imágenes de disco|🟢|PhotoRec: https://www.cgsecurity.org/wiki/TestDisk_Download|
|Bulk Extractor|Forense|Extraer emails, URLs, tarjetas, etc. de una imagen forense masivamente|🟢|https://github.com/simsong/bulk_extractor|
|NetworkMiner|Forense|Alternativa a Wireshark orientada a reconstrucción de objetos/artefactos|🟢|https://www.netresec.com/?page=NetworkMiner|
|jwt.io / jwt_tool|Web|Analizar y falsificar tokens JWT mal configurados|🟡|https://jwt.io/ y https://github.com/ticarpi/jwt_tool|
|Postman|Web|Alternativa a Burp/curl para probar APIs de forma más visual|🟢|https://www.postman.com/downloads/|
|Aircrack-ng|Misc/Forense|Si aparece algo de captura wifi (.cap/.pcap de 802.11)|🟢|https://www.aircrack-ng.org/|
|Maltego (community)|OSINT|Visualizar relaciones entre entidades (dominios, personas, IPs)|🟢|https://www.maltego.com/downloads/|
|theHarvester|OSINT|Recolectar correos, subdominios y nombres desde fuentes públicas|🟢|https://github.com/laramies/theHarvester|
|zbarimg|Stego/Misc|Decodificar códigos QR ocultos o corruptos|🟢|Paquete `zbar-tools` (`apt install zbar-tools`)|
|Audacity|Stego|Analizar espectrograma de archivos de audio (mensajes ocultos en sonido)|🟡|https://www.audacityteam.org/download/|
|Aperi'Solve|Stego|Versión web que corre varias herramientas de stego (binwalk, zsteg, exiftool) a la vez sobre una imagen|🟡|https://www.aperisolve.com/|

---

## 3. Cheatsheet de comandos Linux básicos

### Navegación y archivos

|Comando|Uso típico en CTF|
|---|---|
|`ls -la`|Ver todos los archivos, incluidos ocultos (empiezan con `.`) — el primer comando en casi cualquier reto de forense/misc|
|`cd <ruta>`|Moverse entre carpetas|
|`pwd`|Confirmar en qué carpeta están trabajando|
|`find / -name "flag*"`|Buscar archivos por nombre (típico para encontrar la flag)|
|`find . -type f -mtime -1`|Buscar archivos modificados recientemente (forense)|
|`cp` / `mv` / `rm`|Copiar, mover o borrar archivos de trabajo|
|`chmod +x archivo`|Dar permisos de ejecución a un binario o script antes de correrlo|

### Inspección de archivos

|Comando|Uso típico en CTF|
|---|---|
|`file archivo`|Identificar el tipo real de un archivo (aunque tenga extensión falsa)|
|`strings archivo`|Extraer texto legible de un binario o archivo — a menudo revela la flag directamente|
|`xxd archivo \| head`|Ver el contenido en hexadecimal (detectar magic bytes, headers falsos)|
|`cat` / `head` / `tail`|Leer contenido de archivos de texto rápidamente|
|`wc -l archivo`|Contar líneas (útil con listas de contraseñas o logs grandes)|
|`diff archivo1 archivo2`|Comparar dos archivos (ej. dos imágenes casi idénticas en stego)|

### Compresión y codificación

|Comando|Uso típico en CTF|
|---|---|
|`unzip archivo.zip`|Descomprimir zips (a veces con `-P` si ya tienen la contraseña)|
|`tar -xvf archivo.tar`|Extraer archivos tar/tar.gz|
|`base64 -d archivo`|Decodificar contenido en base64 desde terminal|
|`gzip -d archivo.gz`|Descomprimir archivos gzip|

### Red

|Comando|Uso típico en CTF|
|---|---|
|`curl -X GET/POST url`|Probar endpoints web sin interfaz gráfica|
|`nc host puerto`|Conectarse a un servicio remoto (muy común en retos "misc" o "pwn": `nc chall.ctf.com 1337`)|
|`wget url`|Descargar archivos de reto desde un enlace|
|`ping host`|Verificar conectividad básica con un servidor del reto|
|`ss -tulpn`|Ver puertos abiertos localmente (útil montando servicios propios)|

### Procesos y permisos

|Comando|Uso típico en CTF|
|---|---|
|`sudo <comando>`|Ejecutar con privilegios de administrador cuando una herramienta lo requiere|
|`ps aux`|Ver procesos corriendo (forense en vivo / escalada de privilegios)|
|`whoami`|Confirmar con qué usuario están corriendo (típico en retos de escalada)|
|`history`|Ver comandos ejecutados anteriormente en la sesión|

### Texto y búsqueda (muy usados juntos)

|Comando|Uso típico en CTF|
|---|---|
|`grep "patron" archivo`|Buscar texto o patrones (ej. `grep -r "flag{" .` para buscar la flag en toda una carpeta)|
|`grep -E "flag\{.*\}"`|Buscar con expresiones regulares el formato típico de flag|
|`awk` / `cut`|Extraer columnas específicas de una salida (logs, csv)|
|`sort` / `uniq -c`|Ordenar y contar líneas repetidas (útil analizando logs o diccionarios)|
|`\| less`|Ver salidas largas página por página en vez de que se corten en la terminal|

---

## Nota

Las herramientas 🔴 conviene tenerlas instaladas y probadas _antes_ de empezar la Fase 1 de práctica. Las 🟡 pueden instalarse durante la Fase 1-2 según en qué área vayan entrando más a fondo. Las 🟢 basta con saber que existen y tenerlas descargadas/ancladas en favoritos para el día de la competencia.