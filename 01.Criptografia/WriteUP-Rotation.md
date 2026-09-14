Área: Cripto Dificultad: Difícil Plataforma: picoCTF Link del reto o Nombre:Rotation Resuelto por: Diego  Fecha: 13/09 Tiempo que tardé: 5 min

## ¿Qué pista/detalle me hizo saber por dónde ir?

Como el nombre decía Rotation supuse que se trataba de una encriptacion por rotacion
## Herramienta(s) que usé
- Decode


## Pasos (solo lo esencial, tipo lista)
1.  me dieron este cifrado xqkwKBN{z0bib1wv_l3kzgxb3l_4k71n5j0}
2. lo ingrese a para identifiacar y me salio cifrado ROT y con eso halle la flag

## Comando(s) o payload clave (si aplica)

## Flag

`picoCTF{r0tat1on_d3crypt3d_4c71f5b0}`

## ¿Qué aprendí / qué usaría de nuevo?

Es un cifrado de sustitución por desplazamiento, el más antiguo que existe — el **César**, porque Julio César lo usaba con desplazamiento de 3. La idea es correr cada letra un número fijo de posiciones en el alfabeto.

Con desplazamiento de 3: A→D, B→E, C→F... y al llegar al final se da la vuelta (X→A, Y→B, Z→C). Esa vuelta es aritmética modular: sumas el desplazamiento y tomas módulo 26.

La fórmula:

```
Cifrar:    C = (P + k) mod 26
Descifrar: P = (C - k) mod 26
```

donde P es la letra en claro (A=0, B=1... Z=25), k es la clave (el desplazamiento) y C la letra cifrada.

**ROT13** es simplemente César con k=13. Tiene una propiedad curiosa: como 13 es la mitad de 26, cifrar y descifrar son la misma operación. Aplicar ROT13 dos veces te devuelve el original. Por eso es tan popular.

**Tu reto usó k=18**, que no tiene esa simetría: cifraste con +18 y desciframos con −18.

**La debilidad** que lo hace trivial en CTF: solo hay **25 claves posibles** (desplazar 0 o 26 no cambia nada). Se rompe probándolas todas — se llama _ataque de fuerza bruta_ y con 25 opciones es instantáneo. Por eso las herramientas te muestran las 25 rotaciones de golpe y solo eliges la legible. Un cifrado que se rompe probando todas las claves en un segundo no sirve para seguridad real, solo para ofuscar a simple vista.

## ¿Me trabé en algo? ¿Cómo lo destrabé?
no 