# Metadatos

## ¿Qué son?
Los metadatos son "datos sobre datos" — información adicional guardada dentro (o junto a) un archivo, que no es el contenido principal pero describe su contexto. Cada tipo de archivo guarda metadatos distintos:

- **Fotos**: fecha de captura, modelo de cámara/celular, ubicación GPS, software de edición usado, comentarios.
- **Música**: título, artista, álbum, número de pista.
- **Documentos (Word, PDF)**: autor, fecha de creación/modificación, programa usado, a veces hasta el nombre de usuario del sistema donde se creó.

## Por qué importan tanto en CTF
Los retos frecuentemente esconden pistas específicas en los metadatos de un archivo — sobre todo en archivos multimedia (imágenes, audio). Es uno de los primeros lugares donde buscar después de confirmar el tipo real de archivo (ver [02-Firmas-y-Formatos-de-Archivo](02-Firmas-y-Formatos-de-Archivo.md)).

## Cómo verlos
La herramienta favorita para esto es **exiftool**:
```bash
exiftool archivo.jpg
```
Esto imprime toda la metadata legible que el archivo tiene: cámara, GPS, fechas, comentarios, software usado, y mucho más — de un solo golpe.

## Qué buscar específicamente
- Campos como `Comment`, `Author`, `Software`, `GPS Position` — a veces ahí está la flag directamente, o una pista de dónde buscarla.
- Coordenadas GPS: se pueden pegar directamente en Google Maps para ver la ubicación exacta donde se tomó una foto (muy común combinarlo con retos de OSINT).
- Fecha y hora de creación/modificación: útil para reconstruir una línea de tiempo (ver siguiente archivo).

## Tip
Si `exiftool` no muestra nada relevante, no den por hecho que no hay metadata — algunos formatos ocultan datos en campos no estándar. Prueben también abrir el archivo con un editor hexadecimal (`xxd`) y buscar cadenas de texto legibles con `strings archivo`.
