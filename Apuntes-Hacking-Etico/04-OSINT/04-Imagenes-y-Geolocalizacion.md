# Imágenes y Geolocalización

## Búsqueda inversa de imágenes
Sirve para encontrar el origen de una imagen, otras versiones de ella, o dónde más aparece publicada en internet.

- **Google Images** (búsqueda por imagen): https://images.google.com/
- **Yandex Images**: suele dar mejores resultados que Google para caras y ubicaciones — muy recomendado en OSINT. https://yandex.com/images/
- **TinEye**: especializado en encontrar el origen exacto y versiones anteriores de una imagen. https://tineye.com/

**Tip:** cuando una imagen no da resultados en Google, casi siempre vale la pena probar Yandex — su motor de reconocimiento de rostros y lugares suele ser más efectivo.

## Metadatos de la imagen (el primer paso, siempre)
Antes de cualquier búsqueda inversa, revisen los metadatos con `exiftool` (mismo concepto que en Forense, ver [03-Metadatos](03-Metadatos.md)):
```bash
exiftool imagen.jpg
```
Si la imagen no fue editada/limpiada, puede traer coordenadas GPS exactas de dónde fue tomada, en el campo `GPS Position`. Esas coordenadas se pegan directo en Google Maps.

## Geolocalización "a ojo" (cuando no hay metadatos)
Cuando la imagen no tiene GPS en sus metadatos (lo más común, porque muchas redes sociales lo eliminan al subir), hay que geolocalizar analizando elementos visuales:
- Idioma y alfabeto en carteles/señales.
- Estilo arquitectónico, tipo de vegetación, clima aparente.
- Marcas de autos, patentes (el formato de las placas varía por país/región).
- Postes de luz, señales de tránsito (su diseño varía mucho entre países).
- Sombras: la dirección y longitud de una sombra puede ayudar a estimar hora del día y, con más trabajo, hasta latitud aproximada.

## Herramientas para practicar geolocalización
- **GeoGuessr**: juego que entrena la habilidad de identificar un lugar solo a partir de una imagen de calle — muy recomendado para practicar el "ojo" de geolocalización aunque no sea una herramienta forense en sí. https://www.geoguessr.com/

## Wayback Machine: ver el pasado de un sitio
Permite ver versiones históricas archivadas de una página web, incluso si ya fue borrada o modificada.
```
https://web.archive.org/web/*/example.com
```
Muy útil para encontrar información que un sitio tenía publicada antes y luego eliminó (nombres de empleados, contactos, versiones anteriores de una página "Acerca de").
