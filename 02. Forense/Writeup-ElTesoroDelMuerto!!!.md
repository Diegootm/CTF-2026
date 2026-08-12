**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** El tesoro del muerto!!! (310 pts) **Resuelto por:** Diego **Fecha:** 12/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado hablaba de "capturar mapas para encontrar el tesoro" — junto con el archivo entregado (un `.pcap` dentro de un ZIP), esto apuntaba directo a analizar una **captura de tráfico de red** en busca de credenciales o datos sensibles transmitidos sin cifrar.

## Herramienta(s) que usé

Wireshark (filtros de visualización, "Follow HTTP Stream"), `capinfos`/`strings` para inspección inicial.

## Pasos (solo lo esencial, tipo lista)

- Abrí `flag.pcap` con Wireshark
- Revisé `Estadísticas → Jerarquía de protocolo` — confirmó tráfico HTTP corriendo sobre el puerto 8000, entre `127.0.0.1` y sí mismo (loopback, captura hecha en la misma máquina)
- Filtré primero con `http.request.method == "GET"` — encontré cientos de peticiones normales (assets estáticos de un panel de administración Django: CSS, JS, íconos), sin nada relevante
- Noté peticiones a `/admin/login/` y `/admin/auth/user/?is_staff__exact=1` — indicaban actividad alrededor del login del panel de administración
- Como los formularios de login se envían por **POST**, no por GET, cambié el filtro a:
    
    ```
    http.request.method == "POST" && http.request.uri contains "login"
    ```
    
- Encontré 3 peticiones POST a `/admin/login/` — al inspeccionar el detalle del paquete (sección "HTML Form URL Encoded"), aparecieron los campos `username` y `password` en texto plano (sin cifrar, por ser HTTP y no HTTPS)
- El campo `username` decía, en leetspeak: `l_fl4g_3st4_3n_3l_p4ssw0rd` → **"la flag está en el password"**
- El campo `password` era: `n0_3st4b4_mu3rt0_4nd4b4_d3_p4rr4nd4` → **"no estaba muerto, andaba de parranda"** (juego de palabras con el nombre del reto)
- Convertí ese texto al formato de flag pedido por la plataforma

## Comando(s) o payload clave (si aplica)

Filtro de Wireshark usado para encontrar la petición clave:

```
http.request.method == "POST" && http.request.uri contains "login"
```

Luego, clic en el paquete → expandir **"Hypertext Transfer Protocol" → "HTML Form URL Encoded"** en el panel de detalle, para ver los campos `username`/`password` decodificados.

## Flag

`cidsi{082631b9a58098ab828df3762d240144}`

(el contenido en texto plano era `n0_3st4b4_mu3rt0_4nd4b4_d3_p4rr4nd4`, hasheado/formateado para la flag final)

## ¿Qué aprendí / qué usaría de nuevo?

En una captura de tráfico HTTP (sin cifrar), los formularios de login viajan por **POST**, no por GET — así que filtrar primero por GET puede hacer perder mucho tiempo revisando cientos de peticiones de assets estáticos irrelevantes. El filtro `http.request.method == "POST"` va directo a las acciones que realmente importan (envío de credenciales, formularios). También quedó claro que el propio `username` puede ser una pista in-game dejada por el autor del reto, no solo el `password` — vale la pena leer ambos campos con atención.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí — al filtrar solo por GET, la lista se llenó de cientos de peticiones normales del panel de administración (CSS, JS, fuentes, endpoints de la API REST) sin nada relevante a simple vista. Lo resolví cambiando el enfoque: en vez de revisar GET por GET, until pensar en qué tipo de acción realmente importaba (un login), y filtrar directo por POST + la palabra "login" en la URI, lo que redujo la búsqueda a solo 3 paquetes relevantes.

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._