# Ataques Comunes en Redes

## ARP Spoofing (envenenamiento ARP)
### El problema que explota
ARP (Address Resolution Protocol) es el protocolo que traduce direcciones IP a direcciones MAC **dentro de una red local**. El problema es que ARP fue diseñado sin ningún mecanismo de autenticación — cualquier dispositivo en la red puede "anunciar" (sin que nadie lo verifique) que una IP le pertenece a él.

### El ataque
El atacante envía mensajes ARP falsos a la red, haciéndose pasar por el router (o por otro dispositivo), de forma que el tráfico de la víctima empieza a pasar primero por la máquina del atacante antes de llegar a su destino real — esto se conoce como un ataque **Man-in-the-Middle (MITM)**, porque el atacante queda literalmente "en el medio" de la comunicación.

### Consecuencias
- El atacante puede leer todo el tráfico no cifrado de la víctima (sniffing, ver archivo anterior).
- Puede modificar el tráfico en tránsito.
- Puede combinar esto con DNS Spoofing (ver abajo) para redirigir a la víctima a sitios falsos.

## DNS Spoofing (dentro de un ataque MITM)
Una vez que el atacante está "en el medio" (por ejemplo, gracias a ARP Spoofing), puede interceptar las consultas DNS de la víctima y responder con IPs falsas antes de que la respuesta real llegue — logrando que la víctima visite un sitio controlado por el atacante mientras cree estar en el sitio legítimo.

## VLAN Hopping
Las VLANs (redes virtuales) se usan para segmentar una red física en varias redes lógicas separadas, por seguridad y organización. El VLAN Hopping son técnicas para **saltarse esa segmentación** y acceder a una VLAN a la que no se debería tener acceso, generalmente explotando configuraciones por defecto de switches (como el "trunking automático").

## DHCP Starvation
DHCP es el protocolo que asigna automáticamente direcciones IP a los dispositivos que se conectan a una red. Un atacante puede agotar todas las direcciones IP disponibles en el rango de DHCP enviando muchísimas solicitudes falsas — provocando que dispositivos legítimos no puedan obtener IP y quedarse sin acceso a la red (denegación de servicio). A veces se combina con montar un **servidor DHCP falso (Rogue DHCP)** que responde primero, entregando configuración maliciosa (ej. un DNS controlado por el atacante) a los nuevos dispositivos.

## MAC Flooding
Ataca a los switches directamente: se inundan con miles de direcciones MAC falsas hasta agotar su tabla de memoria (CAM table). Cuando la tabla se llena, muchos switches "fallan de forma insegura" y empiezan a comportarse como un simple hub, transmitiendo todo el tráfico a todos los puertos — lo que permite sniffear tráfico que normalmente no debería ser visible.

## Tabla resumen

| Ataque | Qué explota | Consecuencia principal |
|---|---|---|
| ARP Spoofing | Falta de autenticación en ARP | Man-in-the-Middle, sniffing de tráfico ajeno |
| DNS Spoofing | Falta de verificación en respuestas DNS | Redirección a sitios falsos |
| VLAN Hopping | Configuraciones por defecto de switches | Acceso a segmentos de red restringidos |
| DHCP Starvation | Falta de límite de solicitudes DHCP | Denegación de servicio / DHCP falso |
| MAC Flooding | Límite de memoria de la tabla CAM del switch | El switch se comporta como hub, facilita sniffing |

## Cómo se relacionan con lo que ya vieron
Todos estos ataques comparten un patrón con lo visto en [Web - Fundamentos de Explotación](01-Fundamentos-de-Explotacion.md): identificar un protocolo/servicio que confía demasiado en la buena fe de quien lo usa, y aprovechar esa confianza para lograr un efecto no previsto por sus diseñadores.
