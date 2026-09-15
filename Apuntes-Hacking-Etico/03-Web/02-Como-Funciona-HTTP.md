# Cómo funciona HTTP (lo mínimo indispensable)

## ¿Por qué hay que entender esto antes de atacar nada?
Casi todos los ataques web (SQLi, XSS, LFI, etc.) se ejecutan **a través de peticiones HTTP**. Si no entienden cómo se arma una petición y una respuesta, van a estar "atacando a ciegas" sin entender qué le están mandando realmente al servidor.

## El ciclo básico: petición → respuesta
1. El navegador (o `curl`, o Burp) envía una **petición** (request) a un servidor.
2. El servidor procesa esa petición y devuelve una **respuesta** (response).

## Partes de una petición HTTP
- **Método**: qué acción se quiere realizar. Los más comunes:
  - `GET`: pedir datos (ej. cargar una página). Los parámetros suelen ir en la URL (`?usuario=admin`).
  - `POST`: enviar datos (ej. un formulario de login). Los parámetros van en el "cuerpo" de la petición, no en la URL.
- **URL/ruta**: a qué recurso se apunta (ej. `/login`, `/api/usuarios`).
- **Headers**: metadatos de la petición (ej. `Cookie`, `User-Agent`, `Content-Type`, `Authorization`).
- **Cuerpo (body)**: los datos enviados, sobre todo en POST (ej. `usuario=admin&password=123`).

## Partes de una respuesta HTTP
- **Código de estado**: un número que indica qué pasó. Los que más van a ver:
  - `200 OK`: todo bien.
  - `301/302`: redirección a otra página.
  - `400 Bad Request`: la petición estaba mal formada.
  - `401/403`: no autorizado / prohibido (problemas de permisos).
  - `404 Not Found`: el recurso no existe.
  - `500 Internal Server Error`: algo falló del lado del servidor — **muy útil en CTF**, a menudo indica que lograron romper algo con su input.
- **Headers de respuesta**: también dan pistas (ej. qué tecnología usa el servidor).
- **Cuerpo**: el contenido real (HTML de la página, JSON de una API, etc.).

## Cookies y sesiones
Como HTTP no "recuerda" quién sos entre una petición y otra, los sitios usan **cookies** para mantener la sesión: el servidor te da una cookie al iniciar sesión, y el navegador la reenvía automáticamente en cada petición siguiente. Muchos ataques web (XSS, manipulación de sesión) giran en torno a robar o falsificar estas cookies.

## Cómo ver todo esto en la práctica
- **DevTools del navegador** (`F12` → pestaña "Network"): ven cada petición que hace la página, con sus headers, body y respuesta.
- **Burp Suite**: intercepta y permite modificar las peticiones antes de que lleguen al servidor (ver [09-Herramientas](Apuntes-Hacking-Etico/03-Web/09-Herramientas.md)).
- **curl**: para armar peticiones manualmente desde la terminal, sin interfaz gráfica.
