# Reconocimiento y Enumeración

Antes de atacar una vulnerabilidad específica, hay que **mapear el terreno**: qué tecnologías usa el sitio, qué rutas existen, qué servicios están corriendo. Este paso se llama reconocimiento (o "recon") y ahorra muchísimo tiempo en retos donde la vulnerabilidad no salta a la vista.

## curl y wget: pruebas básicas de petición/respuesta

### curl
Herramienta de línea de comandos para hacer peticiones HTTP/HTTPS/FTP. Muy útil para inspeccionar headers, probar formularios, enviar datos.

```bash
# Ver el código fuente de una web
curl http://example.com

# Ver solo los headers de respuesta
curl -I http://example.com

# Enviar datos por POST (ej. simular un login)
curl -X POST -d "usuario=admin&pass=123" http://example.com/login
```

### wget
Sirve para descargar contenido desde la web — ideal para clonar una página o bajar un archivo específico.
```bash
wget http://example.com
wget http://example.com/archivo.zip
```

## netcat (nc): manipulación de sockets y escaneo básico
Herramienta de red versátil para leer y escribir datos sobre conexiones TCP/UDP.

```bash
# Probar si un puerto está abierto
nc example.com 80

# Enviar una petición HTTP manualmente (después de conectar, escribir):
# GET / HTTP/1.1
# Host: example.com

# Crear un servidor en escucha (útil para recibir shells reversas)
nc -lvnp 4444
```

## nmap: detección de puertos y servicios
Escáner de red para descubrir hosts y servicios activos — clave para saber qué tecnologías corren en un servidor antes de atacarlo.

```bash
nmap example.com                  # escaneo básico de puertos
nmap -sV example.com              # detectar servicios y versiones
nmap -O example.com               # detección de sistema operativo
nmap --script http-enum -p 80 example.com   # scripts de enumeración web
```

### Tabla de escaneos útiles

| Propósito | Comando | Descripción |
|---|---|---|
| Descubrimiento de red | `nmap -sn 192.168.1.0/24` | Ver quién está conectado sin escanear puertos |
| Escaneo ultrarrápido | `nmap -F [IP]` | Solo los 100 puertos más comunes |
| Escaneo agresivo (todo en uno) | `nmap -A [IP]` | SO + versiones + scripts básicos + traceroute (muy ruidoso) |
| Escaneo de puertos UDP | `nmap -sU [IP]` | DNS (53), SNMP (161) usan UDP y muchos lo olvidan |
| Guardar en formato grepable | `nmap -sS -oG resultados.txt [IP]` | Para buscar después con `grep` |
| Escaneo desde archivo | `nmap -iL objetivos.txt` | Escanea una lista de IPs de una sola vez |
| Obtener banners | `nmap -sV --script=banner [IP]` | Extrae el mensaje de bienvenida de un servicio |
| Vulnerabilidades conocidas (ej. SMB) | `nmap --script smb-vuln* -p 445 [IP]` | Motor NSE buscando fallas conocidas |

## whatweb: identificar tecnologías web
Descubre qué CMS, framework, servidor y plugins usa un sitio.
```bash
whatweb http://example.com
```
Salida típica:
```
http://example.com [200 OK] Apache[2.4.1], PHP[7.4], WordPress, Google Analytics
```
Ideal para **reconocimiento pasivo** antes de lanzar pruebas más intrusivas.

## dirb / gobuster: encontrar rutas ocultas
Buscan archivos y directorios ocultos mediante fuerza bruta con un diccionario de nombres comunes.

```bash
# dirb
dirb http://example.com
dirb http://example.com /usr/share/wordlists/dirb/common.txt

# gobuster (más rápido, escrito en Go)
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt
```
Ideal para encontrar rutas como `/admin`, `/login`, `/config.php`, `/backup`, etc. — muy común que ahí esté el punto de entrada real del reto.
