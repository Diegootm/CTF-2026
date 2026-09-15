Apuntes de referencia rápida para retos de CTF categoría Forense, usando **The Sleuth Kit (TSK)** en Debian/Linux.
## 0. Instalación

```bash
sudo apt install sleuthkit
```

Incluye: `mmls`, `fsstat`, `fls`, `icat`, `istat`, `mactime`, `blkcat`, `img_stat`, entre otros.

---

## 1. Metodología general (orden recomendado)

```
file  →  mmls (si aplica)  →  fsstat  →  timeline completa (fls + mactime)
   →  mirar extremos (inicio/fin)  →  filtrar anomalías (macb, ubicación rara)
   →  icat para leer contenido  →  logs específicos
```

---

## 2. Identificar el archivo

```bash
file imagen.img
```

Diferencia entre:

- **Imagen de disco completo** (tiene tabla de particiones, MBR/GPT)
- **Imagen de una sola partición** (sistema de archivos directo, sin tabla de particiones)

Si viene comprimida:

```bash
gunzip -k imagen.img.gz      # -k conserva el .gz original
```

---

## 3. Ver particiones (solo si es un disco completo)

```bash
mmls imagen.img
```

Muestra offset (en sectores), tamaño y tipo de cada partición. Ese offset se necesita para todos los comandos siguientes cuando se trabaja sobre un disco completo (no sobre una partición ya aislada):

```bash
fsstat -o <offset> imagen.img
fls -o <offset> -r -m / imagen.img > bodyfile.txt
icat -o <offset> imagen.img <inodo>
```

---

## 4. Info general del sistema de archivos

```bash
fsstat imagen.img
```

Da información clave de entrada:

- Tipo de FS (ext4, NTFS, FAT32, etc.)
- Última vez montado / desmontado (¿se cerró limpio o no?)
- Rango de inodos y bloques
- Features especiales (journal, extents, etc.)

---

## 5. Listar archivos y construir la Timeline (MAC/MACB)

### Generar el "bodyfile" (metadata cruda de todos los archivos)

```bash
fls -f ext4 -r -m / imagen.img > bodyfile.txt
```

- `-f <fs_type>` → fuerza el tipo de FS si `fls` no lo detecta solo (usar el que dio `fsstat`)
- `-r` → recursivo
- `-m /` → formato "bodyfile" compatible con `mactime`, con `/` como punto de montaje raíz

### Construir la timeline ordenada cronológicamente

```bash
mactime -b bodyfile.txt -d > timeline.csv
```

`-d` = salida en CSV.

### Revisar panorama general antes de filtrar

```bash
wc -l timeline.csv
head -30 timeline.csv     # actividad más antigua
tail -30 timeline.csv     # actividad más reciente (normalmente donde pasó "la acción")
```

---

## 6. Qué significan las columnas MACB

Cada línea trae un campo de 4 caracteres, uno por cada timestamp, marcado con la letra correspondiente si ese evento ocurrió en ese momento exacto, o `.` si no:

|Letra|Significado|
|---|---|
|**M**|Modified — contenido del archivo modificado|
|**A**|Accessed — archivo leído/accedido|
|**C**|Changed — metadata cambiada (permisos, dueño, etc.)|
|**B**|Born — creado|

### Patrones a buscar

- **`macb`** (las 4 juntas) → archivo **creado y nunca vuelto a tocar** — la señal más fuerte de un archivo plantado/agregado artificialmente. Es el primer patrón a buscar en cualquier reto de "encuentra la acción anti-forense".
    
    ```bash
    grep ",macb," timeline.csv
    ```
    
- **`.a..`** → solo accedido (lectura normal, ej. binarios ejecutados en el arranque) — generalmente ruido, se puede filtrar/excluir.
- **`m.c.`** → modificado y con metadata cambiada, pero no marcado como "nuevo" — típico de directorios que reciben archivos nuevos adentro.
- Ráfagas de actividad muy concentradas en pocos segundos → casi siempre corresponden al momento del ataque, instalación de malware, o limpieza de evidencia.

