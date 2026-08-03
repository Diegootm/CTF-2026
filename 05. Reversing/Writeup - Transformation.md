
**Área:** Reversing/**Dificultad:** Fácil/**Plataforma:** CyLab/**Link del reto o Nombre:**Transformation/**Resuelto por:** Xavi/**Fecha:** 03/08/**Tiempo que tardé:** 10 min

**¿Qué pista/detalle me hizo saber por dónde ir?**  
El archivo `.txt` solo tenía caracteres chinos/CJK random en vez de texto normal. Ver "chino" donde debería haber inglés es la señal clásica de un problema de encoding UTF-16 (agrupa 2 bytes ASCII en 1 solo carácter Unicode, y esos números caen justo en el rango de los caracteres CJK).

**Herramienta(s) que usé**  
Python (también se puede con CyberChef, operación "Decode text" → UTF-16BE)

**Pasos (solo lo esencial, tipo lista)**

1. Abrí el `.txt` y vi una cadena de caracteres chinos sin sentido.
2. Sospeché que el texto original era ASCII pero fue empaquetado como UTF-16 (2 bytes por carácter en vez de 1).
3. Para cada carácter, extraje el número Unicode (`ord()`).
4. Separé ese número en byte alto (`>> 8`) y byte bajo (`& 0xFF`) — cada mitad es una letra ASCII original.
5. Uní todas las letras recuperadas en orden y apareció la flag en texto plano.

**Comando(s) o payload clave**

python

```python
mensaje = "灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸形㝦㘲捡㕽"

flag = ''
for c in mensaje:
    n = ord(c)          # número Unicode combinado (ej: 0x7069)
    alto = n >> 8        # recupera la letra "de arriba" -> 'p'
    bajo = n & 0xFF       # recupera la letra "de abajo" -> 'i'
    flag += chr(alto) + chr(bajo)

print(flag)
```

Alternativa (una línea, usando encoding directo):

python

```python
open('flag.txt', encoding='utf-16-be').read()
```

**Flag**  
`picoCTF{16_bits_inst34d_of_8_b7f62ca5}`

**¿Qué aprendí / qué usaría de nuevo?**  
Cuando un texto "debería" ser ASCII/inglés simple pero aparece lleno de caracteres chinos/asiáticos random, es casi siempre un problema de decodificación UTF-16 (2 bytes leídos como 1 carácter); probar CyberChef → "Decode text" → UTF-16BE/LE antes de asumir que es cifrado real.

**¿Me trabé en algo? ¿Cómo lo destrabé?**  
Al principio no era obvio que fuera un tema de encoding y no de cifrado; se destrabó al notar que el nombre de la propia flag ("16_bits_inst34d_of_8") era la pista explícita de qué estaba pasando.