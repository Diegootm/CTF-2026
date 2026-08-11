**Área:** Forense **Dificultad:** Medio **Plataforma:** picoCTF 2026 **Link del reto o Nombre:** Timeline 1 **Resuelto por:** Diego **Fecha:** 11/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El reto daba 4 pistas progresivas: (1) crear una timeline MAC con Sleuthkit, (2) mirar timestamps recientes, (3) prestar atención a timestamps cerca de una acción "anti-forense", y (4) filtrar archivos nuevos buscando el flag `macb` (cuando las 4 marcas de tiempo — Modified, Accessed, Changed, Born — coinciden exactamente, típico de un archivo recién creado y nunca vuelto a tocar). Eso definía el camino completo: reconstruir la línea de tiempo del sistema de archivos y buscar el archivo "impostor" creado justo antes de que el atacante apagara el sistema para cubrir sus huellas.

## Herramienta(s) que usé

The Sleuth Kit (`fsstat`, `fls`, `mactime`, `icat`), `grep`, Python (para decodificar Base64).

## Pasos (solo lo esencial, tipo lista)

- Descomprimí la imagen: `gunzip partition4.img.gz`
- Confirmé el tipo de sistema de archivos con `fsstat partition4.img` → **Ext4**, con fecha de último montaje/desmontaje visibles
- Generé el "bodyfile" (listado crudo de metadata MACB de todos los archivos) con:
    
    ```
    fls -f ext4 -r -m / partition4.img > bodyfile.txt
    ```
    
- Construí la timeline ordenada cronológicamente con:
    
    ```
    mactime -b bodyfile.txt -d > timeline.csv
    ```
    
- Filtré por el flag `macb` (archivos creados y nunca modificados después) y revisé qué actividad había cerca del apagado del sistema (última entrada de log a las 17:50:23)
- Encontré varios candidatos con `macb`, entre ellos `/root/.ash_history` (solo contenía el comando `poweroff`, sospechosamente vacío) y un archivo fuera de lugar: **`/etc/chat`**, creado 16 segundos antes de toda la ráfaga de apagado
- Revisé el contenido de `/etc/chat` con `icat` (usando su número de inodo) — era una cadena de texto en **Base64**
- Decodifiqué esa cadena en Base64 y obtuve la flag en texto plano

## Comando(s) o payload clave (si aplica)

```bash
gunzip partition4.img.gz
fsstat partition4.img
fls -f ext4 -r -m / partition4.img > bodyfile.txt
mactime -b bodyfile.txt -d > timeline.csv

# filtrar archivos recién creados (los 4 timestamps coinciden)
grep ",macb," timeline.csv

# revisar contenido de un archivo por su inodo
icat -f ext4 partition4.img 32716   # /etc/chat
```

```python
import base64
print(base64.b64decode("NTczNDE3aDEzcl83aDRuXzdoM18xNDU3XzU4NTI3YmIyMjIK").decode())
# -> 573417h13r_7h4n_7h3_1457_58527bb222
```

## Flag

`picoCTF{573417h13r_7h4n_7h3_1457_58527bb222}`

## ¿Qué aprendí / qué usaría de nuevo?

El flag `macb` en una timeline de `mactime` es la señal más clara de un archivo plantado/creado artificialmente (todas sus marcas de tiempo coinciden porque nunca se volvió a tocar después de crearlo) — es el primer patrón a buscar en un reto de "encuentra la acción anti-forense". También aprendí a usar `icat` con el número de inodo (que ya sale directo en cada línea de la timeline) para leer el contenido de cualquier archivo sin necesidad de montar la imagen completa.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, al principio `mactime` fallaba con "Can't open bodyfile.txt" porque nunca llegué a generar el bodyfile con `fls` antes de intentar usarlo — lo resolví corriendo `fls -f ext4 -r -m / partition4.img > bodyfile.txt` primero y confirmando que tuviera contenido real (`wc -l`, `head`) antes de pasar al siguiente paso.

---