---

## 7. Leer el contenido de un archivo por su inodo

No hace falta montar la imagen para leer un archivo — cada línea del `timeline.csv`/`bodyfile.txt` ya trae el número de inodo:

```bash
icat -f ext4 imagen.img <inodo>
```

Ejemplo real:

```bash
icat -f ext4 partition4.img 32716
```

Para ver el archivo en hexadecimal (por si el contenido no es texto plano):

```bash
icat -f ext4 imagen.img <inodo> | xxd | less
```

Para ver metadata detallada de un inodo específico (timestamps exactos, bloques que ocupa, etc.):

```bash
istat -f ext4 imagen.img <inodo>
```

---

## 8. Dónde buscar anomalías (por orden de sospecha)

1. **Archivos con flag `macb`** en la timeline (creados, nunca tocados después)
2. **Archivos/carpetas fuera de su ubicación típica** — ej. un archivo suelto en `/etc/` que no es parte del sistema base, algo en `/tmp`, `/var/tmp`, `/dev/shm`
3. **Historiales de shell sospechosamente cortos/vacíos** — `.bash_history`, `.ash_history`, `.zsh_history` con muy pocas líneas (indicio de limpieza manual)
4. **Nombres de archivo que imitan archivos legítimos** (ej. `passwd` con espacio al final, `ls` duplicado en una carpeta rara)
5. **Timestamps que no encajan con el resto** — todo el sistema instalado en una fecha, y un archivo aislado con fecha mucho más reciente

---

## 9. Logs útiles para revisar (con `icat` + el inodo correspondiente)

|Ruta|Contenido|
|---|---|
|`/var/log/messages` o `/var/log/syslog`|Log general del sistema (arranques, apagados, servicios)|
|`/var/log/auth.log`|Intentos de login, sudo, SSH|
|`/var/log/wtmp`|Historial de logins (binario, usar `utmpdump` o `last -f`)|
|`/root/.bash_history` / `.ash_history`|Comandos ejecutados por root|
|`/home/<user>/.bash_history`|Comandos ejecutados por un usuario específico|
|`/etc/passwd`, `/etc/shadow`|Usuarios del sistema, posibles cuentas agregadas|
|`/etc/crontab`, `/var/spool/cron/`|Tareas programadas (persistencia de malware)|

---

## 10. Comandos de referencia rápida (cheat sheet)

```bash
# Descomprimir
gunzip -k imagen.img.gz

# Ver particiones (disco completo)
mmls imagen.img

# Info del sistema de archivos
fsstat imagen.img

# Listar todo recursivamente (sin timeline, solo listado)
fls -r imagen.img

# Generar bodyfile + timeline
fls -f ext4 -r -m / imagen.img > bodyfile.txt
mactime -b bodyfile.txt -d > timeline.csv

# Filtrar archivos "plantados" (macb)
grep ",macb," timeline.csv

# Leer contenido de un archivo por inodo
icat -f ext4 imagen.img <inodo>

# Ver metadata detallada de un inodo
istat -f ext4 imagen.img <inodo>

# Extraer TODOS los archivos de la imagen a una carpeta (si hace falta explorar a fondo)
tsk_recover -f ext4 imagen.img carpeta_salida/
```

---

## 11. Notas rápidas / recordatorios

- Si `fls`/`fsstat` fallan sin especificar tipo, forzar con `-f <tipo>` (ext4, ntfs, fat32, etc.), usando el dato que ya dio `fsstat`.
- Antes de generar la timeline, confirmar que `bodyfile.txt` tenga contenido real (`wc -l`, `head`) — si `mactime` da error "Can't open bodyfile.txt", casi siempre es porque el `fls` anterior falló o no se corrió.
- El decodificador de Base64 es buen primer sospechoso si el contenido de un archivo "raro" se ve como texto sin sentido pero con caracteres válidos de Base64 (letras, números, `+`, `/`, `=`).