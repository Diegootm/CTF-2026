# Cifrados Clásicos

Estos son cifrados "de papel y lápiz", anteriores a las computadoras. Suelen ser el primer filtro fácil en un reto de cripto — reconocerlos a simple vista ahorra mucho tiempo.

## Tabla de identificación rápida

| Tipo de Cifrado | Características | Cómo identificarlo | Ejemplo |
|---|---|---|---|
| **César** | Desplazamiento simple de letras (todo el alfabeto se corre N posiciones) | Solo letras, se ve "casi legible" | `Gljlwdo_Vhuylfhv` |
| **Atbash** | Alfabeto invertido (A=Z, B=Y, C=X...) | Solo letras, patrón simétrico | `Wrtrgzo_Hvierxvh` |
| **Vigenère** | Cifrado polialfabético usando una palabra clave | Solo letras, pero NO cede ante un simple desplazamiento fijo | `Nmesxyv_Wcbzgmiq` |
| **Morse** | Puntos y rayas | Secuencias de `.` y `-`, separadas por espacios o `/` | `-.. .. --. .. - .- .-.. ..--.- ... . .-. ...`|
| **Binario** | Solo 0 y 1 | Agrupado típicamente en bloques de 8 bits | `01000100 01101001 01100111...` |

## Cómo romper cada uno

### César
Solo hay 25 desplazamientos posibles (26 letras del alfabeto, menos la opción de "no mover nada"). Se puede:
- **Probar los 25 a mano** con [dCode - César](https://www.dcode.fr/caesar-cipher) o CyberChef ("ROT13 Brute Force" o "ROT47").
- Si el texto es largo, un **análisis de frecuencia** (qué letra se repite más) ayuda a adivinar el desplazamiento, porque en español/inglés la letra más común suele ser "E" o "A".

### Atbash
No tiene clave — es siempre la misma sustitución (A↔Z, B↔Y...). Se decodifica directo con cualquier herramienta que tenga la opción "Atbash" (dCode, CyberChef).

### Vigenère
Usa una palabra como clave que se repite sobre el texto. Si no dan la clave:
- Busquen si el reto la menciona (a veces está escondida en el nombre del reto o en un archivo adjunto).
- Se puede atacar con **análisis de Kasiski** o **índice de coincidencia** para adivinar la longitud de la clave, pero para CTF suele bastar con probar herramientas automáticas como [dCode - Vigenère](https://www.dcode.fr/vigenere-cipher), que a veces hasta adivinan la clave solas si el texto es suficientemente largo.

### Morse
Se traduce directo con cualquier decodificador de Morse (dCode, CyberChef). Ojo: a veces el separador de letras/palabras no es el estándar (usan comas, saltos de línea, etc.) — si la herramienta falla, revisen qué símbolo separa los grupos.

### Binario
Se agrupa de 8 en 8 bits y cada grupo se convierte a su carácter ASCII correspondiente. CyberChef tiene la receta "From Binary" lista para esto.

## Tip general para todos los cifrados clásicos
Si no reconocen el cifrado a simple vista, usen el **"Magic Wand"** de CyberChef o el **identificador automático** de dCode — analizan el texto y sugieren qué cifrado es probable, ahorrando la parte de "adivinar".
