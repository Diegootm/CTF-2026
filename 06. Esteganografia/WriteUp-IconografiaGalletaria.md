**Área:** Esteganografía **Dificultad:** Fácil **Plataforma:** CITC 2024 **Link del reto o Nombre:** Iconografía galletaria (100 pts) **Resuelto por:** Diego **Fecha:** 29/07 - 01/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía: _"El conjunto de imágenes de representación de galletitas tienen carácter de visualización sin fondo representando estados y objetos."_ Esto describía, sin decirlo directo, un **sprite sheet de íconos de jQuery UI** (el mismo set de iconitos transparentes, repetido en distintos colores para representar distintos "estados" de la interfaz — normal, hover, error, etc.). El archivo entregado resultó ser un firmware completo de un router Ubiquiti, así que había que extraer su sistema de archivos para llegar a esas imágenes.

## Herramienta(s) que usé

- `file`, `xxd`, `exiftool`, `binwalk` (identificación del archivo)
- `dd` + `unsquashfs` (extracción del sistema de archivos SquashFS embebido en el firmware)
- `find` (para localizar los íconos dentro del filesystem extraído)
- `ls -la` y `md5sum` (para comparar los 5 archivos de íconos entre sí)
- Visor de imágenes (`feh`) para inspeccionar visualmente el sprite sospechoso

## Pasos (solo lo esencial, tipo lista)

- Corrí `file`, `exiftool` y `binwalk` sobre el archivo entregado (`Iconografia_galletaria`) — resultó ser un **firmware de router Ubiquiti** (arquitectura ar934x), con una partición `SquashFS` (el sistema de archivos del router) claramente identificada por `binwalk`
- Extraje el SquashFS con `dd` (usando el offset y tamaño exactos que dio `binwalk`) y lo descomprimí con `unsquashfs`
- Busqué imágenes dentro del filesystem extraído con `find ... -iname "*.png"` — apareció un grupo de 5 archivos `ui-icons_<color>_256x240.png`, el set de íconos de jQuery UI en 5 colores distintos
- Comparé los 5 archivos por tamaño (`ls -la`) y hash (`md5sum`): 4 pesaban exactamente 4.4 kB (idénticos en estructura, solo cambia el color), pero **uno pesaba 21 kB** — casi 5 veces más grande
- Revisé ese archivo distinto (`ui-icons_cd0a0a_256x240.png`) con `exiftool`: tenía **tipo de color RGBA** (los otros 4 eran paleta indexada) y **fecha de modificación de 2023**, mientras los otros 4 eran del firmware original de 2013 — confirmando que fue agregado/modificado después, específicamente para el reto
- Revisé metadata (`exiftool -a -u -g1`, `XMP:all`) y ejecuté `binwalk` sobre ese archivo — sin nada oculto relevante (solo el bloque EXIF estándar)
- Abrí la imagen directamente con un visor (`feh`) y ahí, entre los iconitos normales, apareció un recuadro de texto blanco con la flag escrita directamente sobre la imagen

## Comando(s) o payload clave (si aplica)

```bash
# extraer el sistema de archivos del firmware
dd if=Iconografia_galletaria of=rootfs.squashfs bs=1 skip=1197848 count=5470072
unsquashfs rootfs.squashfs

# encontrar el icono distinto entre los 5
ls -la squashfs-root/usr/www/images/ui-icons_*.png
md5sum squashfs-root/usr/www/images/ui-icons_*.png

# abrir el sospechoso para verlo directo
feh squashfs-root/usr/www/images/ui-icons_cd0a0a_256x240.png
```

## Flag

`cidsi{b3bc634eaecec30651ca8b55eff512fe}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un reto entrega un conjunto de archivos "iguales" (mismo tipo, mismo propósito, como un set de íconos recoloreados), comparar tamaño y hash entre todos ellos es una forma rápida de encontrar cuál fue modificado — no hace falta correr herramientas de esteganografía pesadas si el intruso se puede detectar a simple vista por su tamaño de archivo. Y no siempre hay que buscar datos ocultos en bits o metadata: a veces la flag está escrita **directamente y a la vista** dentro de la imagen, solo hay que abrirla.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí — antes de comparar los 5 archivos entre sí, intenté varias herramientas de esteganografía (`zsteg`, `stegano`, revisar metadata XMP/EXIF a fondo) sobre el archivo sospechoso sin encontrar nada. Lo resolví simplemente abriendo la imagen con un visor normal — la flag estaba escrita en texto plano sobre la imagen, visible a simple vista, sin necesidad de extraerla con herramientas.

---

