# Firmas y Formatos de Archivo

## El problema
La extensión de un archivo (`.jpg`, `.pdf`, `.zip`) es solo una etiqueta — cualquiera puede cambiarla o borrarla sin que el archivo deje de ser lo que realmente es. En CTF es MUY común encontrar archivos sin extensión o con una extensión falsa a propósito.

## La solución: firmas de archivo (magic numbers)
Los archivos tienen ciertos **bytes iniciales** (generalmente entre 2 y 4 bytes, al principio del archivo) que identifican su formato real, independientemente de cómo se llame el archivo. Se les llama **firmas de archivo** o **números mágicos**.

## Cómo ver la firma de un archivo

### Opción rápida: el comando `file`
```bash
file archivo_sospechoso
```
Analiza la firma automáticamente y te dice el tipo real de archivo. Es siempre el primer comando a correr sobre cualquier archivo desconocido.

### Opción manual: volcado hexadecimal con `xxd`
Si quieren ver la firma con sus propios ojos (o si `file` no reconoce el formato):
```bash
xxd archivo_sospechoso | head
```
Esto muestra los primeros bytes en hexadecimal. Por ejemplo, un archivo PDF siempre empieza con los bytes que representan `%PDF`.

## Firmas comunes que conviene recordar

| Tipo de archivo | Firma (hex) | Firma (ASCII visible) |
|---|---|---|
| PDF | `25 50 44 46` | `%PDF` |
| PNG | `89 50 4E 47` | `.PNG` |
| JPEG | `FF D8 FF` | — |
| ZIP | `50 4B 03 04` | `PK..` |
| GIF | `47 49 46 38` | `GIF8` |
| ELF (ejecutable Linux) | `7F 45 4C 46` | `.ELF` |

## Cuando la firma no coincide con la extensión
Si un archivo se llama `foto.jpg` pero su firma dice que en realidad es un ZIP, eso es una pista fuerte: renombrenlo con la extensión correcta (o simplemente ábranlo con la herramienta que corresponda a su firma real) y sigan investigando desde ahí — probablemente el reto esconde otro archivo dentro.

## Repositorio de referencia
Si encuentran una firma que no reconocen, pueden compararla con el repositorio de Gary Kessler, una base de datos extensa de firmas de archivo conocidas: https://filesig.search.org/
