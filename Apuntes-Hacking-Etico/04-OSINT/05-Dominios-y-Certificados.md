# Dominios y Certificados

## Whois: información de un dominio
El registro Whois muestra quién registró un dominio, cuándo, y cuándo vence (aunque muchos dueños ocultan sus datos personales con servicios de privacidad).
```bash
whois example.com
```
O de forma web: https://who.is/

Datos que a veces revela:
- Fecha de registro y expiración.
- Servidor DNS usado.
- Registrante (si no está oculto por privacidad).

## crt.sh: certificados SSL y subdominios
Cada vez que se emite un certificado SSL/TLS para un dominio, queda registrado públicamente en los **Certificate Transparency Logs (CT Logs)**. `crt.sh` es una interfaz que permite buscar en esos registros.

```
https://crt.sh/?q=example.com
```

**Por qué importa en OSINT:** es una de las formas más efectivas de descubrir **subdominios** de una organización, porque cada subdominio que alguna vez tuvo un certificado SSL queda registrado ahí — incluso subdominios de desarrollo/staging que no deberían ser públicos pero terminan filtrados por esta vía.

## Combinando con theHarvester
Como vimos en el archivo anterior, `theHarvester` puede usar `crtsh` como una de sus fuentes automáticamente:
```bash
theHarvester -d example.com -b crtsh
```

## Flujo típico de investigación de un dominio
1. `whois` para ver quién lo registró y datos generales.
2. `crt.sh` (o `theHarvester -b crtsh`) para descubrir subdominios.
3. Revisar cada subdominio encontrado — a veces hay paneles de administración, versiones de desarrollo, o servicios olvidados que exponen información.
4. Cruzar con Wayback Machine para ver si alguno de esos subdominios tenía contenido distinto en el pasado.
