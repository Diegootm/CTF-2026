# Criptografía Simétrica Moderna

"Simétrica" significa que se usa **la misma clave** para cifrar y descifrar. Es rápida y se usa muchísimo en la vida real (HTTPS, WhatsApp, discos cifrados), pero en CTF el reto casi nunca es "romper el algoritmo" sino encontrar un **error de implementación**.

## Tabla de identificación

| Tipo | Características | Cómo identificarlo | Ejemplo |
|---|---|---|---|
| **AES** | Cifrado por bloques, el estándar actual | Texto cifrado en bloques de 16 bytes; claves de 128/192/256 bits | Datos en Base64/Hex de longitud múltiplo de 16 |
| **DES / 3DES** | Cifrado por bloques más antiguo (obsoleto) | Bloques de 64 bits, clave de 56 bits (DES) o 112/168 (3DES) | `4A5E6F7A8B9C0D1E` |
| **XOR** | Operación lógica bit a bit, muy simple y muy común en retos fáciles | Datos que "casi" parecen aleatorios; si la clave es corta y se repite, se puede ver un patrón | — |

## XOR: el más importante de aprender bien
El **XOR** (`⊕`) es la base de muchísimos retos "fáciles" de CTF porque es simple de implementar mal:
- Si cifran un texto con una clave corta que se repite (ej. clave de 3 bytes sobre un texto de 300), el patrón se repite cada N bytes — eso permite un **ataque de clave repetida** (similar al análisis de frecuencia de Vigenère, pero en bytes).
- Si conocen (o pueden adivinar) parte del texto plano —por ejemplo, saben que el archivo cifrado empieza con `flag{`— pueden hacer XOR entre ese texto plano conocido y el cifrado para recuperar la clave directamente.
- Herramientas como **CyberChef** (receta "XOR Brute Force") prueban automáticamente claves de 1 byte.

## AES: qué buscar cuando aparece
AES bien implementado no se rompe por fuerza bruta (las claves son enormes). En CTF, los errores típicos que sí se explotan son:
- **Modo de operación inseguro**: el modo **ECB** cifra cada bloque de forma independiente, así que patrones repetidos en el texto original (ej. una imagen con zonas de un solo color) siguen siendo visibles en el texto cifrado — es un error clásico y fácil de detectar a simple vista.
- **IV (vector de inicialización) reutilizado o predecible** en modos como CBC o CTR.
- **Clave hardcodeada** en el código fuente del reto, si lo dan.

## DES / 3DES
Hoy se consideran inseguros por el tamaño pequeño de su clave (56 bits para DES es crackeable por fuerza bruta con hardware moderno). Si un reto usa DES, probablemente el punto es justamente demostrar que es débil y crackearlo por fuerza bruta.

## Regla general para esta sección
Cuando vean un cifrado simétrico moderno en un CTF, la pregunta no es "¿cómo rompo el algoritmo?" sino **"¿qué hicieron mal al usarlo?"** — clave reutilizada, modo inseguro, IV fijo, o la clave/contraseña está en algún lugar del propio reto (código fuente, otro archivo, metadatos).
