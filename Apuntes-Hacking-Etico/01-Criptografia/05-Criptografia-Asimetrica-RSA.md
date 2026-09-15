# Criptografía Asimétrica — RSA

"Asimétrica" significa que hay **dos claves distintas**: una **pública** (que se puede compartir con cualquiera) y una **privada** (que debe mantenerse secreta). Lo que se cifra con una, solo se descifra con la otra. RSA es, por lejos, el algoritmo asimétrico más frecuente en CTF.

## La idea sin pánico matemático

RSA se basa en un hecho simple: **es fácil multiplicar dos números primos grandes, pero es extremadamente difícil hacer el proceso inverso** (factorizar el resultado para encontrar los primos originales). Toda la seguridad de RSA depende de que esos primos sean lo bastante grandes y bien elegidos.

## Las variables que van a ver todo el tiempo

| Variable | Qué es |
|---|---|
| `p`, `q` | Dos números primos grandes (secretos) |
| `n` | El producto `p × q` — es parte de la clave pública |
| `e` | El "exponente público" — parte de la clave pública (muy comúnmente `65537`) |
| `d` | El "exponente privado" — se calcula a partir de `p`, `q` y `e`, y es la clave privada |
| `c` | El texto cifrado ("ciphertext") |
| `m` | El mensaje original ("plaintext") |

La clave pública es el par `(n, e)`. La clave privada es `d`. Si un reto les da `n`, `e` y `c`, el objetivo casi siempre es recuperar `m` (el mensaje), y para eso generalmente hay que encontrar `d` — lo cual requiere conocer `p` y `q`.

## Cómo identificar un reto de RSA
- Aparecen las letras `n`, `e`, `c`, `p`, `q`, `d` en el enunciado o en un archivo.
- Números **muy** grandes (decenas o cientos de dígitos).
- Archivos `.pem` o `.pub` (claves en formato estándar).

## Ataques típicos de CTF a RSA (del más al menos común)

1. **`n` factorizable fácilmente**: si `p` y `q` no son lo bastante grandes, herramientas de factorización online (como [factordb.com](http://factordb.com/)) pueden encontrarlos directamente pegando `n`.
2. **`e` muy pequeño (ej. `e=3`) con mensaje corto**: si el mensaje es lo bastante pequeño respecto a `n`, se puede recuperar sin necesitar la clave privada, usando raíz cúbica.
3. **Mismo `n` usado por varias claves públicas distintas / mismo mensaje cifrado con distintas claves**: permite ataques como el **Teorema Chino del Resto**.
4. **Primos `p` y `q` muy cercanos entre sí**: permite el **ataque de Fermat**, que factoriza `n` rápidamente.
5. **Reutilización de un primo entre dos `n` distintos** (ej. dos claves públicas comparten `p`): se puede recuperar ambos con el **máximo común divisor (GCD)** de los dos `n`.

**No hace falta programar estos ataques desde cero** — la herramienta `RsaCtfTool` (ver [07-Herramientas](./07-Herramientas.md)) los prueba automáticamente contra un `n`, `e`, `c` dados.

## Flujo práctico recomendado para un reto de RSA
1. Identifiquen `n`, `e`, `c` (y `p`, `q` si los dan directamente).
2. Prueben `n` en [factordb.com](http://factordb.com/) — a veces ya está factorizado en su base de datos pública.
3. Si no, corran `RsaCtfTool` contra los datos — prueba automáticamente la mayoría de ataques conocidos.
4. Si tienen `p` y `q`, usen un script corto en Python con `pycryptodome` o `sympy` para reconstruir `d` y descifrar.
