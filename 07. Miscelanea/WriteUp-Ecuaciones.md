**Área:** Criptografía / Misceláneo **Dificultad:** Fácil **Plataforma:** CITSI **Link del reto o Nombre:** Ecuaciones (100 pts) **Resuelto por:** Diego **Fecha:** 05/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado solo decía "Un juego de... Suma, Resta, Multiplicación y División; fácil!!!", y el archivo adjunto era un PDF protegido con contraseña. Eso ya indicaba dos etapas separadas: primero romper la contraseña del PDF, y luego resolver algún acertijo matemático adentro.

## Herramienta(s) que usé

- `exiftool` (para confirmar que el PDF tenía cifrado de contraseña de usuario, no solo de permisos)
- `pdfcrack` (fuerza bruta de la contraseña del PDF)
- Python (para resolver las 20 operaciones y convertir los resultados a caracteres ASCII)

## Pasos (solo lo esencial, tipo lista)

- Confirmé con `exiftool Ecuaciones.pdf` que el PDF tenía **Encryption: Standard V4.4 (128-bit)** y pedía contraseña para abrirse
- Intenté primero con `john` + `pdf2john.pl`, pero me trabé bastante con dependencias de Perl/ExifTool incompatibles entre versiones — terminé abandonando esa vía
- Cambié a **`pdfcrack`**, mucho más simple y directo para este tipo de cifrado, probando fuerza bruta solo con dígitos
- La contraseña resultó ser `123456`
- Abrí el PDF y encontré una hoja tipo "examen" titulada **"Montañas de Ecuaciones"**, con 20 operaciones de suma, resta, multiplicación y división numeradas del 1 al 20
- Resolví las 20 operaciones y noté que todos los resultados caían en el rango 97-117 — exactamente el rango de **códigos ASCII de letras minúsculas** (`a`=97 ... `u`=117)
- Convertí cada resultado a su carácter ASCII correspondiente (con `chr()`) y los concatené en orden (1 al 20)
- El mensaje resultante fue: `sonecuacionesbasicas` → **"son ecuaciones básicas"**
- Convertí esa frase (con guiones bajos) al formato de flag que usa la plataforma

## Comando(s) o payload clave (si aplica)

```bash
# romper la contraseña del PDF
pdfcrack -f Ecuaciones.pdf -c "0123456789" -n 1 -m 8
# contraseña encontrada: 123456
```

```python
ops = [
    47+32+36, 29+65+17, 55+38+17, 51-17-33+100, 200-38-65+2,
    352-45-78-65-47, 156-8-30-21, 204-49-56, 35*3, 37*3,
    330//3, 303//3, 460//4, 14*7, 189-52-9-31,
    460//4, 35*3, 33*3, 485//5, 23*5,
]
mensaje = ''.join(chr(v) for v in ops)
print(mensaje)  # -> "sonecuacionesbasicas"
```

## Flag

`cidsi{01fb37fc148ba35c3dea6a1f9579eabf}`

(el hash corresponde al texto `son_ecuaciones_basicas`)

## ¿Qué aprendí / qué usaría de nuevo?

Cuando los resultados de una serie de operaciones matemáticas caen todos en un rango entre ~32 y ~126, vale la pena sospechar que son **códigos ASCII disfrazados** — es un patrón común en retos "fáciles" de este estilo. También aprendí que para romper contraseñas de PDF, `pdfcrack` es mucho más simple y directo que armar la cadena `pdf2john.pl` + `john`, sobre todo en sistemas donde las versiones de los módulos de Perl (como ExifTool) no calzan con lo que el script viejo espera — mejor no perder tiempo peleando con eso si `pdfcrack` puede hacer el mismo trabajo.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, bastante — intenté usar `john` con `pdf2john.pl` primero, pero el script pedía el módulo `ExifTool.pm` de una forma incompatible con la versión moderna que trae Debian (`Image::ExifTool.pm`), y aunque instalé varias dependencias de Perl, seguía sin funcionar (error de "Can't locate object method new via package ExifTool"). Lo resolví abandonando esa ruta por completo y usando `pdfcrack` en su lugar, que no depende de Perl y resolvió la contraseña numérica en poco tiempo.

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._