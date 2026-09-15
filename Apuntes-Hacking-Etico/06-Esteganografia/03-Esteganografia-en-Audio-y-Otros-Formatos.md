# Esteganografía en Audio y Otros Formatos

## Espectrogramas: la técnica más común en audio
Un espectrograma es una representación visual de las frecuencias de un sonido a lo largo del tiempo. La técnica más común de stego en audio consiste en **codificar una imagen o texto directamente en las frecuencias** del archivo — algo que es completamente inaudible al escuchar el audio normal, pero que se hace visible al mirar su espectrograma.

**Cómo verlo:**
- **Audacity** (gratuito): abrir el archivo de audio, y cambiar la vista de la pista a "Espectrograma" (clic en el nombre de la pista → Spectrogram). Si hay un mensaje escondido en las frecuencias, va a aparecer como una imagen o texto visible directamente en esa vista.
- **Sonic Visualiser**: alternativa a Audacity, más orientada al análisis técnico de audio.

## Otras técnicas en audio
- Datos ocultos en los **metadatos** del archivo de audio (título, artista, comentarios) — revisar con `exiftool`, igual que con imágenes.
- Un archivo completo (imagen, texto, zip) **embebido** dentro del archivo de audio, sin afectar la reproducción — se detecta con `binwalk`, igual que en cualquier otro formato.
- Morse escondido como pulsos de audio (pitidos cortos y largos) — hay que escuchar con atención o ver la forma de onda en Audacity.

## Códigos QR ocultos o corruptos
A veces la "esteganografía" no está en un formato de audio/imagen tradicional, sino en un **código QR** dañado o parcialmente oculto dentro de una imagen.
```bash
zbarimg imagen_con_qr.png
```
Si el QR está corrupto o incompleto, a veces hay que reconstruirlo manualmente en un editor de imágenes antes de que pueda ser leído.

## Archivos comprimidos con datos ocultos en el ZIP
Los archivos ZIP tienen su propia estructura, y a veces se manipula para esconder información:
- Comentarios de ZIP (el formato ZIP permite agregar un comentario que no se ve al descomprimir, pero se puede leer con herramientas específicas o incluso con `unzip -z archivo.zip`).
- Múltiples "finales de archivo" (end of central directory) — un truco donde un ZIP contiene, en realidad, dos archivos ZIP concatenados, y hay que extraer el segundo por separado.

## Flujo recomendado para audio/otros formatos
1. `exiftool` — metadatos primero, siempre (aplica a cualquier formato).
2. `binwalk` — ¿hay algo embebido dentro del archivo?
3. Si es audio: abrir en Audacity y revisar el espectrograma.
4. Si el reto menciona un QR o aparece uno en una imagen: `zbarimg`.
5. Si es un ZIP: revisar comentarios y la posibilidad de múltiples archivos concatenados.
