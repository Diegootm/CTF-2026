# Otras Vulnerabilidades Comunes

Estas no estaban en el material original, pero aparecen tan seguido en CTF de web que vale la pena tenerlas cubiertas desde ya (no queríamos dejarlas fuera).

## IDOR (Insecure Direct Object Reference)
Ocurre cuando una aplicación expone directamente un identificador (ej. el ID de un usuario o pedido en la URL) sin verificar si quien hace la petición tiene permiso de verlo.

**Ejemplo:**
```
https://ejemplo.com/factura?id=1023
```
Si al cambiar el número a `id=1024` pueden ver la factura de otra persona sin ningún control de permisos, es un IDOR. Es una de las vulnerabilidades más simples de encontrar y explotar: básicamente, cambiar un número o ID y ver qué pasa.

## SSRF (Server-Side Request Forgery)
Ocurre cuando una aplicación permite que el usuario controle, directa o indirectamente, una URL que el **servidor** va a visitar. Esto se puede abusar para que el servidor haga peticiones a recursos internos que normalmente no serían accesibles desde afuera (ej. servicios internos de la red, metadatos de un servidor en la nube).

**Ejemplo:** un campo "URL de tu foto de perfil" que el servidor descarga automáticamente — si aceptan cualquier URL, se podría apuntar a `http://localhost/admin` o a direcciones internas de la red.

## JWT (JSON Web Tokens) mal implementados
Los JWT se usan para manejar sesiones/autenticación sin guardar estado en el servidor. Contienen tres partes (header, payload, firma) codificadas en Base64. Errores comunes que se explotan en CTF:
- **Algoritmo `none`**: algunos servidores mal configurados aceptan tokens sin firma si el header dice `"alg": "none"`.
- **Clave de firma débil o adivinable**: si la clave secreta usada para firmar el token es débil, se puede crackear por fuerza bruta (similar a crackear un hash) y luego falsificar tokens propios.
- **Confusión de algoritmo (RS256 → HS256)**: un ataque más avanzado donde se engaña al servidor para que valide con un algoritmo distinto al que espera.

## Subida de archivos maliciosa (Insecure File Upload)
Cuando una aplicación permite subir archivos (ej. una foto de perfil) sin validar correctamente su tipo real, se puede subir un archivo con extensión permitida (`.jpg`) pero contenido malicioso (ej. código PHP), o directamente burlar el filtro de extensión (`.php.jpg`, `.phtml`, mayúsculas `.PHP`) para lograr que el servidor lo ejecute como código.

## Por qué las incluimos aquí
Estas cuatro son extremadamente comunes en CTF de dificultad media, y suelen combinarse con lo que ya vieron: por ejemplo, un IDOR que expone un JWT, o un SSRF que se usa para leer un archivo local (relacionándose con LFI). Vale la pena tenerlas identificadas de nombre aunque no las dominen a fondo todavía — para eso está también el archivo [10-Para-Investigar-Despues](Apuntes-Hacking-Etico/03-Web/10-Para-Investigar-Despues.md).
