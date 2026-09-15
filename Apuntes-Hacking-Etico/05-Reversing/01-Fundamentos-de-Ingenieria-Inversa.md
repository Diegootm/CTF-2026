# Fundamentos de Ingeniería Inversa

## ¿Qué es?
Ingeniería inversa es como desarmar un reloj para entender cómo funciona por dentro, pero con programas de computadora. En CTF, significa **analizar un programa ya compilado** para entender qué hace, sin tener acceso a su código fuente original.

## Para qué sirve en la práctica (y en CTF)
- Analizar cómo funciona un programa cuyo código no tenemos.
- Encontrar contraseñas o claves ocultas dentro del binario.
- Entender la lógica de un algoritmo implementado en el programa.
- Saltarse (bypassear) protecciones o validaciones puestas a propósito.

## Cómo identificar un challenge de Reversing
- Te dan un **archivo ejecutable** para descargar, en vez de código fuente o una URL.
- Extensiones comunes: `.exe` (Windows), `.elf` o sin extensión (Linux), `.bin` (genérico).
- El enunciado usa frases como "Find the flag", "Reverse this program", "Crackme" (nombre típico para este tipo de reto).

## El flujo mental para resolver un reto de reversing
1. **Identifiquen el tipo de archivo** con `file` — ¿es un ELF de 32 o 64 bits? ¿Un .exe de Windows?
2. **Análisis estático primero**: miren el binario sin ejecutarlo (`strings`, y luego un descompilador como Ghidra). Muchas veces la flag o una pista fuerte está directamente en el texto legible del binario.
3. **Si el análisis estático no alcanza, pasen a análisis dinámico**: ejecuten el programa paso a paso con un depurador (gdb) para ver qué hace en tiempo real, qué compara, qué condiciones tiene que cumplirse.
4. **Busquen el punto de comparación de la flag**: casi todo crackme en algún momento compara lo que ingresaste contra un valor esperado — encontrar ese punto exacto en el código suele ser la clave del reto.

## Los dos archivos siguientes cubren justo esos dos enfoques
- [02-Analisis-Estatico](02-Analisis-Estatico.md): sin ejecutar el programa.
- [03-Analisis-Dinamico-y-Debugging](03-Analisis-Dinamico-y-Debugging.md): ejecutándolo paso a paso.
