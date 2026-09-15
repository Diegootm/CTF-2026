# Firewalls y Segmentación

## ¿Qué es un firewall?
Es un sistema (hardware, software, o ambos) que controla qué tráfico de red puede entrar o salir, en base a un conjunto de **reglas** definidas de antemano. Es la primera línea de defensa de casi cualquier red.

## Tipos de firewall (de más simple a más avanzado)

| Tipo | Cómo filtra |
|---|---|
| **Filtrado de paquetes** | Reglas simples basadas en IP origen/destino, puerto, y protocolo — no entiende el contenido del tráfico |
| **Stateful (con estado)** | Recuerda el estado de las conexiones activas (ej. sabe que un paquete de respuesta corresponde a una conexión que la propia red inició) |
| **Firewall de aplicación (WAF, para web)** | Entiende el contenido de las peticiones HTTP y puede bloquear patrones de ataque específicos (ej. intentos de SQL Injection) |
| **Next-Gen Firewall (NGFW)** | Combina filtrado tradicional con inspección profunda de paquetes, detección de intrusiones, y control por aplicación (no solo por puerto) |

## Reglas de firewall: la idea básica
Una regla típica define: origen, destino, puerto/protocolo, y una acción (permitir o denegar). Por ejemplo, en `iptables` (el firewall estándar de Linux):
```bash
# Permitir tráfico entrante en el puerto 22 (SSH)
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Bloquear todo lo demás por defecto
iptables -A INPUT -j DROP
```

## Segmentación de red
Es la práctica de **dividir una red grande en secciones más pequeñas y aisladas** (subredes o VLANs), de forma que un problema de seguridad en una parte no se propague automáticamente a toda la red. Por ejemplo, separar la red de invitados de la red interna de una empresa, o separar los servidores de producción de las estaciones de trabajo de empleados.

### Por qué importa para un pentester/CTF
En un escenario de red segmentada, comprometer un dispositivo en una subred **no** significa acceso automático a todas las demás — hay que seguir "moviéndose lateralmente" (pivoting) entre segmentos, lo cual suele requerir explotar configuraciones de firewall mal hechas o rutas de confianza entre segmentos que no deberían existir.

## Detección de un firewall durante un escaneo
Cuando un escaneo de nmap muestra muchos puertos como **"filtered"** (en vez de "open" o "closed"), generalmente significa que hay un firewall bloqueando esos paquetes sin responder nada — ni un "sí está abierto" ni un "no, está cerrado", simplemente silencio. Eso mismo es una pista de que hay protección presente.
