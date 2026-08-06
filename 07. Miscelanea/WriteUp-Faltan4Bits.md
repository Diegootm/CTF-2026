**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** Faltan 4 Bits (175 pts) **Resuelto por:** Diego **Fecha:** 06/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía: _"Nos robaron 4 bits de la flag, para que el reto no caiga pusieron 4 bits de repuesto, pero no son los correctos."_ Eso indicaba que había que buscar un archivo con datos faltantes/reemplazados de exactamente medio byte (un nibble = 4 bits), y que el archivo entregado (`construccion.jpg`) probablemente escondía algo más adentro.

## Herramienta(s) que usé

- `exiftool` y `xxd` (inspección inicial)
- `binwalk` (para detectar y extraer el ZIP oculto dentro del JPG)
- `dd` (para sobreescribir los bytes corruptos con la firma correcta)
- `file` (para confirmar que el archivo quedó válido tras el parche)

## Pasos (solo lo esencial, tipo lista)

- Corrí `exiftool` y `xxd` sobre `construccion.jpg` — se veía como un JPEG normal a simple vista
- `binwalk construccion.jpg` reveló un **archivo ZIP embebido** dentro de la imagen, con un archivo adentro llamado `flag`
- Extraje el ZIP con `binwalk -e construccion.jpg`
- Al revisar el archivo `flag` extraído con `xxd`, resultó ser **otro JPEG**, pero con los primeros 4 bytes corruptos: en vez de la firma estándar `ff d8 ff e0`, tenía `d3 f5 a1 c7`. El resto del archivo (desde el byte 4 en adelante) coincidía exactamente con la estructura normal de un JPEG/JFIF
- Como la firma de un JPEG siempre es fija y conocida (no hay que adivinarla), sobreescribí esos 4 bytes corruptos directamente con la firma correcta usando `dd`
- Con el archivo ya reparado, `file` lo reconoció correctamente como imagen JPEG
- Al abrir la imagen reparada, apareció el texto: `m4g1c_numb3rs_meg4_r3d`
- Convertí ese texto al formato de flag pedido por la plataforma

## Comando(s) o payload clave (si aplica)

```bash
binwalk -e construccion.jpg
cd _construccion.jpg.extracted/1D34C

# los primeros 4 bytes del archivo "flag" estaban corruptos
# (d3 f5 a1 c7 en vez de la firma JPEG real ff d8 ff e0)
cp flag flag_reparada.jpg
printf '\xff\xd8\xff\xe0' | dd of=flag_reparada.jpg bs=1 count=4 conv=notrunc

file flag_reparada.jpg   # ahora reconocido como JPEG válido
xdg-open flag_reparada.jpg
```

## Flag

`citc{m4g1c_numb3rs_meg4_r3d}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un archivo binario tiene bytes corruptos justo al inicio, lo primero es comparar contra la **firma mágica** (magic number) estándar del tipo de archivo que se espera — muchos formatos (JPEG, PNG, ZIP, etc.) empiezan siempre con los mismos bytes fijos, así que si el resto del archivo se ve estructuralmente correcto, no hace falta adivinar nada: se sobreescribe directamente con el valor conocido. La fuerza bruta (probar las 16 combinaciones de un nibble) solo hubiera sido necesaria si el valor correcto NO fuera un estándar fijo y conocido de antemano.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._