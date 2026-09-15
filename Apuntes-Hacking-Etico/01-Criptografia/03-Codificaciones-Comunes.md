# Codificaciones Comunes

Recordatorio: **una codificación NO es un cifrado.** No hay clave secreta — cualquiera con la herramienta correcta la revierte al instante. Aparecen constantemente como "capas" antes de llegar al reto real (ej. algo en Base64 que al decodificar da un texto en César).

## Tabla de identificación

| Tipo | Características | Cómo identificarlo | Ejemplo |
|---|---|---|---|
| **Base64** | Usa A-Z, a-z, 0-9, `+`, `/`, y `=` de relleno al final | Termina frecuentemente en `=` o `==` | `RGlnaXRhbF9TZXJ2aWNlcw==` |
| **Hex (Base16)** | Solo caracteres 0-9 y A-F | Todo el texto son pares de estos caracteres | `44 69 67 69 74 61 6c` |
| **Base32** | Usa A-Z y 2-7, relleno con `=` | Menos símbolos que Base64, todo mayúsculas | `IJQXGZI=` |
| **URL Encoding** | Reemplaza caracteres especiales por `%XX` | Aparecen muchos `%` seguidos de 2 caracteres hex | `Hola%20Mundo` |
| **Base85 / Ascii85** | Más denso que Base64, usa más símbolos (`!`, `#`, etc.) | Empieza a veces con `<~` y termina con `~>` | `<~87cURD_*#4DfTZ)+T~>` |

## Cómo reconocer "capas" de codificación
Un truco típico en CTF es encadenar codificaciones: Base64 → Hex → Base64 otra vez. Para no perderse:
1. Miren el conjunto de caracteres presentes. Si son solo 0-9 y A-F → probablemente Hex. Si aparecen `+`, `/`, `=` → probablemente Base64.
2. Decodifiquen un nivel y vuelvan a mirar el resultado — ¿tiene pinta de otra codificación, o ya es texto legible/un cifrado clásico?
3. **CyberChef es ideal para esto**: se pueden encadenar varias "recetas" (Base64 → Hex → From Binary...) en una sola pasada y ver el resultado final sin decodificar manualmente paso a paso.

## Diferencia clave con los cifrados clásicos
Si después de decodificar Base64/Hex el texto sigue sin tener sentido (letras pero no forman palabras), probablemente hay un **cifrado** debajo (ver [02-Cifrados-Clasicos](02-Cifrados-Clasicos.md)) y no solo una codificación.
