# DNS y sus Vulnerabilidades

## ¿Qué es DNS?
DNS (Domain Name System) es el sistema que traduce nombres de dominio legibles para humanos (`google.com`) a direcciones IP que las computadoras realmente usan para comunicarse (`142.250.x.x`). Es, en esencia, la "agenda de contactos" de internet.

## Cómo funciona una consulta DNS (simplificado)
1. Tu computadora pregunta "¿cuál es la IP de `example.com`?" a un servidor DNS (generalmente el de tu proveedor de internet, o uno público como `8.8.8.8` de Google).
2. Si ese servidor no tiene la respuesta guardada (en caché), la va preguntando en cadena: primero a los servidores raíz, luego a los servidores del dominio `.com`, y finalmente al servidor autoritativo de `example.com`.
3. La respuesta final vuelve hasta tu computadora, que ya puede conectarse directamente a esa IP.

## Tipos de registros DNS más comunes

| Tipo | Qué contiene |
|---|---|
| **A** | Dirección IPv4 de un dominio |
| **AAAA** | Dirección IPv6 de un dominio |
| **MX** | Servidor de correo del dominio |
| **TXT** | Texto libre — a veces usado para verificación de dominio, políticas de seguridad (SPF), o... escondido de forma poco convencional en algún reto de CTF |
| **NS** | Servidores de nombres autoritativos del dominio |
| **CNAME** | Alias — apunta a otro nombre de dominio en vez de a una IP directamente |

## Herramientas para consultar DNS manualmente
```bash
# Consulta básica
nslookup example.com

# Más detallado, permite elegir tipo de registro
dig example.com MX
dig example.com TXT
```

## Vulnerabilidades y ataques relacionados a DNS

### DNS Spoofing / Cache Poisoning
Consiste en engañar a un servidor DNS (o a una víctima directamente) para que asocie un dominio legítimo con una IP maliciosa controlada por el atacante — así, cuando la víctima intenta visitar el sitio real, en realidad termina en el sitio falso del atacante.

### Zone Transfer mal configurado (AXFR)
Los servidores DNS a veces necesitan sincronizar su información entre sí mediante una "transferencia de zona". Si un servidor está mal configurado y permite transferencias de zona a cualquiera (no solo a otros servidores DNS autorizados), un atacante puede pedir **toda la lista de subdominios y registros** de una organización de una sola consulta.

```bash
dig axfr @servidor-dns example.com
```
Si funciona, revela una cantidad enorme de información interna (subdominios de desarrollo, servidores internos, etc.) — un fallo de configuración grave pero sorprendentemente común.

### Subdomain Takeover
Ocurre cuando un registro DNS (típicamente un CNAME) apunta a un servicio externo (ej. un hosting en la nube) que ya no está en uso o fue eliminado — un atacante puede registrar ese mismo recurso en el servicio externo y "tomar control" del subdominio, sin necesitar acceso al DNS de la víctima.

## Relación con OSINT
Todo esto se conecta directamente con lo visto en [04-OSINT/05-Dominios-y-Certificados](05-Dominios-y-Certificados.md) — descubrir subdominios (con `crt.sh`, `theHarvester`) es el paso previo natural antes de investigar si alguno de ellos tiene una configuración DNS vulnerable.
