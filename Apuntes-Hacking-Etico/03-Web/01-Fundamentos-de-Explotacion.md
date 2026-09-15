# Fundamentos de Explotación

## ¿Qué es "explotar" una vulnerabilidad?
**Exploiting** es el proceso de convertir una vulnerabilidad (un defecto o error en un sistema) en un efecto práctico y controlado. Implica diseñar y ejecutar una entrada, secuencia de peticiones, o código que dispare el fallo y te dé un resultado concreto: leer datos que no deberías, saltarte un login, escalar privilegios, o ejecutar comandos en el servidor.

## Vocabulario que hay que tener claro

| Término | Significado |
|---|---|
| **Vulnerabilidad** | Un defecto en el sistema que puede ser aprovechado |
| **Exploit** | La técnica o artefacto concreto que dispara/aprovecha esa vulnerabilidad |
| **Payload** | Lo que hace el exploit una vez que "abrió la puerta" (ej. el código que se ejecuta, el comando que se corre) |

## Web Exploitation en el contexto de la ciberseguridad
Es el proceso de identificar y aprovechar vulnerabilidades en aplicaciones web para obtener acceso no autorizado, manipular datos, evadir controles, o ejecutar acciones imprevistas. Forma parte de disciplinas como el **hacking ético**, el **pentesting**, y por supuesto los **CTF**.

Es un campo especialmente relevante porque las aplicaciones web están disponibles públicamente por diseño — eso las convierte en un blanco constante de atacantes reales, y por eso las empresas invierten en auditorías de seguridad.

## Pentesting ético vs. explotación maliciosa

| Característica | Pentesting (Ético) | Explotación Maliciosa (Ilegal) |
|---|---|---|
| Propósito | Evaluar y mejorar la seguridad | Obtener beneficio personal o dañar |
| Permiso | Siempre con autorización legal | Sin consentimiento del propietario |
| Herramientas | Controladas y documentadas | Puede incluir malware o scripts dañinos |
| Resultado | Reportes y recomendaciones de mejora | Robo de datos, sabotaje, chantaje |
| Intención | Protección y prevención | Ataque y aprovechamiento |

**Ejemplo:** un pentester que descubre un XSS lo reporta para que se arregle. Un atacante malicioso usaría el mismo XSS para robar cookies de sesión de usuarios reales.

## El ciclo típico de un exploit contra un servidor

1. **Identificación**: se escanean los puertos del servidor para ver qué está expuesto.
2. **Análisis**: se detecta qué servicio y versión corre en cada puerto, y se busca si esa versión tiene vulnerabilidades públicas conocidas.
3. **Construcción o uso del exploit**: se adapta un exploit ya público o se construye uno nuevo a medida.
4. **Ejecución**: se lanza el exploit contra el servicio y se verifica si logró su efecto (ejecutar código, leer datos, etc.).
5. **Post-explotación**: si se logró acceso, el siguiente paso suele ser establecer una shell remota, escalar privilegios si hace falta, y (en un entorno real, no CTF) mantener el acceso.

Este ciclo es la base mental detrás de casi cualquier reto de Web, y también de Miscelánea/Pwn — antes de meterse en un tipo de vulnerabilidad específica (SQLi, XSS, etc.), conviene tener este flujo internalizado.
