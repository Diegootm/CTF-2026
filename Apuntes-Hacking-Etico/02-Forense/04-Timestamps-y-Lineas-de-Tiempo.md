# Timestamps y Líneas de Tiempo

## ¿Qué son los timestamps?
Son datos que indican **cuándo** ocurrió un evento sobre un archivo o entrada del sistema. Se conocen como marcas **MAC** por sus iniciales en inglés:

| Timestamp | Qué indica |
|---|---|
| **M**odified | Cuándo se modificó el contenido del archivo |
| **A**ccessed | Cuándo se leyó o accedió al archivo |
| **C**reated | Cuándo se creó el archivo o entrada |

## Otros timestamps más específicos (sistemas de archivos tipo NTFS)
En sistemas de archivos más complejos (como NTFS de Windows) existen timestamps adicionales, todos relacionados con la tabla maestra de archivos (MFT):
- Date Changed (MFT)
- Filename Date Created / Modified / Accessed (MFT)
- INDX Entry Date Created / Modified / Accessed / Changed

No hace falta memorizarlos todos — lo importante es entender que **un mismo archivo puede tener varias "fechas" distintas**, y cada una cuenta una historia diferente sobre qué se hizo con él.

## Por qué importa esto en CTF
Ciertos eventos —crear, mover, copiar, abrir, editar— afectan las marcas MAC de formas predecibles. Si logran extraer las marcas de tiempo de varios archivos relacionados, pueden **reconstruir una línea de tiempo de eventos**: qué se hizo primero, qué se modificó después, si algo fue copiado desde otro lugar, etc.

## Patrones típicos a reconocer
Algunos patrones comunes que ayudan a interpretar una línea de tiempo:
- Si **Created** es más reciente que **Modified**, probablemente el archivo fue **copiado** desde otro lugar (se "recreó" en el nuevo sistema, pero conservó su fecha de modificación original).
- Si **Modified** y **Accessed** son iguales pero **Created** es distinto, el archivo probablemente fue movido, no editado.
- Una ráfaga de archivos con timestamps casi idénticos suele indicar una operación masiva (por ejemplo, extraer un ZIP completo de una vez).

## Cómo obtener los timestamps
- `exiftool` también reporta fechas de creación/modificación en muchos formatos.
- `stat archivo` en Linux muestra los timestamps básicos (Access, Modify, Change).
- Para sistemas de archivos completos (imágenes de disco), herramientas como **Autopsy** generan una línea de tiempo visual automáticamente.
