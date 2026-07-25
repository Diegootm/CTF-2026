Área: Forense Dificultad: Fácil Plataforma: CyLab Nombre: digits.bin Resuelto por: Diego Fecha: 24/07/2026 Tiempo que tardé: 15 minutos

## ¿Qué pista/detalle me hizo saber por dónde ir?

la pista decía que el archivo era "solo un montón de 1 y 0" pero que quizás no era ruido aleatorio; al ver con `file` que era texto ASCII de puros caracteres '0' y '1' (no bytes binarios crudos), supe que había que reconstruir algo agrupando esos bits

## Herramienta(s) que usé

xxd, file, python3 (para agrupar bits en bytes)

## Pasos (solo lo esencial, tipo lista)

- me descargué el archivo
- corrí `file digits.bin` y vi que era "ASCII text" en vez de binario real
- con `xxd digits.bin | head` confirmé que cada byte del hexdump era literalmente el caracter '0' (0x30) o '1' (0x31), no bits reales
- probé primero interpretarlo como imagen bitmap (71016 bits ≈ 264x269), pero el resultado fue un patrón de líneas diagonales sin forma reconocible — descarté esa hipótesis
- noté que 71016 es divisible exacto por 8 (71016/8 = 8877), lo que apuntaba a una simple conversión bits → bytes
- agrupé los primeros 16 bits del archivo de a 8 y los pasé a hex: `11111111 11011000` → `FF D8`, la firma SOI de JPEG
- hice lo mismo con los últimos bits del archivo: terminaban en `FF D9`, la firma EOI de JPEG
- con eso confirmado, escribí un script en Python que agrupa todo el string de bits de a 8 caracteres, los convierte a bytes con `int(bits[i:i+8], 2)`, y guarda el resultado como `.jpg`
- abrí la imagen resultante y ahí estaba la flag

Comando(s) o payload clave (si aplica)

```python
with open('digits.bin') as f:
    bits = f.read().strip()

data = bytes(int(bits[i:i+8], 2) for i in range(0, len(bits) - len(bits) % 8, 8))

with open('digits_recovered.jpg', 'wb') as f:
    f.write(data)
```

## Flag 

picoCTF{h1dd3n_1n_th3_b1n4ry_2607862b}

## ¿Qué aprendí / qué usaría de nuevo?

que cuando `file` marca un archivo como "ASCII text" compuesto solo de '0'/'1', conviene probar primero la hipótesis más simple (bits → bytes → puede ser un archivo real) antes de asumir que es una imagen bitmap; y que reconocer firmas de archivo (FF D8/FF D9 para JPEG) en los bytes decodificados es la forma más rápida de confirmar el formato real

## ¿Me trabé en algo? ¿Cómo lo destrabé?

sí, al principio asumí que era una imagen (por el nombre "digits" y la pista de "1 y 0") y perdí tiempo generando bitmaps con distintas dimensiones que salían como ruido/patrón diagonal sin sentido. Me destrabé al notar que el total de bits era divisible exacto por 8 y al reconocer las firmas FF D8 / FF D9 de JPEG en los extremos del archivo agrupado en bytes