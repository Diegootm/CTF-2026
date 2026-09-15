# Nociones de Ensamblador

## ¿Por qué necesitan saber esto?
Ghidra descompila a pseudocódigo (parecido a C), pero a veces ese pseudocódigo es confuso o incompleto, y hay que mirar el ensamblador real para entender qué está pasando exactamente. No hace falta ser expertos — con lo básico de esta página alcanza para no perderse en retos fáciles/medios.

## Los registros más importantes (arquitectura x86-64)
Un registro es como una "casilla" muy rápida donde el procesador guarda un valor temporalmente.

| Registro | Uso típico |
|---|---|
| `rax` / `eax` | Suele guardar el valor de retorno de una función |
| `rbx` / `ebx` | Uso general |
| `rcx` / `ecx` | Uso general, a veces contador de bucles |
| `rdx` / `edx` | Uso general |
| `rsp` | Apunta al tope de la pila (stack) |
| `rbp` | Apunta a la base del marco de la función actual |
| `rip` | Apunta a la instrucción que se está ejecutando ahora |

(El prefijo `r` es para 64 bits, `e` para 32 bits — mismo registro, distinto tamaño.)

## Instrucciones que van a ver todo el tiempo

| Instrucción | Qué hace |
|---|---|
| `mov a, b` | Copia el valor de `b` en `a` |
| `add a, b` | Suma `b` a `a` |
| `sub a, b` | Resta `b` de `a` |
| `cmp a, b` | Compara `a` con `b` (no guarda el resultado, pero afecta "flags" que usan los saltos siguientes) |
| `jmp direccion` | Salta incondicionalmente a otra parte del código |
| `je` / `jz` | Salta si la comparación anterior dio "igual" / "cero" |
| `jne` / `jnz` | Salta si la comparación anterior dio "distinto" / "no cero" |
| `call funcion` | Llama a una función |
| `ret` | Vuelve de una función a quien la llamó |
| `push` / `pop` | Guarda/recupera un valor en la pila (stack) |

## El patrón más importante para CTF: comparación + salto condicional
Casi todo crackme se reduce a este patrón en ensamblador:
```asm
cmp eax, ebx      ; compara dos valores
jne fallo         ; si son distintos, salta a "fallo"
; si llega aquí, son iguales -> "éxito"
```
Cuando estén leyendo ensamblador buscando la lógica de validación, **busquen exactamente este patrón**: una instrucción `cmp` seguida de un salto condicional. Ahí es donde se decide si su input fue correcto, y qué es lo que se estaba comparando contra qué.

## Tip práctico
No necesitan leer TODO el ensamblador de un programa línea por línea. Ubiquen la función relevante (con Ghidra suele ser más fácil encontrarla primero en el pseudocódigo, y de ahí saltar a ver su ensamblador si hace falta), y busquen específicamente los `cmp` y saltos condicionales cerca de donde se procesa el input del usuario.
