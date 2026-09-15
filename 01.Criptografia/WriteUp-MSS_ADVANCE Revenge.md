Área: Cripto Dificultad: Difícil Plataforma: picoCTF Link del reto o Nombre:MSS_ADVANCE Resuelto por: Xavi  Fecha: 12/08 Tiempo que tardé: 30 min

## ¿Qué pista/detalle me hizo saber por dónde ir?

El `chall.py` define un polinomio de grado 29 (30 coeficientes) pero solo filtra 20 puntos `(x, y)`. Con Lagrange normal hacen falta 30 puntos para interpolar — así que el sistema está "incompleto" a propósito. La pista real está en cómo se generan los coeficientes: `coeffs[0] = SHA256(flag)` y cada siguiente es `SHA256(coeffs[i-1])`. Eso significa que cada coeficiente mide solo 256 bits, mientras que el módulo `p` es de 1024 bits. Esa diferencia de tamaño (coeficientes "pequeños" frente a un módulo enorme) es la firma clásica de un ataque de **reticulados (lattice attack / LLL)**.

## Herramienta(s) que usé

- Python 3
- `sympy` (álgebra lineal modular, inversas mod p)
- `fpylll` (reducción LLL con embedding de Kannan)
- `pycryptodome` (AES-CBC para descifrar el flag al final)

## Pasos (solo lo esencial, tipo lista)

1. Parsear `output.txt`: `p`, los 20 pares `(x, y)` y `enc_flag` (IV + ciphertext en hex).
2. Modelar el sistema como `A·c ≡ Y (mod p)`, donde `A` es la matriz 20×30 de potencias `x_j^(29-i) mod p` y `c` es el vector de 30 coeficientes desconocidos (cada uno < 2²⁵⁶).
3. Como hay 20 ecuaciones y 30 incógnitas, el sistema es subdeterminado: se elige un submatriz 20×20 invertible mod p (20 variables "pivote") y se despejan en función de las 10 variables "libres" restantes. Esto da:
    - Una solución particular `t` (fijando las variables libres en 0).
    - Una base del reticulado núcleo `L` (rango 30) que captura toda la periodicidad mod `p`.
4. Construir el reticulado extendido (embedding de Kannan) de dimensión 31: las 30 filas de `L` más una fila extra `(t, K)` con `K = 2^256` (misma escala que los coeficientes).
5. Correr LLL sobre esa matriz con `fpylll`. Como los coeficientes reales son mucho más pequeños que un vector "típico" del reticulado (2²⁵⁹ vs ~2⁶⁶⁹ esperado), LLL encuentra directamente el vector corto que corresponde a los 30 coeficientes reales.
6. Verificar el resultado dos veces: (a) que satisface las 20 ecuaciones polinomiales originales, y (b) que `coeffs[i+1] == SHA256(coeffs[i])` para toda la cadena — esto confirma que `c[0]` es efectivamente `MASTER_KEY = SHA256(flag)`.
7. Usar `MASTER_KEY` (32 bytes) como llave AES-256 para descifrar `enc_flag` en modo CBC con IV de ceros, y quitar el padding.

## Comando(s) o payload clave (si aplica)

```python
from fpylll import IntegerMatrix, LLL

# t = solución particular (30 dims), L_rows = base del reticulado núcleo (30x30)
# K = 1 << 256  (escala del embedding)
dim = n + 1  # 31
M = IntegerMatrix(dim, dim)
for r in range(n):
    for c_ in range(n):
        M[r, c_] = L_rows[r][c_]
for c_ in range(n):
    M[n, c_] = t[c_]
M[n, n] = K

LLL.reduction(M)

# La fila más corta (row 0) trae, en sus primeras 30 coordenadas,
# el vector real de coeficientes (si la última coordenada es +K;
# si es -K, se niega el vector completo).
row0 = [M[0, c_] for c_ in range(dim)]
coeffs = row0[:-1] if row0[-1] == K else [-x for x in row0[:-1]]

MASTER_KEY = long_to_bytes(coeffs[0], 32)

cipher = AES.new(MASTER_KEY, AES.MODE_CBC, bytes.fromhex(enc_flag[0]))
flag = unpad(cipher.decrypt(bytes.fromhex(enc_flag[1])), 16)
```

## Flag

`picoCTF{MSS_Advance_but_we_brought_it_back_and_made_it_harder!!!}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un sistema de ecuaciones (mod p) tiene menos ecuaciones que incógnitas pero las incógnitas están acotadas a un tamaño mucho menor que el módulo, casi siempre es una señal de "esto se resuelve con LLL/lattice reduction", no con álgebra lineal pura. `fpylll` con el embedding de Kannan es la herramienta estándar para este patrón (CVP disfrazado de sistema modular con solución pequeña).

## ¿Me trabé en algo? ¿Cómo lo destrabé?

`fpylll` fallaba al importar por faltarle la dependencia `cysignals` (`pip install fpylll` no la trae). Se resolvió instalándola aparte con `pip install cysignals`.