**Área:** Cripto  **Dificultad:** medio  **Plataforma:** Cylab  **Link del reto o Nombre:** Black Cobra Pepper (chall.py + output.txt)  **Resuelto por:** Xavi  **Fecha:** 14/09  **Tiempo que tardé:** ~30 minutos

---

**¿Qué pista/detalle me hizo saber por dónde ir?**  
Me dieron un `chall.py` que decía implementar AES a mano y un `output.txt` con dos textos cifrados en hex. Al leer el código con calma me di cuenta de que las funciones `sub_bytes`, `sub_word` y `rcon` estaban definidas pero no hacían absolutamente nada, solo hacían `return` de lo mismo que recibían. Eso me hizo caer en que le habían quitado la S-box a AES, o sea toda la parte no lineal. Ahí until pensé "si no queda nada no lineal, todo esto tiene que ser lineal sobre GF(2)", y si es lineal, se puede romper con álgebra en vez de fuerza bruta.

**Herramienta(s) que usé**

- Python puro (sin librerías raras, reimplementé las funciones de `chall.py` a mano porque no tenía pwntools instalado)
- Eliminación gaussiana sobre GF(2) escrita por mí mismo
- Nada de fuerza bruta ni bibliotecas de crypto externas

**Pasos (solo lo esencial, tipo lista)**

- Revisé `chall.py` y confirmé que `sub_bytes`, `sub_word` y `rcon` son identidad, así que no hay confusión no lineal en ninguna parte del cifrado ni del key schedule
- Probé que `AES(pt=0, key=0) = 0`, lo que confirma que la función es puramente lineal (sin término independiente)
- Como es lineal, se cumple que `AES(pt, key) = AES(pt, 0) XOR AES(0, key)`
- Armé una matriz de 128x128 sobre GF(2) evaluando `AES(pt=0, key=e_i)` para cada uno de los 128 bits base de la clave (cada evaluación me daba una columna de la matriz)
- Usé el par conocido `(pt1, ct1)` que venía en `chall.py`/`output.txt` para plantear el sistema `matriz * key = ct1 XOR AES(pt1, 0)`
- Resolví el sistema con eliminación gaussiana en GF(2) y recuperé la clave exacta
- Verifiqué que la clave recuperada reproducía correctamente `ct1`
- Implementé el descifrado inverso (InvShiftRows + InvMixColumns, sin InvSubBytes porque no existe) y desencripté la segunda línea del output, que era la flag cifrada

**Comando(s) o payload clave (si aplica)**

python

```python
# clave recuperada por álgebra lineal sobre GF(2)
key = "a1a1a1a1b2b2b2b2c3c3c3c3d4d4d4d4"
AES_decrypt(ct_flag, key)  # -> "7069636f4354467b737069316379217d"
```

**Flag**  
`picoCTF{spi1cy!}`

**¿Qué aprendí / qué usaría de nuevo?**  
Aprendí a reconocer cuándo un "AES casero" en realidad es una transformación lineal disfrazada: basta con que quiten la S-box (o cualquier paso no lineal) para que todo el cifrado, incluido el key schedule, se vuelva resoluble con álgebra lineal sobre GF(2) en vez de con fuerza bruta o buscando vulnerabilidades típicas de AES real. La próxima vez que vea una implementación "custom" de un cifrado de bloque, lo primero que voy a hacer es revisar si cada paso es realmente no lineal, porque ahí suele estar la falla.

**¿Me trabé en algo? ¿Cómo lo destrabé?**  
Al principio pensé en atacar el cifrado por fuerza bruta o buscando algún patrón en el output, pero con 128 bits de clave eso no era viable. Me trabé bastante en entender que necesitaba tratar `AES(pt, key)` como una función lineal conjunta en vez de intentar invertir el algoritmo "a mano" ronda por ronda. Lo destrabé cuando confirmé que `AES(0,0)=0` y until caí en que podía separar la parte de la clave y la del texto plano usando esa propiedad, construir la matriz con vectores base y resolver el sistema con eliminación gaussiana.