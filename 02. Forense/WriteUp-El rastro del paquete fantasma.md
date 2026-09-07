**Área:** Forense **Dificultad:** Media **Plataforma:** UMSS CTF (SCESI) **Link del reto o Nombre:** El rastro del paquete fantasma / Captura de Red (`e79fbd68258cd5c3f1e667be066286e6.pcap`) **Resuelto por:** Axel**Fecha:** 06/09 **Tiempo que tardé:** ~35 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El enlace de wireshark que te daban al habrilo te mostraba puras peticiones DNS lo cual no te ayudaba nada pero alli estaba el detalle después de un largo análisis podemos ver peticiones DNS o conexiones hacia subdominios altamente sospechosos etiquetados explícitamente como **exfil.umssctf.local**.

El atacante utilizó una técnica conocida como **Exfiltración por DNS (DNS Tunneling)**. Si ordenamos cronológicamente las peticiones enviadas a esos servidores internos, obtenemos los siguientes subdominios en orden secuencial:
- c00-ovwxg43dor
- c01-thwzdoonpx
- c02-i5lonyzwym
- c03-lom5pwe2lf
- c04-nzpwgylnov
- c05-tgyndegb6q

Decodificando el canal oculto:
Si extraemos la primera parte de cada subdominio (quitando el identificador del paquete `c00-`, `c01-`, etc.), nos queda la siguiente cadena consecutiva de caracteres:  
ovwxg43dorthwzdoonpxi5lonyzwymlom5pwe2lfnzpwgylnovtgyndegb6q 
Lo cual si analizamos puede ser un mensaje cifrado el cual ya con dcode deciframos.
## Herramienta(s) que usé

- **Wireshark** — para el análisis: En si solo me sirvio para ver los mensajes iocultos y una ves con eso recien encontrar el objetivo
- Dcode: Herramienta para decifrar mensajes lo cual si fue clave por que al meter el mensaje cifrado que encontramos nos da una serie de posibilidades que podrian ser, lo cual habia que probar con las opciones mas acertadas en esete caso era un cifrado en Base32 el cual ya te daba la flag  

## Pasos (solo lo esencial, tipo lista)

- Abrir el archivo con wireshark: una vez alli y despues de un profundo analisis encontrar los mensajes ocualtos en los cuales estaba con el nombre exfil.umssctf.local de alli en orden cronologico nos da una serie de mensajes que podian ayudarnos, al oredenar los mensajes y extraer la primera parte de todos los mensajes unido me daba un mensaje encriptado.
- Dcode: Herramienta que ua vez con el texto codificado la mandamos a analizar y vamos a aver que nos da varias posibilidades de posibles cifrados que pueden ser como Base64,62 y 32 el cual si revisamos un poco la teoria y vemos el texto encriptado no puede ser un base 64 ni 62 lo cual la opcion que queda es la Base 32 el cual es la correcta y con eso te da la flag. 
## Comando(s) o payload clave (si aplica)

## Flag

umssctf{dns_tunn3l1ng_bien_camufl4d0}

## ¿Que aprendí / qué usaría de nuevo?

- Que en un reto como este es facil que te engañen y lo clave sera la habilidad del que esta resolviendo para darse cuenta lo mas rápido posible 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Cuando abrí el archivo en wireshark y no veía que em daba un camino claro tuve que ver pequeños detalles que al final terminaron siendo la clave para resolver el reto
