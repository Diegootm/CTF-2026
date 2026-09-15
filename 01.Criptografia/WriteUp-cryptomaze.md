**Área:** Cripto  **Dificultad:** Medio  **Plataforma:**picoCTF 2025 (vía CyLabAcademy)  **Link del reto o Nombre:** LFSR (registro de desplazamiento)  **Resuelto por:** Xavi  **Fecha:** 14/09  **Tiempo que tardé:** ~10 min

---

**¿Qué pista/detalle me hizo saber por dónde ir?**  
El enunciado te daba tres cosas: un montón de ceros y unos (llamado "estado inicial"), unos números raros llamados "taps", y un texto cifrado bien largo en hexadecimal. La pista clave estaba en el propio enunciado: decía que esos ceros y unos se usaban para "armar una clave", y esa clave se usaba para cifrar la bandera. O sea, el reto en realidad tenía dos pasos pegados: primero reconstruir la clave, después usarla para abrir el candado.

**Herramienta(s) que usé**  
Un cuadernito de código (Python) para hacer los cálculos, porque esto no se puede resolver "a mano" ni con una web como dCode — hay que simular un mecanismo paso a paso.

**Pasos**

- Imagina una fila de 64 casillas, cada una con un 0 o un 1 adentro. Eso es el "estado inicial" que me dieron.
- Ese conjunto de números no es al azar: es como una maquinita que, cada vez que le das una vuelta de manivela, agarra unos pocos números de la fila (los "taps", que son solo las posiciones que hay que mirar), los combina con una regla bien simple (si son iguales da 0, si son distintos da 1), y ese resultado nuevo lo mete al final de la fila mientras bota el primer número. Es literalmente una fila que se va deslizando y regenerando sola.
- Cada vez que le das la vuelta, esa maquinita te "escupe" un numerito (0 o 1). Si le das vuelta 128 veces, terminas con una fila de 128 ceros y unos.
- Esos 128 ceros y unos, juntos, forman la "llave" para abrir el candado (esto es lo que en criptografía le dicen "clave AES", pero para efectos prácticos: es solo una contraseña muy larga hecha de puros 0 y 1).
- Con esa llave ya armada, se la das a un programa que sabe "destrabar" el texto cifrado (una función lista para eso, no hay que inventarla), y ese programa te devuelve el mensaje original.
- Al desarmar el candado, apareció la bandera, pegada con un poco de "relleno" (unos caracteres invisibles al final que no significan nada, solo se ignoran).

**Comando(s) o payload clave (si aplica)**

python

```python
key = 0
state = estado_inicial  # los 64 ceros y unos que te dieron

for i in range(128):
    key = (key << 1) + state[0]          # guardamos el primer numerito
    nuevo = state[63] ^ state[61] ^ state[60] ^ state[58]   # combinamos los "taps"
    state = state[1:] + [nuevo]          # la fila se desliza y crece de nuevo

# esa "key" ya armada es la llave que abre el candado (AES)
```

**Flag**  
`picoCTF{scr8mbledt_flvg_ab88432f}`

**¿Qué aprendí / qué usaría de nuevo?**  
Aprendí que no todos los candados se abren con una página web como dCode: este tenía una "receta" (el mecanismo de deslizar y combinar números) que había que reconstruir paso a paso con código, porque no era un cifrado clásico de letras sino de puros ceros y unos. La lógica en el fondo es simple: seguir la receta exactamente como la explicaron, sin saltarse pasos.

**¿Me trabé en algo? ¿Cómo lo destrabé?**  
Lo único confuso al principio es que el resultado final no se ve "limpio": trae basura pegada al final (el relleno). No hay que asustarse por eso, simplemente hay que quedarse con la parte que está entre `picoCTF{` y `}`, que es la bandera real.