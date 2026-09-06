**Área:** Reversing **Dificultad:** Medio **Plataforma:** UMSS CTF (SCESI) **Link del reto o Nombre:** Muchas Flags (`fefa10c93be98fdc3db9886ef37bd674`) **Resuelto por:** Diego **Fecha:** 31/08 **Tiempo que tardé:** ~30 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

`strings` sobre el binario escupe ~100 cadenas `umssctf{...}` distintas (todas falsas) y además un montón de símbolos tipo `_ZStaNRSt13_Ios_FmtflagsS_`. Casi caemos en la trampa de pensar que era **rot13** por esos `Fmtflags`, pero esos símbolos son de la STL de C++ (`std::ios_base::fmtflags`), puro ruido.

La pista buena apareció al abrir `main` en Ghidra: en `.rodata` hay una cadena hexadecimal de **64 caracteres** (`eafcde31d8f0ccfb247b03d9dff9dd89ae63c13c276b1c1bb87d8baef304e1d1`) y `main` llama a una función `sha256(...)`. Ahí quedó claro: **no hay que romper nada**, hay que averiguar cuál de las ~100 cadenas ya incrustadas produce ese SHA-256.

## Herramienta(s) que usé

- **Ghidra** — para desensamblar/decompilar `main`, identificar la función `sha256` (símbolo `_Z6sha256RKNSt7__cxx1112basic_string...`) y ver que la comparación final era `sha256(entrada) == <constante en .rodata>`.
- **Python** (script propio) — para hashear todas las cadenas del binario y ver cuál coincide.
- El propio binario — para verificar la flag.

## Pasos (solo lo esencial, tipo lista)

- `file fefa10c93be98fdc3db9886ef37bd674` → ELF 64-bit, **not stripped**, C++.
- `strings ... | grep umssctf` → lista de ~100 flags falsas.
- Abrir en **Ghidra** y leer `main`:
  - construye un arreglo de 100 `std::string` con todas las flags falsas → decoración/ruido.
  - carga la constante `eafcde31...e1d1` (32 bytes en hex) desde `.rodata` (dir `0x4d38`).
  - imprime `Ingresa la bandera:` y lee con `getline`.
  - calcula `sha256(entrada)` y compara con `operator==` contra la constante.
  - si coincide: imprime `Correcto, la flag es: <entrada>`.
- Conclusión: la flag verdadera **ya está** dentro del binario, mezclada con las 99 falsas. Hay que hashearlas todas.
- Correr el script → `umssctf{much0s_str1ngs_un4_fl4g}`.
- Verificar: `echo 'umssctf{much0s_str1ngs_un4_fl4g}' | ./fefa10c93be98fdc3db9886ef37bd674` → `Correcto, la flag es: umssctf{much0s_str1ngs_un4_fl4g}`.

## Comando(s) o payload clave (si aplica)

```bash
# ver el hash objetivo dentro de .rodata (opcional, ya se ve en Ghidra)
objdump -s -j .rodata fefa10c93be98fdc3db9886ef37bd674 | grep -A2 eafcde
```

```python
#!/usr/bin/env python3
# probar_flags.py
# Prueba cada cadena umssctf{...} incrustada en el binario contra el SHA-256 fijo.

import re, hashlib

BINARIO  = "fefa10c93be98fdc3db9886ef37bd674"
OBJETIVO = "eafcde31d8f0ccfb247b03d9dff9dd89ae63c13c276b1c1bb87d8baef304e1d1"

datos = open(BINARIO, "rb").read()

# sacar todas las cadenas del tipo  umssctf{...}
candidatas = set(re.findall(rb"umssctf\{[^}]*\}", datos))

for c in candidatas:
    texto = c.decode()
    if hashlib.sha256(texto.encode()).hexdigest() == OBJETIVO:
        print("FLAG:", texto)
        break
else:
    print("ninguna coincide")
```

## Flag

`umssctf{much0s_str1ngs_un4_fl4g}`

## ¿Que aprendí / qué usaría de nuevo?

- Cuando un binario guarda un **SHA-256 fijo** y una lista grande de candidatos, la solución casi siempre es "hashear todos los candidatos", no atacar el hash.
- Los símbolos `_ZSt..._Ios_Fmtflags` son de `std::ios_base` (C++), no tienen nada que ver con rot13.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí: seguimos un rato la pista falsa del "rot13" sobre los símbolos `Fmtflags`. Se destrabó abriendo `main` en Ghidra y viendo que la única comparación real del programa era `sha256(entrada) == constante`, o sea que solo había que probar las cadenas ya presentes.
