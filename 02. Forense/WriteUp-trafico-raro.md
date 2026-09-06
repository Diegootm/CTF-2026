**Área:** Forense **Dificultad:** Media **Plataforma:** UMSS CTF (SCESI) **Link del reto o Nombre:** Tráfico Raro / Captura de Red (`4847a62673c33ec1bb86fe9e9a910e89.gz`) **Resuelto por:** Diego **Fecha:** 31/08 **Tiempo que tardé:** ~35 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

Abriendo la captura en Wireshark: **~7279 peticiones HTTP idénticas** `POST /flag` (localhost → localhost, captura de loopback), todas con el mismo cuerpo `caracter=X&cuerpo=<~1000 chars aleatorios>` y todas contestadas con `501`. Claramente una **exfiltración carácter a carácter** ahogada en ruido.

Las 2 pistas que rompieron el reto:
1. Exactamente **2 peticiones** tienen `caracter=%7B` y `caracter=%7D` → son las llaves `{` y `}` de la flag (URL-encoded).
2. Esas 2 peticiones comparten **`Content-Length: 1102`**. Ese es el "sello" de los caracteres reales: el resto usa longitudes de `cuerpo` aleatorias. La propia flag lo confirma: *"el length es importante"*.

## Herramienta(s) que usé

- **Wireshark** — para el análisis: `Statistics → Conversations` (todo HTTP:80 loopback, miles de conexiones cortas), filtros de display para aislar las peticiones y descubrir el patrón `caracter`/`cuerpo` y el `Content-Length` distintivo.
- **Python + Scapy** (script propio) — para el paso final: reensamblar los cuerpos de los POST, filtrar `Content-Length == 1102` y ordenar por tiempo. (En la máquina no estaba `tshark`; con él bastaría un one-liner.)

## Pasos (solo lo esencial, tipo lista)

- `gunzip 4847a62673c33ec1bb86fe9e9a910e89.gz` → `pcapng` de 21 MB, captura de loopback (Windows), 55 s, 109k paquetes.
- Abrir en **Wireshark**:
  - `Statistics → Protocol Hierarchy`: casi todo TCP:80 (HTTP). 12 paquetes SSDP (`M-SEARCH`) = ruido de red normal.
  - Filtro `http.request` → 7279 × `POST /flag`, cuerpo `caracter=<1 char>&cuerpo=<~1000 base62 aleatorios>`.
  - Filtro `http.response` → todas `501 Unsupported method`, sin datos útiles.
  - Filtro `http.request and frame contains "caracter=%7"` → **2 paquetes**: `caracter=%7B` y `caracter=%7D` (las llaves).
  - Mirando esos 2 paquetes: ambos con `Content-Length: 1102`.
- Filtro **`http.request and http.content_length == 1102`** → **35 paquetes**.
- Leer el campo `caracter` de esos 35 **en orden de captura** → se arma la flag.
  - (`cuerpo` es relleno aleatorio; las otras ~7244 peticiones con `caracter` aleatorio son señuelo puro.)
- Resultado: `umssctf{3l_l3ngth_3s_1mp0rt4nt3_27}`.

## Comando(s) o payload clave (si aplica)

En Wireshark (para verlo a mano):
```
# aislar los 35 caracteres reales
http.request and http.content_length == 1102
# luego: click en cada paquete -> HTML Form URL Encoded -> campo "caracter", en orden
```

Script para automatizarlo:
```python
#!/usr/bin/env python3
# extraer_flag.py
# Hay 7279 POST /flag identicos con cuerpo  caracter=X&cuerpo=<basura>.
# Casi todo es ruido. Los caracteres REALES de la flag son los de las
# peticiones cuyo Content-Length es exactamente 1102 (las llaves { } van
# como %7B / %7D y comparten ese mismo Content-Length).

from scapy.all import PcapNgReader, TCP, Raw
from collections import defaultdict
import urllib.parse

PCAP = "4847a62673c33ec1bb86fe9e9a910e89"        # ya descomprimido con gunzip

# 1) reagrupar segmentos TCP por puerto origen (cada peticion abre uno nuevo)
segmentos, primer_tiempo = defaultdict(list), {}
for pkt in PcapNgReader(PCAP):
    if TCP in pkt and pkt[TCP].dport == 80 and Raw in pkt:
        sp = int(pkt[TCP].sport)
        segmentos[sp].append((int(pkt[TCP].seq), bytes(pkt[Raw].load)))
        primer_tiempo.setdefault(sp, float(pkt.time))

# 2) rearmar cada peticion y sacar (tiempo, Content-Length, caracter)
peticiones = []
for sp, segs in segmentos.items():
    segs.sort()
    vistos, datos = set(), b""
    for seq, d in segs:
        if seq in vistos:
            continue
        vistos.add(seq)
        datos += d
    if b"POST /flag" not in datos or b"\r\n\r\n" not in datos:
        continue
    cabecera, cuerpo = datos.split(b"\r\n\r\n", 1)
    cl = 0
    for linea in cabecera.split(b"\r\n"):
        if linea.lower().startswith(b"content-length:"):
            cl = int(linea.split(b":")[1])
    cuerpo = cuerpo[:cl].decode("latin1")
    params = dict(p.split("=", 1) for p in cuerpo.split("&") if "=" in p)
    caracter = urllib.parse.unquote(params.get("caracter", ""))
    peticiones.append((primer_tiempo[sp], cl, caracter))

# 3) filtrar Content-Length == 1102 y ordenar por tiempo
reales = sorted((t, c) for (t, cl, c) in peticiones if cl == 1102)
print("Flag:", "".join(c for _, c in reales))
```

## Flag

`umssctf{3l_l3ngth_3s_1mp0rt4nt3_27}`

## ¿Que aprendí / qué usaría de nuevo?

- Ante miles de peticiones "iguales", buscar el paquete **único/raro** (aquí las llaves `%7B`/`%7D`) y sacar de él la característica que distingue lo real del ruido.
- En Wireshark, `http.content_length` es un filtro de display buenísimo para separar señal de ruido cuando el tamaño es el canal encubierto.
- El `cuerpo` con longitud aleatoria era una distracción para que no te fijaras en que **el Content-Length** era el que importaba (y la flag lo dice literalmente).

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí. Primero probé concatenar TODOS los `caracter` en orden (7279 chars) y buscar la flag dentro: no salía. Luego probé agrupar por longitud de `cuerpo` y votar por mayoría: tampoco (no hay repetición). Se destrabó al mirar **las dos peticiones de las llaves** y notar que compartían `Content-Length: 1102`; filtrando por ese valor quedaron exactamente los 35 caracteres de la flag.
