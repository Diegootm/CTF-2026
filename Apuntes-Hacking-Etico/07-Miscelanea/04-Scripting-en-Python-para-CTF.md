# Scripting en Python para CTF

## ¿Por qué Python y no otro lenguaje?
Porque tiene librerías listas para casi todo lo que se necesita en CTF (criptografía, manejo de bytes, conexión a sockets), su sintaxis es rápida de escribir bajo presión de tiempo, y es el lenguaje más usado en toda la comunidad de CTF — la mayoría de writeups y herramientas están en Python.

## Cuándo conviene escribir un script en vez de hacerlo a mano
- Cuando hay que **probar muchas combinaciones** (fuerza bruta, probar los 25 desplazamientos de César, probar claves XOR de 1 byte).
- Cuando el reto es **interactivo y rápido** (un servicio remoto que espera una respuesta en pocos segundos — imposible de resolver a mano a tiempo).
- Cuando hay que **repetir un cálculo** muchas veces con datos ligeramente distintos.
- Cuando el "ataque" en sí es matemático (ej. reconstruir la clave privada de RSA a partir de `p` y `q`).

## Librerías esenciales

| Librería | Para qué |
|---|---|
| `pwntools` | Conectarse a servicios remotos, automatizar exploits, manejar bytes fácilmente |
| `pycryptodome` | Cifrar/descifrar AES, DES, RSA desde Python |
| `requests` | Hacer peticiones HTTP (para retos de Web que conviene automatizar) |
| `pillow` (PIL) | Manipular imágenes (útil en Esteganografía) |

## Ejemplo: conectarse a un servicio remoto con pwntools
```python
from pwn import *

conn = remote('chall.ctf.com', 1337)
print(conn.recvline())          # leer una línea de lo que envía el servidor
conn.sendline(b'mi respuesta')  # enviar una respuesta
print(conn.recvall())           # leer todo lo que quede
```

## Ejemplo: automatizar un ataque XOR de clave repetida
```python
from itertools import cycle

cifrado = bytes.fromhex("...")  # el texto cifrado que les dieron
clave = b"clave"

resultado = bytes(a ^ b for a, b in zip(cifrado, cycle(clave)))
print(resultado)
```

## Ejemplo: automatizar una petición HTTP repetida (fuerza bruta simple)
```python
import requests

for intento in range(1000):
    r = requests.post("http://example.com/login", data={"pin": intento})
    if "bienvenido" in r.text.lower():
        print(f"PIN encontrado: {intento}")
        break
```

## Tip general
No hace falta que el script sea "elegante" — en CTF el objetivo es que **funcione rápido**, no que sea código de producción. Prioricen velocidad de escritura sobre prolijidad durante la competencia; pueden limpiar el código después si quieren guardarlo en su writeup.
