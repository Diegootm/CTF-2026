# Esteganografía en Imágenes

Es, con diferencia, el formato más común en retos de CTF. Estas son las técnicas que hay que conocer sí o sí.

## LSB (Least Significant Bit) — la técnica más común
Cada píxel de una imagen se representa con números (por ejemplo, valores de Rojo, Verde y Azul entre 0 y 255). La técnica LSB oculta información modificando **el bit menos significativo** de cada byte de color.

**Por qué funciona sin que se note:** cambiar el último bit de un valor de color (por ejemplo, de 218 a 219) modifica el color de ese píxel de forma tan mínima que el ojo humano no lo puede percibir — pero si se juntan todos esos últimos bits de todos los píxeles, se puede reconstruir un mensaje o archivo completo escondido ahí.

**Cómo se extrae:** con herramientas especializadas (StegOnline, zsteg) que leen sistemáticamente los bits menos significativos de la imagen y los reconstruyen como texto o como otro archivo.

## Canales de color por separado
Una imagen a color tiene (típicamente) 3-4 canales: Rojo, Verde, Azul, y a veces Transparencia (Alpha). A veces la información oculta no está en el LSB, sino que se ve directamente si se **aísla un solo canal** de color y se lo mira solo — patrones o texto que están "camuflados" entre los otros colores se vuelven visibles al aislar el canal correcto.

Herramientas como **StegOnline** o **Stegsolve** permiten navegar canal por canal y por plano de bits, viendo la imagen resultante en cada combinación.

## Diferencias entre imágenes casi idénticas
Un truco típico: te dan dos imágenes que se ven prácticamente iguales a simple vista, pero tienen diferencias mínimas de píxeles que esconden un mensaje. Se puede comparar con:
```bash
diff imagen1 imagen2
```
O de forma visual con herramientas de comparación de imágenes, o restando una imagen de la otra con un script simple en Python (con la librería PIL/Pillow) para resaltar las diferencias.

## Detección automática de LSB: zsteg
```bash
zsteg imagen.png
```
Prueba automáticamente muchas combinaciones de canales y bits, y reporta qué encontró en cada una — mucho más rápido que revisar manualmente combinación por combinación.

## steghide: ocultar y extraer con contraseña
`steghide` es una herramienta que permite **ocultar** un archivo dentro de una imagen JPG/BMP (con o sin contraseña), y también **extraerlo**.

```bash
# Instalar
sudo apt install steghide

# Ocultar un archivo secreto dentro de un archivo contenedor
steghide embed -cf archivo.contenedor -ef archivo.secreto

# Extraer el archivo oculto de un contenedor
steghide extract -sf archivo.contenedor
```
Si el reto usa steghide y tiene contraseña, se puede intentar crackearla con `stegcracker` (fuerza bruta con diccionario contra archivos de steghide) en vez de adivinarla a mano.

## Flujo recomendado para un reto de stego con imágenes
1. `exiftool` — metadatos primero, siempre.
2. `binwalk` — ¿hay un archivo completo embebido al final del archivo?
3. `zsteg` (para PNG/BMP) — detección automática de LSB.
4. `StegOnline` o `Stegsolve` — inspección visual manual por canales/planos de bits.
5. `steghide extract` — si sospechan que puede tener contraseña, prueben sin contraseña primero, y si pide una, prueben `stegcracker` con un diccionario.
