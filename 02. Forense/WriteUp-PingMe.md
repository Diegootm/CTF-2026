**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** ping me **Resuelto por:** Diego **Fecha:** 12/08 **Tiempo que tardé:** 20 minutos

---

**¿Qué pista/detalle me hizo saber por dónde ir?**  
El enunciado hablaba de estar en un SOC revisando tráfico de red "que aunque parezca habitual" generaba sospecha. Al abrir el `.pcap` en Wireshark, noté que era puro tráfico ICMP (pings), y que el campo `Data` de los paquetes no tenía el contenido típico y predecible de un ping normal (que normalmente es solo relleno con letras del alfabeto repetidas) — en cambio, cada paquete traía un valor distinto tipo `6f00`, lo cual no encajaba con tráfico ICMP legítimo.

**Herramienta(s) que usé**  
Wireshark (inspección inicial), `tshark` (extracción en línea de comandos de los campos Data), CyberChef (conversión de hexadecimal a texto).

**Pasos (solo lo esencial, tipo lista)**

- Abrí el `.pcap` en Wireshark y filtré por `icmp`
- Noté que el campo `Data` de cada paquete tenía un valor hexadecimal distinto, sospechoso de ser un túnel de exfiltración de datos por ICMP
- Instalé `tshark` para extraer el campo `Data` de todos los paquetes ICMP tipo Echo Request de forma masiva y ordenada
- Tomé cada valor de 2 bytes (ej. `6f00`) y usé solo el primer byte (`6f`) como el carácter real del mensaje, descartando el `00` de relleno
- Pasé toda la secuencia de bytes por CyberChef (De Hexadecimal) para reconstruir el mensaje completo
- El mensaje reconstruido fue directamente la flag en texto plano

**Comando(s) o payload clave (si aplica)**

bash

```bash
tshark -r flag.pcap -Y "icmp.type==8" -T fields -e data
```

Luego cada línea (ej. `6f00`) se interpreta tomando solo el primer byte (`6f` = 'o') como el carácter real del mensaje oculto, y se concatenan en orden.

**Flag**  
`Morteruelo2022{1d7a90a63039831c7fcaa53b766d5b2d}`

**¿Qué aprendí / qué usaría de nuevo?**  
El tráfico ICMP "normal" (ping) tiene un patrón de datos predecible y repetitivo; cualquier desviación de ese patrón en el campo `Data` es una señal fuerte de **túnel ICMP** — una técnica real de exfiltración de datos que aprovecha que muchos firewalls dejan pasar el tráfico ICMP sin inspeccionarlo a fondo, por considerarlo "solo un ping". `tshark -T fields -e data` es mucho más rápido que revisar paquete por paquete en la interfaz gráfica cuando hay que extraer un mismo campo de cientos de paquetes.

**¿Me trabé en algo? ¿Cómo lo destrabé?**  
Sí, dos cosas: no tenía `tshark` instalado (lo instalé con `sudo apt install tshark`), y después de armar la flag pensé que el hash dentro de las llaves podría estar cifrado o necesitar romperse aparte — lo descarté probando que no decodifica como texto ASCII ni coincide con palabras comunes en MD5, confirmando que era simplemente el identificador final de la flag tal como lo generó la plataforma.



---






