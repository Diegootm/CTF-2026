**Área:** Forense **Dificultad:** medio **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Rogue Tower **Resuelto por:** Xavi **Fecha:** 14/09 **Tiempo que tardé:** ~30 min

---

### ¿Qué pista/detalle me hizo saber por dónde ir?

El reto entregaba un archivo `.pcap`, así que empecé asumiendo que la flag estaba oculta en el tráfico de red capturado. Al inspeccionar los paquetes noté que había dos torres celulares legítimas (Verizon y AT&T) transmitiendo normalmente por broadcast UDP en el puerto 55000, y de repente aparecía una tercera transmisión con el texto `UNAUTHORIZED-TEST-NETWORK` — eso fue la señal clara de que se trataba de una torre celular falsa (IMSI-catcher) y que ahí estaba el hilo a seguir.

### Herramienta(s) que usé

- Scapy (Python) — para parsear el pcap y extraer paquetes/payloads
- Análisis manual de headers HTTP (User-Agent)
- Python (XOR bruteforce / crib-dragging) — para romper el cifrado del payload exfiltrado

### Pasos (solo lo esencial, tipo lista)

- Filtré los broadcasts UDP en el puerto 55000 y encontré la torre no autorizada: `UNAUTHORIZED-TEST-NETWORK PLMN=00101 CELLID=92058`
- Busqué en los headers `User-Agent` de las peticiones `GET /api/register` qué dispositivo se conectó a esa celda falsa (`CELL:92058`) → IMSI de la víctima: `310410308555787`
- Detecté varias peticiones `POST /upload` fragmentadas hacia el servidor del atacante y reconstruí el string en base64 uniendo los fragmentos
- Decodifiqué el base64, obteniendo bytes cifrados (no texto plano)
- Intenté primero XOR con los últimos 4 dígitos del IMSI → solo alineaba parcialmente (falso positivo parcial)
- Hice crib-dragging con la palabra `picoCTF{` contra el ciphertext en todas las posiciones, y en la posición 0 obtuve como clave `08555787` — los **últimos 8 dígitos** del IMSI de la víctima
- Apliqué XOR repetido con esa clave sobre todo el bloque y obtuve la flag completa

### Comando(s) o payload clave (si aplica)

python

```python
key = b'08555787'  # ultimos 8 digitos del IMSI de la victima
out = bytes([b[i] ^ key[i % len(key)] for i in range(len(b))])
print(out.decode())
```

### Flag

```
picoCTF{r0gu3_c3ll_t0w3r_dbc40831}
```

### ¿Qué aprendí / qué usaría de nuevo?

Que cuando un XOR "casi" funciona (da texto imprimible pero sin sentido, o solo coincide parcialmente con la palabra que buscas) no significa que la clave sea correcta — hay que seguir probando longitudes de clave distintas y usar crib-dragging con la palabra exacta esperada (en este caso `picoCTF{` en vez de solo `CTF{`) para confirmar la clave real letra por letra.

### ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, me trabé asumiendo que la clave eran los últimos 4 dígitos del IMSI, porque coincidencialmente esa clave sí producía texto imprimible y hasta contenía fragmentos como "CTF{" y "0w3r" — un falso positivo bastante convincente. Me destrabé cuando until me diste el dato de que el formato real era `picoCTF{}` y no `CTF{}`; al usar ese prefijo completo como crib para derivar la clave, until la posición 0 me devolvió limpiamente `08555787`, confirmando que la clave correcta usaba 8 dígitos del IMSI, no 4.