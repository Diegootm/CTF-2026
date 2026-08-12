**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** Qué Rollo (250 pts) **Resuelto por:** Diego **Fecha:** 12/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado ya avisaba con humor: _"¿Una flag en un QR? ¡Qué Rollo!"_ — y al escanear el código QR visible en la imagen, el mensaje decodificado literalmente decía `th15_15_n0t_th3_fl49_1m_s0_s0rry` ("esto no es la flag, lo siento"). Eso confirmó que el QR visible era una trampa/broma a propósito, y que la flag real estaba escondida en otra parte del archivo.

## Herramienta(s) que usé

`exiftool`, `xxd`, `binwalk`, `file`.

## Pasos (solo lo esencial, tipo lista)

- Escaneé el QR visible del PNG — decodificó a un mensaje de broma, no la flag
- Corrí `exiftool Que-Rollo.png` — entre los metadatos apareció un campo `Thumbnail Image (Binary data 31295 bytes)`, con `Thumbnail Offset: 172` y `Thumbnail Length: 31295`
- Corrí `binwalk Que-Rollo.png` — confirmó que dentro del archivo había, además del PNG principal, una **imagen JPEG completa** embebida (detectada en el offset 252, justo dentro del rango del thumbnail EXIF)
- Extraje esa miniatura JPEG directamente con `exiftool -b`
- Al abrirla, contenía **otro código QR**, distinto al visible en la imagen principal
- Escaneé ese segundo QR y obtuve la flag real

## Comando(s) o payload clave (si aplica)

```bash
exiftool -b -ThumbnailImage Que-Rollo.png > thumbnail.jpg
file thumbnail.jpg
xdg-open thumbnail.jpg
```

## Flag

`cidsi{61e7276c6bb03aaefa004d6f154a10d4}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un archivo de imagen trae metadata EXIF, siempre vale la pena revisar si tiene un campo `ThumbnailImage` — es una imagen JPEG completa embebida dentro del propio archivo (pensada originalmente para previsualización rápida), y es un escondite clásico para esteganografía: la miniatura puede ser completamente distinta a la imagen principal. `exiftool -b -ThumbnailImage archivo > salida.jpg` la extrae directo, sin necesidad de calcular offsets a mano.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

No, una vez identificado el campo `ThumbnailImage` en el `exiftool`, la extracción fue directa con un solo comando.

---

