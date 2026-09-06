**Área:** Reversing **Dificultad:** Medio **Plataforma:** UMSS CTF (SCESI) **Link del reto o Nombre:** Mensaje Raro (`90abe4903eb8467bb47993ec46370aa8`) **Resuelto por:** Diego**Fecha:** 31/08 **Tiempo que tardé:** ~20 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

`strings` muestra: `Introduce la flag:`, `incorrecto`, la cadena `umssctf` suelta, una función llamada `bytes_to_hex` y sobre todo una **cadena hexadecimal larga**: `000000000000000e0e1c1d3c150b1a1f2c1f022b151608001a1e`.

Que empiece con varios bytes `00` fue la clave: si el binario hace `entrada XOR clave` y el resultado esperado arranca con `00 00 00 ...`, entonces los primeros caracteres de la flag **son exactamente la clave**. Y la clave estaba a la vista: `umssctf`.

## Herramienta(s) que usé

- **Ghidra** — para decompilar `main`, ver la comprobación de longitud (`strlen == 0x1a` = 26), el bucle `out[i] = entrada[i] XOR clave[i % 7]`, la clave `umssctf` en `.rodata` (dir `0x2072`) y la comparación final con `bytes_to_hex(out)` contra la cadena en `0x2030`.
- **Python** (script propio) — para invertir el XOR.
- El binario — para verificar.

## Pasos (solo lo esencial, tipo lista)

- `file` → ELF 64-bit, not stripped.
- Abrir `main` en **Ghidra**:
  - lee la entrada con `fgets` y le quita el `\n`.
  - exige `strlen(entrada) == 26`.
  - clave de 7 bytes en `0x2072` → son los bytes `75 6d 73 73 63 74 66` = `"umssctf"`.
  - bucle `i = 0..25`: `out[i] = entrada[i] XOR clave[i % 7]`.
  - `bytes_to_hex(out)` y `strcmp` contra `000000000000000e0e1c1d3c150b1a1f2c1f022b151608001a1e` (26 bytes, en `0x2030`).
- Como XOR es reversible: `flag[i] = objetivo[i] XOR "umssctf"[i % 7]`.
- Los primeros 7 bytes del objetivo son `00` → confirma que la flag empieza por `umssctf`.
- Correr el script → `umssctf{con_amor_la_scesi}` (26 caracteres, cuadra con la longitud exigida).
- Verificar ejecutando el binario y pegando la flag → `correcto:umssctf{con_amor_la_scesi}`.

## Comando(s) o payload clave (si aplica)

```python
#!/usr/bin/env python3
# descifrar.py
# main hace  out[i] = entrada[i] XOR "umssctf"[i % 7]  y compara el hex resultante.
# Invertimos:  flag[i] = objetivo[i] XOR "umssctf"[i % 7]

OBJETIVO_HEX = "000000000000000e0e1c1d3c150b1a1f2c1f022b151608001a1e"
CLAVE = b"umssctf"

objetivo = bytes.fromhex(OBJETIVO_HEX)          # 26 bytes
flag = bytes(objetivo[i] ^ CLAVE[i % len(CLAVE)] for i in range(len(objetivo)))
print(flag.decode())
```

## Flag

`umssctf{con_amor_la_scesi}`

## ¿Que aprendí / qué usaría de nuevo?

- Un objetivo XOR que empieza con bytes `00` casi siempre te regala el prefijo de la clave (o de la flag), porque `x XOR x == 0`.
- Si el binario ya trae la clave en `.rodata`, no hay que fuerza bruta: solo desofuscar.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

No demasiado. La única duda fue si la clave "umssctf" era la clave real o parte de la flag; al ver en Ghidra que el bucle indexaba `clave[i % 7]` quedó claro que era una clave repetida de 7 bytes.
