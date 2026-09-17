Área: Forense Dificultad: Media-Alta Plataforma: CIDSI  Link del reto o Nombre: Discos-Horrocruxes (`1-discos.zip`) Resuelto por: Axel Fecha: 16/09 Tiempo que tardé: ~40 min

¿Qué pista/detalle me hizo saber por dónde ir? Al descomprimir el zip solo había 2 archivos (`disk2.img`, `disk3.img`) de 100MB cada uno, pero el enunciado decía explícitamente que el servidor tenía 3 discos y que "al haber sido el asalto problemático, solo se pudo obtener info de dos". Eso ya me dijo que esto no era un disco normal, sino un arreglo de varios discos (RAID) al que le faltaba una pieza — de ahí el nombre "Horrocruxes": la información estaba fragmentada entre varios objetos y había que reunirla. Correr `file` sobre las imágenes lo confirmó al toque: eran miembros de un **RAID Linux (mdadm) nivel 5** con `disks=3`, mismo UUID de array y mismo nombre (`amelia:0`).

Pasos (solo lo esencial, tipo lista)

- `file disk2.img disk3.img` → confirma que son miembros RAID5 (mdadm 1.2), 3 discos, mismo array UUID.
- `mdadm --examine` sobre cada imagen (sin necesidad de montar nada) → obtengo los metadatos clave: `Raid Devices: 3`, `Chunk Size: 512K`, `Layout: left-symmetric`, `Data Offset: 4096 sectores`, y el rol de cada disco (`disk2.img` = rol 1, `disk3.img` = rol 2; falta el rol 0).
- El entorno no tenía acceso al módulo del kernel `md` (contenedor sin privilegios), así que no pude usar `mdadm --assemble` de forma normal. Reconstruí el array **en Python**, aprovechando la propiedad matemática de RAID5: para cada bloque de una misma "fila" (stripe), `disco_A XOR disco_B XOR disco_C = 0`. Esto significa que el disco faltante se puede recalcular perfectamente con el XOR de los otros dos, sin tocar el kernel para nada.
- Reimplementé el algoritmo de distribución `left-symmetric` (el que usa mdadm por defecto) para saber, stripe por stripe, qué disco tenía cada chunk de datos y cuál tenía la paridad, y así reordenar todo en un archivo lineal.
- El archivo reconstruido dio exactamente el tamaño esperado (`Array Size` reportado por mdadm), buena señal de que el layout estaba bien calculado.
- `blkid` sobre el array reconstruido → detecta un filesystem **exFAT**. Lo monto con `mount.exfat-fuse`.
- Dentro del exFAT solo había un archivo: `md0`, que resultó ser un **volcado comprimido con gzip** (`file` mostró el tamaño original sin comprimir, que coincidía exacto con el tamaño del array).
- Descomprimo `md0.gz` → aparece un filesystem **ext4** completo. Lo monto.
- Dentro del ext4 hay varios archivos: `Instrucciones.txt`, `si.txt`, dos imágenes (un meme de gato y un avatar, ambos señuelos), un logo SVG (señuelo también), y `PLE200 FW3.3 Rev2 NA.bin` (firmware de un adaptador PowerLine).
- `Instrucciones.txt` decía literalmente que ese firmware estaba "hackeado" para intervenir los adaptadores PowerLine, y pedía que nadie fuera del ejército lo obtuviera — señal clara de que ahí estaba lo importante.
- `si.txt` decodificado en base64 resultó ser un RFC público (RFC 8276) sin relación con la flag: puro señuelo para hacer perder tiempo.
- `binwalk`, `strings` y revisar visualmente las imágenes no mostraron nada oculto a simple vista en el `.bin` ni en las imágenes.
- El detalle que realmente resolvió el reto: revisar los **atributos extendidos (xattrs)** de cada archivo con `getfattr -d`. `ls` normal no los muestra, pero ahí apareció un atributo `user.fl4g` en el firmware con el texto `casinadieconocelosatributosextendidos` ("como si nadie conociera los atributos extendidos" — literalmente burlándose de que ahí estaba escondida la pista).
- Esa cadena era el texto que había que pasar por MD5 según el formato pedido (`cidsi{Flag_en_MD5}`).

Comando(s) o payload clave (si aplica)

```bash
# Examinar metadata RAID sin montar nada
mdadm --examine disk2.img
mdadm --examine disk3.img

# Reconstrucción RAID5 vía XOR (Python), sin necesitar el módulo del kernel
missing_chunk = chunk_disk1 XOR chunk_disk2

# Identificar filesystem tras reconstruir
blkid raid_reconstructed.img   # -> exFAT

# Dentro del exFAT: md0 era un gzip
file md0     # gzip compressed data, original size = tamaño exacto del array
gunzip md0.gz
file md0     # ext4 filesystem

# El detalle que dio la flag
getfattr -d "PLE200 FW3.3 Rev2 NA.bin"
# user.fl4g="casinadieconocelosatributosextendidos"

echo -n "casinadieconocelosatributosextendidos" | md5sum
```

Flag 
cidsi{293332c9ccc2a8d7cc7e83868768cdf9}

¿Que aprendí / qué usaría de nuevo?

- Que cuando un enunciado menciona explícitamente "faltó un disco" o "solo se pudo recuperar parte de la info", es casi siempre una señal directa de que se trata de un RAID (o algo con redundancia) y que la matemática del XOR puede salvar el día aunque no tengas acceso a herramientas de sistema como mdadm/kernel real.
- Que los datos pueden venir anidados en capas (RAID → exFAT → gzip → ext4) y hay que ir "pelando la cebolla" con `file`/`blkid` en cada paso en vez de asumir que ya se llegó al final.
- Que los **atributos extendidos** son un lugar clásico para esconder cosas en forense de Linux y que `ls -la` jamás los va a mostrar — siempre correr `getfattr -d` sobre archivos sospechosos, sobre todo si el reto insiste en que "hay algo escondido".
- Que en estos retos suele haber señuelos (memes, logos, RFCs públicos) diseñados para hacer perder tiempo — conviene priorizar el archivo que el propio enunciado señala como "sensible" o "confidencial".

¿Me trabé en algo? ¿Cómo lo destrabé? 
Me trabé al intentar montar el RAID de la forma "normal" con `mdadm --assemble`, porque el contenedor no tenía acceso al módulo del kernel `md` (`failed to load MD subsystem`). Lo destrabé dándome cuenta de que no necesitaba el kernel para nada: con solo `mdadm --examine` (que únicamente lee metadatos, no ensambla nada) pude sacar el layout exacto (chunk size, offset, algoritmo, roles de cada disco) y reconstruir el array manualmente en Python usando XOR. También me trabé un poco al no encontrar la flag después de revisar contenido, strings y esteganografía básica de las imágenes — lo que me destrabó fue acordarme de revisar capas "invisibles" del sistema de archivos como los atributos extendidos, que no aparecen con un simple `ls`.