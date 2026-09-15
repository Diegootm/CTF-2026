# Búsqueda de Personas, Correos y Usuarios

## theHarvester
Herramienta en Python diseñada para recolectar direcciones de correo electrónico, subdominios, direcciones IP, nombres de host y URLs asociadas a un dominio.

### Parámetros principales

| Parámetro | Descripción | Ejemplo |
|---|---|---|
| `-d` | Dominio objetivo (requerido) | `-d google.com` |
| `-l` | Límite de resultados a buscar | `-l 500` |
| `-b` | Fuente de datos a utilizar | `-b google` |
| `-s` | Realizar un escaneo de DNS | `-s` |
| `-v` | Verificar el nombre de host vía DNS | `-v` |
| `-f` | Guardar resultados en un archivo | `-f resultados.html` |
| `-g` | Utilizar Google Dorks | `-g` |

### Ejemplos de uso
```bash
# Búsqueda básica sobre un dominio
theHarvester -d spotify.com -b duckduckgo -l 50

# Descubrir subdominios y puntos de entrada, guardando resultados
theHarvester -d microsoft.com -b dnsdumpster,certspotter,crtsh -l 300 -f microsoft_subs.html
```

Fuentes de datos útiles para combinar (parámetro `-b`):
- **dnsdumpster**: mapeo de dominios.
- **crt.sh**: buscador de certificados, interfaz directa a los CT Logs (Certificate Transparency), un registro público de certificados SSL.
- **certspotter**: servicio de monitoreo de CT Logs.

## Holehe: verificar dónde está registrado un correo
Permite comprobar en qué plataformas/servicios está registrado un correo electrónico (sin necesitar la contraseña — solo revisa si el correo "existe" en el sistema de registro de cada servicio).

```bash
# Escaneo básico de un correo
holehe email@gmail.com

# Escaneo limpio (solo servicios donde SÍ está registrado, sin colores)
holehe email@gmail.com --only-used --no-color

# Escanear varios correos desde un archivo
holehe -f emails.txt

# Guardar resultados en JSON
holehe email@gmail.com -o resultados.json
```

## Sherlock: buscar un usuario en redes sociales
Busca un mismo nombre de usuario en decenas de plataformas y redes sociales de una sola vez.
```bash
sherlock nombre_de_usuario
```
Es especialmente útil cuando el reto da un nombre de usuario y hay que encontrar en qué otras redes esa misma persona lo usa (la gente reutiliza usuarios entre plataformas constantemente).

## Flujo típico combinando estas tres herramientas
1. Si tienen un **dominio**, usen theHarvester para sacar correos y subdominios asociados.
2. Si tienen un **correo**, usen holehe para ver en qué plataformas está registrado.
3. Si tienen un **nombre de usuario**, usen Sherlock para encontrar sus otras cuentas — y desde ahí, seguir investigando cada perfil manualmente.
