# Análisis Estático

## ¿Qué es?
Es analizar un binario **sin ejecutarlo** — mirando su contenido, estructura y código directamente. Es siempre el primer paso, porque es más seguro (no corren código potencialmente malicioso) y más rápido para retos simples.

## Paso 1: identificar el archivo
```bash
file programa
```
Les dice si es un ejecutable ELF (Linux) o PE (Windows), de 32 o 64 bits, si está "stripped" (sin símbolos de depuración, más difícil de leer) o no.

## Paso 2: buscar texto legible
```bash
strings programa
```
Extrae todas las cadenas de texto imprimibles dentro del binario. En retos fáciles, **la flag puede estar directamente ahí**, o al menos mensajes que dan pistas sobre la lógica del programa (ej. "Contraseña incorrecta", "¡Correcto!").

Consejo: combínenlo con `grep` para filtrar rápido:
```bash
strings programa | grep -i "flag"
```

## Paso 3: descompilar con Ghidra
Cuando `strings` no alcanza, hay que ver la lógica real del programa. **Ghidra** es un descompilador gratuito (creado por la NSA) que convierte el código máquina de vuelta a algo parecido a código en C, mucho más legible que ensamblador puro.

Flujo básico en Ghidra:
1. Crear un nuevo proyecto y cargar el binario.
2. Dejar que Ghidra haga el análisis automático inicial.
3. Ir a la función `main` (o buscar funciones con nombres sugerentes como `check_password`, `verify`).
4. Leer el pseudocódigo generado en el panel "Decompile" — casi siempre se entiende la lógica de validación mucho más fácil ahí que en el ensamblador crudo.

## Qué buscar en el pseudocódigo
- Comparaciones de strings (`strcmp`, `==`) contra un valor fijo — ese valor fijo suele ser (o llevar a) la flag.
- Funciones con nombres reveladores (`check_flag`, `validate`, `xor_decrypt`).
- Bucles que transforman datos byte a byte (común en retos que "cifran" la flag dentro del propio binario con XOR u otra operación simple).

## Limitación del análisis estático
Algunos programas usan trucos para dificultar el análisis estático (código empaquetado/ofuscado, auto-modificación). Cuando el pseudocódigo no tiene sentido o el programa hace cosas raras que no se explican solo leyendo, es momento de pasar a análisis dinámico (siguiente archivo).
