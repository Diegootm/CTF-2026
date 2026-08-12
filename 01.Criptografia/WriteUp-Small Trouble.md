Área: Cripto Dificultad: Medio Plataforma: picoCTF  **Link del reto o Nombre:** Small TroubleResuelto por: Xavi Fecha: 11/08 Tiempo que tardé: 15 min

¿Qué pista/detalle me hizo saber por dónde ir? En encryption.py, `d = getPrime(256)` se genera primero (muy pequeño frente a un módulo de ~2096 bits) y luego `e = inverse(d, phi)`. Un `e` gigante y un `d` deliberadamente chico es la firma clásica de un exponente privado vulnerable a Wiener.

Herramienta(s) que usé Python 3, pycryptodome (Crypto.Util.number)

Pasos (solo lo esencial, tipo lista)

- Leer n, e, c desde message.txt
- Calcular la fracción continua de e/n
- Generar los convergentes k/d de esa fracción continua
- Para cada convergente, probar si (e*d - 1) es divisible por k → candidato a φ(n)
- Con φ(n) candidato, resolver la ecuación cuadrática x² - (n-φ+1)x + n = 0 para hallar p y q
- Verificar p*q == n → si coincide, d es el correcto
- Descifrar: m = c^d mod n
- Convertir m a bytes → flag

Comando(s) o payload clave (si aplica)

```python
from Crypto.Util.number import long_to_bytes
import math

def continued_fraction(numer, denom):
    cf = []
    while denom:
        cf.append(numer // denom)
        numer, denom = denom, numer % denom
    return cf

def convergents(cf):
    convs = []
    for i in range(len(cf)):
        num, den = cf[i], 1
        for j in range(i - 1, -1, -1):
            num, den = cf[j] * num + den, num
        convs.append((num, den))
    return convs

def wiener_attack(e, n):
    cf = continued_fraction(e, n)
    for k, d in convergents(cf):
        if k == 0 or d == 0:
            continue
        if (e * d - 1) % k != 0:
            continue
        phi = (e * d - 1) // k
        b = n - phi + 1
        disc = b * b - 4 * n
        if disc < 0:
            continue
        sqrt_disc = math.isqrt(disc)
        if sqrt_disc * sqrt_disc != disc:
            continue
        if (b - sqrt_disc) % 2 != 0:
            continue
        p = (b - sqrt_disc) // 2
        q = (b + sqrt_disc) // 2
        if p * q == n:
            return d, p, q
    return None

# n, e, c cargados desde message.txt
d, p, q = wiener_attack(e, n)
m = pow(c, d, n)
print(long_to_bytes(m))
```

Flag `picoCTF{sm4ll_d_63c34244}`

¿Qué aprendí / qué usaría de nuevo? Cuando `e` es enorme (miles de bits) y no es el clásico 65537, sospechar de un `d` pequeño y probar directamente el ataque de Wiener vía fracciones continuas antes de intentar cualquier otra cosa.

¿Me trabé en algo? ¿Cómo lo destrabé? Sí: `int(n**0.5)` fallaba con `OverflowError` por números de miles de bits (float no tiene esa precisión). Lo resolví usando `math.isqrt()`, que calcula raíz cuadrada entera exacta sin pasar por float.