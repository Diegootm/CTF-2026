# Fundamentos de Esteganografía

## ¿Qué es?
La esteganografía es la práctica de **ocultar datos a plena vista**. A diferencia de la criptografía (que hace que un dato sea ilegible pero obvio que "hay algo cifrado ahí"), la esteganografía busca que ni siquiera se note que hay información oculta.

Un ejemplo clásico: podrían enviarle a alguien la foto de un gato con un mensaje de texto escondido dentro. Al mirar la imagen, no hay absolutamente nada que sugiera que hay un mensaje escondido — se ve como una foto de gato normal.

## Diferencia clave con criptografía
| | Criptografía | Esteganografía |
|---|---|---|
| Objetivo | Que el contenido sea ilegible sin la clave | Que no se note que hay contenido oculto |
| ¿Es obvio que hay "algo"? | Sí (se ve texto cifrado, evidentemente raro) | No (se ve un archivo normal) |
| ¿Se pueden combinar? | Sí — es muy común esconder datos cifrados dentro de una imagen (doble capa) |

## Dónde suele estar escondida la información
- **Imágenes** (lo más común, con diferencia): en los bits menos significativos de cada píxel (técnica LSB, ver archivo siguiente), en capas de color separadas, o directamente un archivo completo embebido dentro del archivo de imagen.
- **Audio**: en frecuencias específicas visibles solo en un espectrograma, o datos embebidos en los metadatos/estructura del archivo.
- **Documentos**: texto en color blanco sobre fondo blanco, capas ocultas, metadatos.
- **Cualquier archivo, en realidad**: un archivo puede tener OTRO archivo completo "pegado" al final sin que el primero deje de funcionar normalmente (esto se solapa con binwalk de Forense).

## Mentalidad para resolver un reto de esteganografía
1. **Miren los metadatos primero** (`exiftool`) — a veces la pista o la flag está ahí directamente, sin necesitar ninguna técnica más compleja.
2. **Prueben herramientas automáticas** que revisan varias técnicas a la vez (Aperi'Solve, zsteg) antes de perder tiempo revisando manualmente canal por canal.
3. **Si el archivo es una imagen**, casi siempre el siguiente paso es LSB (ver [02-Esteganografia-en-Imagenes](02-Esteganografia-en-Imagenes.md)).
4. **No descarten que haya "capas"**: a veces hay que extraer un archivo oculto, y ESE archivo extraído tiene a su vez otra capa de esteganografía o un cifrado encima.
