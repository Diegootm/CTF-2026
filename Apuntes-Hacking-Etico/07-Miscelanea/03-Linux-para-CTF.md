# Linux para CTF

Cheatsheet de comandos que van a usar constantemente, sin importar el área del reto. No hace falta memorizarlos de una — la idea es tenerlos a mano como referencia.

## Navegación y archivos

| Comando | Uso típico en CTF |
|---|---|
| `ls -la` | Ver todos los archivos, incluidos ocultos (empiezan con `.`) — el primer comando en casi cualquier reto de forense/misc |
| `cd <ruta>` | Moverse entre carpetas |
| `pwd` | Confirmar en qué carpeta están trabajando |
| `find / -name "flag*"` | Buscar archivos por nombre (típico para encontrar la flag) |
| `find . -type f -mtime -1` | Buscar archivos modificados recientemente (forense) |
| `cp` / `mv` / `rm` | Copiar, mover o borrar archivos de trabajo |
| `chmod +x archivo` | Dar permisos de ejecución a un binario o script antes de correrlo |

## Inspección de archivos

| Comando | Uso típico en CTF |
|---|---|
| `file archivo` | Identificar el tipo real de un archivo (aunque tenga extensión falsa) |
| `strings archivo` | Extraer texto legible de un binario — a menudo revela la flag directamente |
| `xxd archivo \| head` | Ver el contenido en hexadecimal (detectar magic bytes, headers falsos) |
| `cat` / `head` / `tail` | Leer contenido de archivos de texto rápidamente |
| `wc -l archivo` | Contar líneas (útil con listas de contraseñas o logs grandes) |
| `diff archivo1 archivo2` | Comparar dos archivos (ej. dos imágenes casi idénticas en stego) |

## Compresión y codificación

| Comando | Uso típico en CTF |
|---|---|
| `unzip archivo.zip` | Descomprimir zips (a veces con `-P` si ya tienen la contraseña) |
| `tar -xvf archivo.tar` | Extraer archivos tar/tar.gz |
| `base64 -d archivo` | Decodificar contenido en base64 desde terminal |
| `gzip -d archivo.gz` | Descomprimir archivos gzip |

## Red

| Comando | Uso típico en CTF |
|---|---|
| `curl -X GET/POST url` | Probar endpoints web sin interfaz gráfica |
| `nc host puerto` | Conectarse a un servicio remoto (muy común en retos "misc" o "pwn": `nc chall.ctf.com 1337`) |
| `wget url` | Descargar archivos de reto desde un enlace |
| `ping host` | Verificar conectividad básica con un servidor del reto |
| `ss -tulpn` | Ver puertos abiertos localmente (útil montando servicios propios) |

## Procesos y permisos

| Comando | Uso típico en CTF |
|---|---|
| `sudo <comando>` | Ejecutar con privilegios de administrador cuando una herramienta lo requiere |
| `ps aux` | Ver procesos corriendo (forense en vivo / escalada de privilegios) |
| `whoami` | Confirmar con qué usuario están corriendo (típico en retos de escalada) |
| `history` | Ver comandos ejecutados anteriormente en la sesión |

## Texto y búsqueda (muy usados juntos)

| Comando | Uso típico en CTF |
|---|---|
| `grep "patron" archivo` | Buscar texto o patrones (ej. `grep -r "flag{" .` para buscar la flag en toda una carpeta) |
| `grep -E "flag\{.*\}"` | Buscar con expresiones regulares el formato típico de flag |
| `awk` / `cut` | Extraer columnas específicas de una salida (logs, csv) |
| `sort` / `uniq -c` | Ordenar y contar líneas repetidas (útil analizando logs o diccionarios) |
| `\| less` | Ver salidas largas página por página en vez de que se corten en la terminal |

## Regla de oro
Ante cualquier archivo desconocido, la secuencia casi automática debería ser: `file` → `strings \| grep -i flag` → `exiftool` → herramienta específica del área. Este orden resuelve una fracción sorprendente de retos "fáciles" antes de necesitar herramientas más pesadas.
