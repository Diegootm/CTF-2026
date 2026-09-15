# Análisis Dinámico y Debugging

## ¿Qué es?
Es analizar un binario **mientras se ejecuta**, en vez de solo leer su código. Se usa un **depurador (debugger)**, que permite pausar el programa en cualquier punto, ver el valor de las variables en ese momento, y avanzar instrucción por instrucción.

## Cuándo conviene usar esto en vez de análisis estático
- Cuando el pseudocódigo de Ghidra es difícil de seguir o el programa hace cosas que no se explican solo leyendo.
- Cuando quieren **confirmar** una hipótesis rápidamente (ej. "creo que compara mi input con esta variable en este punto exacto") sin tener que leer todo el flujo del programa.
- Cuando el programa pide un input y quieren ver exactamente qué hace con lo que escriben, en tiempo real.

## Herramienta base: gdb
`gdb` es el depurador estándar en Linux. Solo, es funcional pero poco amigable visualmente — por eso casi siempre se usa con una extensión.

### Extensiones recomendadas
- **pwndbg**: agrega muchísima información visual útil (registros, stack, memoria) automáticamente en cada paso.
- **GEF (GDB Enhanced Features)**: alternativa a pwndbg, con un enfoque similar.

Cualquiera de las dos mejora enormemente la experiencia comparado con gdb "pelado".

## Flujo básico de debugging

1. **Cargar el programa en gdb:**
   ```bash
   gdb ./programa
   ```
2. **Poner un punto de ruptura (breakpoint)** en una función clave (ej. donde se compara el input con la flag):
   ```
   break check_password
   ```
3. **Ejecutar el programa**:
   ```
   run
   ```
   El programa corre normalmente hasta llegar al breakpoint, donde se pausa.
4. **Inspeccionar registros y memoria** en ese punto — con pwndbg/GEF esto se muestra automáticamente en pantalla al pausar.
5. **Avanzar paso a paso**:
   - `next` (o `n`): ejecuta la siguiente línea, sin entrar en funciones que llame.
   - `step` (o `s`): ejecuta la siguiente línea, entrando en las funciones que llame.
6. **Ver el valor de una variable/registro**:
   ```
   print variable
   ```

## Qué buscar mientras depuran
- El momento exacto donde el programa compara el input del usuario contra un valor esperado.
- El valor esperado en sí, que puede estar en un registro o en memoria justo antes de la comparación.
- Saltos condicionales (`jz`, `jnz`, `je`, `jne` en ensamblador) — determinan si el programa sigue por el camino de "correcto" o "incorrecto", y ver qué se compara justo antes te dice qué se esperaba.

## Tip práctico
Si logran identificar el salto condicional que decide "contraseña correcta/incorrecta", a veces ni siquiera hace falta encontrar la flag real — se puede **forzar el salto** para que el programa siempre tome el camino de "correcto" (parchando el binario o modificando el flag de comparación en memoria durante la ejecución), aunque para conseguir la flag en sí generalmente van a necesitar el valor real de todos modos.
