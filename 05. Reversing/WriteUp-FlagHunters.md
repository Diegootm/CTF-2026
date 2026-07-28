

**Área:** Reversing **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab/Laboratorio Cy) **Link del reto o Nombre:** Cazadores de banderas **Resuelto por:** Diego **Fecha:** 28/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía: _"Hay un estribillo oculto que este programa no imprime de forma predeterminada. ¿Puedes conseguirlo para imprimirlo?"_ — eso confirmaba que la flag estaba **en el código fuente, en texto plano**, dentro de una sección que el programa normalmente nunca ejecuta ni imprime. No era necesario romper cifrado ni nada externo: había que analizar la lógica del intérprete y forzarlo a llegar a esa sección oculta.

## Herramienta(s) que usé

- Lectura manual del código fuente (Python) descargado del reto
- Una **simulación local en Python** del mismo programa (recreé el `reader()` con una flag de prueba) para poder probar distintas entradas de forma segura y rápida, sin gastar intentos contra el servidor real
- `nc` (netcat) para conectarme al servidor final una vez confirmado el exploit

## El error lógico (la vulnerabilidad)

El programa es un intérprete casero de una "canción" con su propio mini-lenguaje de control de flujo (`REFRAIN`, `RETURN`, `CROWD`, `END`). La flag real está escondida en una introducción (`secret_intro`) que se ubica **antes** de la sección `[VERSE1]`, que es donde el programa empieza a ejecutar por defecto — por diseño, esa introducción nunca se imprime.

El fallo está en cómo maneja la interacción `CROWD (Singalong here!)`:

```python
elif re.match(r"CROWD.*", line):
    crowd = input('Crowd: ')
    song_lines[lip] = 'Crowd: ' + crowd   # <-- aquí está el bug
    lip += 1
```

Cuando el programa pide `Crowd:` y el usuario escribe algo, **esa línea de la canción se sobrescribe permanentemente** con `"Crowd: " + <lo que escribiste>`. El estribillo (`[REFRAIN]`) se repite varias veces a lo largo de la canción (una vez por cada verso), así que el programa **vuelve a pasar por esa misma línea más adelante**.

La segunda vez que llega ahí, como la línea ya no empieza con `CROWD`, no vuelve a pedir input — en cambio, cae en esta parte del bucle principal:

```python
for line in song_lines[lip].split(';'):
    ...
    elif re.match(r"RETURN [0-9]+", line):
        lip = int(line.split()[1])
```

Es decir: el programa **vuelve a interpretar tu propio texto anterior como si fuera código** del mini-lenguaje, partiéndolo por `;`. Si tu input contenía un `;` seguido de `RETURN <número>`, el programa salta directo a la línea que tú elijas — **sin ninguna validación de que ese salto tenga sentido**. Esto es básicamente una inyección de código/control flow: el dato que el usuario controla (`crowd`) termina siendo re-ejecutado como instrucción.

## Cómo encontré la vulnerabilidad (proceso)

1. Recreé el programa completo en un archivo local, reemplazando la flag real por una de prueba (`picoCTF{TEST_FLAG_PLACEHOLDER}`), para poder experimentar libremente
2. Añadí un mini "debug" que imprimía cada línea de la canción con su número de índice, para ubicar exactamente dónde estaba la flag (línea 0-3, antes de `[REFRAIN]`) y dónde estaban los puntos de entrada/salto (`[VERSE1]`, `[REFRAIN]`, `RETURN`)
3. Leí con cuidado la función `reader()` línea por línea, prestando atención a **qué controla el usuario** (el input de `Crowd:`) y qué hace el programa con ese dato después — ahí noté que el input se guarda directamente en `song_lines[lip]`, es decir, se convierte en parte del "código" de la canción
4. Simulé automáticamente una entrada de `Crowd:` igual a `;RETURN 0` (usando un input "enlatado" en vez de escribir a mano cada vez) y confirmé que, en la segunda vuelta al estribillo, el programa efectivamente saltaba a la línea 0 e imprimía la introducción secreta con la flag de prueba
5. Con el exploit confirmado localmente, lo repetí contra el servidor real por netcat

## Comando(s) o payload clave (si aplica)

```
$ nc verbal-sleep.picoctf.net 61125

# Esperar hasta que aparezca el primer prompt:
Crowd: ;RETURN 0

# Presionar Enter y esperar a que el programa vuelva a pasar
# por el estribillo (segunda vez) — ahí imprime la intro secreta con la flag
```

## Flag

`picoCTF{70637h3r_f0r3v3r_befbccb7}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un programa toma un input del usuario y lo **guarda para reutilizarlo/reinterpretarlo más adelante como parte de su propia lógica** (en vez de tratarlo siempre como dato inerte), ahí hay una posible inyección de control de flujo — el mismo principio detrás de vulnerabilidades más "serias" tipo inyección SQL o eval() inseguro, aplicado aquí a un mini-lenguaje casero. Simular el programa localmente con una flag falsa antes de probar contra el servidor real ahorra tiempo y no gasta intentos.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Al principio no era obvio cómo llegar a la introducción secreta, ya que el programa arranca directo en `[VERSE1]` y nunca vuelve atrás por diseño. Lo resolví identificando que el `input()` de `Crowd:` no se trata como dato inerte, sino que se reinserta en la lista de líneas de la canción y se vuelve a interpretar la próxima vez que el flujo pasa por ese punto — eso abrió la puerta a inyectar `;RETURN 0` para forzar el salto a la línea 0.

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._