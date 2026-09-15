# Herramientas de Web

| Herramienta | Para qué sirve | Enlace |
|---|---|---|
| Burp Suite (Community) | Interceptar y modificar peticiones HTTP, repetir requests, fuzzing básico | https://portswigger.net/burp/communitydownload |
| Navegador + DevTools | Inspeccionar cookies, JS, requests, almacenamiento local | Integrado en Chrome/Firefox (tecla F12) |
| curl | Probar endpoints desde la terminal | Preinstalado en Linux |
| wget | Descargar páginas/archivos | Preinstalado en Linux |
| sqlmap | Automatizar detección/explotación de SQL Injection | https://sqlmap.org/ |
| ffuf | Fuerza bruta de directorios, subdominios y parámetros | https://github.com/ffuf/ffuf |
| gobuster | Alternativa a ffuf/dirb, escrita en Go | https://github.com/OJ/gobuster |
| dirb | Fuerza bruta de rutas con diccionario | Preinstalado en Kali |
| nmap | Escaneo de puertos y servicios | https://nmap.org/download.html |
| whatweb | Identificar tecnologías/CMS de un sitio | Preinstalado en Kali |
| netcat (nc) | Conectarse a servicios, crear listeners para shells | Preinstalado en Linux |
| jwt.io / jwt_tool | Analizar y falsificar tokens JWT mal configurados | https://jwt.io/ y https://github.com/ticarpi/jwt_tool |
| Postman | Probar APIs de forma visual (alternativa a Burp/curl) | https://www.postman.com/downloads/ |
| PortSwigger Web Security Academy | El mejor recurso gratuito para practicar cada vulnerabilidad con labs guiados | https://portswigger.net/web-security |

## Diccionarios útiles
- `/usr/share/wordlists/dirb/common.txt` — rutas comunes (viene en Kali).
- `/usr/share/wordlists/rockyou.txt.gz` — contraseñas comunes (descomprimir con `gunzip`).
- SecLists: colección enorme de wordlists para todo tipo de fuzzing. https://github.com/danielmiessler/SecLists
