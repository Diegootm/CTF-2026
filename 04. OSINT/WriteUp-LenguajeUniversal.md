**Área:** OSINT **Dificultad:** - **Plataforma:** CIDSI **Link del reto o Nombre:** Lenguaje Universal (300 pts) **Resuelto por:** Diego **Fecha:** 27/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El mensaje decía: "Si quieres comunicarte conmigo este es mi número 7A:F8:82:C8:33:03", acompañado de una imagen. El formato de ese "número" (6 pares hexadecimales separados por `:`) es el formato exacto de una **dirección MAC/BSSID**, no un número de teléfono. La imagen adjunta mostraba el Palacio Legislativo / Plaza Murillo en La Paz, Bolivia, dando la zona donde buscar esa red WiFi.

## Herramienta(s) que usé

- `exiftool` (para revisar metadatos de la imagen, confirmó autoría `0xb3t0` pero sin coordenadas GPS)
- `binwalk` (para descartar esteganografía, la imagen no tenía nada oculto)
- WiGLE.net (base de datos de redes WiFi geolocalizadas por wardriving)
- DevTools del navegador (Inspeccionar elemento) para confirmar el nombre exacto sin recortes visuales

## Pasos (solo lo esencial, tipo lista)

- Analicé la imagen adjunta y confirmé que correspondía al Palacio Legislativo/Plaza Murillo, La Paz
- Revisé metadatos con `exiftool -a -u -g1` y `binwalk` — sin datos ocultos relevantes, solo confirmó autoría
- Identifiqué que `7A:F8:82:C8:33:03` es un BSSID (MAC de punto de acceso WiFi)
- Busqué el BSSID directo en WiGLE.net sin resultados al inicio (rate limit de la cuenta gratuita)
- Acoté la búsqueda a un radio pequeño alrededor de las coordenadas exactas del Palacio Legislativo
- Encontré el punto en el mapa con el BSSID exacto y el nombre de red parcialmente visible: "...mela cumbierita"
- Confirmé el nombre completo sin recortes usando "Inspeccionar elemento" del navegador sobre el resultado
- El SSID completo era exactamente: `mela cumbierita`
- Usé ese nombre como respuesta, formateado como pedía el reto

## Comando(s) o payload clave (si aplica)

```
BSSID buscado: 7A:F8:82:C8:33:03

Coordenadas usadas en WiGLE (radio pequeño alrededor del Palacio Legislativo, La Paz):
Lat: -16.497   to: -16.495
Lon: -68.134   to: -68.132

Fuente: WiGLE.net (búsqueda por coordenadas del Palacio Legislativo, La Paz)
SSID encontrado: mela cumbierita
```

## Flag

`cidsi{85429083c4c82fec0927ca4cbe5f254d}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un "número" viene en formato de pares hexadecimales separados por `:`, es una dirección MAC, no un teléfono — y WiGLE.net es la herramienta correcta para buscar a qué red WiFi (SSID) corresponde un BSSID, usando la imagen/ubicación como pista para acotar la zona de búsqueda cuando la cuenta gratuita tiene límite de consultas. También aprendí a usar "Inspeccionar elemento" del navegador para confirmar texto que visualmente aparece recortado o tapado por un ícono en la interfaz.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, en dos partes: (1) WiGLE limitó las consultas de mi cuenta gratuita varias veces ("too many queries"), lo resolví acotando mucho el rango de coordenadas para gastar menos consultas y centrando la búsqueda justo en el Palacio Legislativo; (2) el nombre de la red se veía visualmente tapado por el ícono del router en el popup de resultados, lo confirmé sin ambigüedad abriendo el HTML con "Inspeccionar elemento" del navegador.

---
