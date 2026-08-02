**Área:** Esteganografía **Dificultad:** - **Plataforma:** CITC 2024 **Link del reto o Nombre:** La llave de tu corazon (140 pts) **Resuelto por:** Diego **Fecha:** 29/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía que un hacker encontró el archivo durante un pentest y aseguraba que era una "llave", pero advertía que "es un hacker muy bromista". Eso ya sugería que el archivo no era realmente lo que decía ser. Al correr `file` sobre el archivo, decía "OpenSSH private key", pero al ver el contenido con `xxd`, el cuerpo en Base64 empezaba con `/9j/4AAQSkZJRgABAQ...` — esa es la firma clásica de un JPEG en Base64, no de una llave SSH real.

## Herramienta(s) que usé

- `file` (para ver el tipo de archivo declarado)
- `xxd` (para inspeccionar el contenido crudo)
- `exiftool` y `binwalk` (para descartar otras pistas)
- `grep` + `base64 -d` (para extraer la imagen real oculta detrás del disfraz de llave SSH)

## Pasos (solo lo esencial, tipo lista)

- Descargué el archivo `La-llave-de-tu-corazon`
- Confirmé con `file` que decía ser una llave privada OpenSSH
- Con `xxd` noté que el cuerpo Base64 empezaba con `/9j/` (firma de JPEG), no con el contenido típico de una llave SSH real
- Quité las líneas `-----BEGIN OPENSSH PRIVATE KEY-----` / `-----END...-----` (que no son Base64 válido) y decodifiqué el resto como Base64 para reconstruir el archivo JPEG real
- Al abrir la imagen resultante, apareció el texto: `3st4_35_m1_ll4v3`
- Convertí ese texto a MD5 para obtener la flag en el formato pedido por el reto

## Comando(s) o payload clave (si aplica)

```bash
grep -v -- "-----" La-llave-de-tu-corazon | base64 -d > llave_secreta.jpg
```

```
Texto encontrado en la imagen: 3st4_35_m1_ll4v3
MD5("3st4_35_m1_ll4v3") -> 2a697898cf11b0e1263770cbd15ba1bc
```

## Flag

`citc{2a697898cf11b0e1263770cbd15ba1bc}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un archivo dice ser una llave SSH (o cualquier formato PEM) pero el reto insinúa que "no es lo que parece", vale la pena mirar el Base64 del cuerpo directamente — los primeros caracteres de Base64 delatan el tipo real de archivo (`/9j/` = JPEG, `iVBOR` = PNG, etc.). Quitando las líneas `BEGIN`/`END` y decodificando el resto como Base64 se recupera el archivo real sin importar qué encabezado le hayan puesto encima.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._