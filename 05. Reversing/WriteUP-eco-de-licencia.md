**Área:** Reversing **Dificultad:** Media **Plataforma:** UMSS CTF (SCESI) **Link del reto o Nombre:** Eco de Licencia  `d37d9d86931f8a0550c51cb0647b0489`) **Resuelto por:** Diego **Fecha:** 31/08 **Tiempo que tardé:** ~20 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El nombre lo dice todo: "Eco de Licencia" → el programa **te devuelve tu propia entrada** como flag si aciertas (`printf("Flag: %s", entrada)`). O sea, la flag ES la licencia válida y hay que reconstruirla.

En `strings` se ven 3 flags falsas (`umssctf{flag_falsa_strings}`, `umssctf{no_es_por_aqui}`, `umssctf{casi_pero_no}`), unas cadenas ofuscadas raras (` 8&&6!3.H`, `9<60;H`, ...), la función `ruido` (código muerto) y textos como `Formato esperado: XXXX-XXXX-XXXX`... que resultó ser otra pista falsa. La comprobación real era mucho más simple.

## Herramienta(s) que usé

- **Ghidra** — para decompilar `main` y ver: cómo arma un buffer de 41 bytes en la pila con `movabs`, la clave XOR `0x55` (`mov BYTE PTR [rbp-0x9], 0x55`), la comprobación de longitud (`0x29` = 41) y el bucle `entrada[i] XOR 0x55 == buf[i]`.
- **Python** (script propio) — para reconstruir el buffer y aplicar el XOR inverso.
- El binario — para verificar.

## Pasos (solo lo esencial, tipo lista)

- `file` → ELF 64-bit, not stripped (fuente `eco_licencia.c`).
- Abrir `main` en **Ghidra**:
  - arma 41 bytes en `[rbp-0xa0]` con **6 instrucciones `movabs`**. Ojo: la 5ª escribe en `[rbp-0x87]`, que **solapa** parcialmente a la 4ª (`[rbp-0x88]`) → hay que aplicar las escrituras en orden.
  - guarda la longitud `0x29` (41) y la clave XOR `0x55`.
  - lee la licencia con `fgets`, quita el `\n` con `strcspn`.
  - si la licencia es igual a una de las 3 flags falsas → `Parece una flag, pero no es la correcta.`
  - si `strlen != 41` → `Licencia incorrecta.`
  - bucle: para todo `i`, exige `(entrada[i] XOR 0x55) == buf[i]`.
  - si todo cuadra → `Licencia valida.` y `printf("Flag: %s", entrada)`.
- Entonces: `licencia = buf XOR 0x55` (41 caracteres).
- Extraer los 6 valores `movabs`, respetar el solape, XOR `0x55`.
- Correr el script → `umssctf{la_licencia_no_estaba_en_strings}` (41 caracteres).
- Verificar: `printf 'umssctf{la_licencia_no_estaba_en_strings}\n' | ./d37d9d86931f8a0550c51cb0647b0489` → `Licencia valida. / Flag: umssctf{la_licencia_no_estaba_en_strings}`.

## Comando(s) o payload clave (si aplica)

```python
#!/usr/bin/env python3
# descifrar_licencia.py
# main arma 41 bytes en la pila con 6 movabs (uno pisa al anterior) y compara
#   entrada[i] XOR 0x55 == buf[i].
# Reconstruimos buf y aplicamos XOR 0x55.

import struct

buf = bytearray(41)

def escribir(offset, valor):
    for j, x in enumerate(struct.pack("<Q", valor)):
        if offset + j < len(buf):
            buf[offset + j] = x

escribir(0,  0x2e33213626263820)   # mov [rbp-0xa0], rax
escribir(8,  0x3b30363c390a3439)   # mov [rbp-0x98], rdx
escribir(16, 0x300a3a3b0a343c36)   # mov [rbp-0x90], rax
escribir(24, 0x3b300a3437342126)   # mov [rbp-0x88], rdx
escribir(25, 0x0a3b300a34373421)   # mov [rbp-0x87], rax  <-- solapa al anterior
escribir(33, 0x2826323b3c272126)   # mov [rbp-0x7f], rdx

licencia = bytes(c ^ 0x55 for c in buf)
print(licencia.decode())
```

## Flag

`umssctf{la_licencia_no_estaba_en_strings}`

## ¿Que aprendí / qué usaría de nuevo?

- Si el reto "hace eco" de tu entrada al acertar, la flag es literalmente la entrada válida → reconstruirla desde la comprobación.
- XOR de 1 byte con la clave a la vista (`0x55`) = solo desofuscar, cero fuerza bruta.
- La función `ruido` y el texto `Formato esperado: XXXX-XXXX-XXXX` eran señuelos; siempre confirmar en el decompilado qué comprobación se ejecuta de verdad.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

El único detalle fino fue el `movabs` en `[rbp-0x87]` que pisa parte del anterior. Al principio armé el buffer con offsets "limpios" y salía basura en el medio; recolocando esa escritura en su offset real (25, no 24 ni 32) la licencia salió entera.
