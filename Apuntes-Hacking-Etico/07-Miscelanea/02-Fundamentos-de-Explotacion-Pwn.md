# Fundamentos de Explotación / Pwn

## Vocabulario base (repaso rápido)
Ya lo vimos en [Web - Fundamentos de Explotación](01-Fundamentos-de-Explotacion.md), pero vale la pena repetirlo porque acá aplica igual:
- **Vulnerabilidad**: un defecto aprovechable.
- **Exploit**: la técnica concreta que dispara la vulnerabilidad.
- **Payload**: lo que hace el exploit una vez que consiguió su efecto.

## Qué es "Pwn" en el argot de CTF
"Pwn" (de "pwned", jerga de "owned"/dominado) se refiere específicamente a retos de **explotación de binarios** — programas que corren en un servidor remoto, con una vulnerabilidad de memoria (buffer overflow y similares) que hay que explotar para lograr ejecutar código propio en ese servidor.

Es un área muy profunda por sí sola (normalmente sería su propia categoría en muchos CTF), pero cuando aparece dentro de "Misc" suele ser en versión más ligera/introductoria.

## Conectarse a un servicio remoto: netcat
La gran mayoría de retos de Misc/Pwn dan una dirección y puerto para conectarse:
```bash
nc chall.ctf.com 1337
```
Al conectar, el servicio puede:
- Pedirles un input y validar una respuesta (acertijo, cálculo, contraseña).
- Darles directamente una shell interactiva si ya explotaron algo.
- Ejecutar un programa vulnerable esperando su input, que es lo que van a intentar explotar.

## El ciclo típico (recordatorio del archivo de Web)
1. **Identificación**: ¿qué servicio corre? ¿qué binario ejecuta?
2. **Análisis**: si les dan el binario para descargar, analícenlo con las herramientas de [Reversing](Apuntes-Hacking-Etico/05-Reversing/00-Indice.md) antes de conectarse en serio.
3. **Construcción del exploit**: a veces armado manualmente, a veces con `pwntools` (ver [04-Scripting-en-Python-para-CTF](04-Scripting-en-Python-para-CTF.md)).
4. **Ejecución**: lanzar el exploit contra el servicio remoto.
5. **Post-explotación**: si lograron una shell, buscar la flag (suele estar en un archivo `flag.txt` en el directorio del programa, o como variable de entorno).

## Conceptos básicos de vulnerabilidades de memoria (para reconocerlas)
- **Buffer Overflow**: cuando un programa escribe más datos de los que el espacio reservado (buffer) puede contener, sobrescribiendo memoria adyacente — potencialmente incluyendo la dirección de retorno de una función, lo que permite redirigir la ejecución del programa.
- **Format String**: cuando un programa usa una función como `printf` pasándole directamente el input del usuario como formato (`printf(input)` en vez de `printf("%s", input)`), permitiendo leer o escribir memoria arbitraria.

Estos dos son los puntos de entrada más comunes a "pwn" — no hace falta dominarlos a fondo para el nivel de Misc, pero reconocerlos quiere decir que están frente a un reto de este tipo.

## Nota importante
Si un reto de Misc resulta ser claramente de "pwn" con explotación de memoria seria, revisen también la carpeta de [Reversing](Apuntes-Hacking-Etico/05-Reversing/00-Indice.md) — las herramientas (Ghidra, gdb/pwndbg) son las mismas.
