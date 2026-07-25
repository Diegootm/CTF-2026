Área: Web Exploiting Dificultad: Fácil Plataforma: Cylab Nombre: LocalAutority Resuelto por: Diego Fecha: 25/07/2026 Tiempo que tardé: 15  minutos

## ¿Qué pista/detalle me hizo saber por dónde ir?

toda la validación del login (usuario/contraseña y el chequeo de credenciales) corría en JavaScript del lado del cliente, y el hash que se necesitaba mandar al servidor ya estaba hardcodeado como texto plano en el propio código fuente de la página

## Herramienta(s) que usé

DevTools del navegador (pestañas Elements y Console)

## Pasos (solo lo esencial, tipo lista)

- entré a la página "Secure Customer Portal" y abrí el inspector (F12) para ver el código fuente
- encontré un `<script>` con una función `filter()` que solo permitía letras y números en usuario/contraseña, y un flujo que llamaba a `checkPassword(username, password)`
- noté que si `checkPassword` devolvía `true`, el código ponía un hash hardcodeado (`"2196812e91c29df34f5e217cfd639881"`) en un input oculto (`adminFormHash`) y enviaba un formulario oculto (`hiddenAdminForm`)
- la función `checkPassword` no estaba visible en el código que tenía disponible — no hacía falta encontrarla ni hacer que devolviera `true` de verdad, porque el hash ya estaba fijo en el HTML
- como toda la lógica corre en el navegador, usé la consola de DevTools para saltarme el chequeo por completo y disparar el envío del formulario oculto manualmente
- Chrome pidió confirmar "allow pasting" antes de dejar pegar comandos en la consola (protección anti self-XSS, no relacionada al reto) — lo tipeé y seguí
- ejecuté el submit manual con el hash ya hardcodeado, sin depender de `checkPassword`, y el login como admin se resolvió

## Comando(s) o payload clave (si aplica)

```js
document.getElementById('adminFormHash').value = "2196812e91c29df34f5e217cfd639881";
document.getElementById('hiddenAdminForm').submit();
```

## Flag 

picoCTF{j5_15_7r4n5p4r3n7_a8788e61}

## ¿Qué aprendí / qué usaría de nuevo?

que cuando toda la validación de login pasa por JavaScript client-side, no hace falta "romper" la lógica ni entender cada función involucrada (como `checkPassword`, que ni siquiera encontré) — alcanza con ejecutar manualmente en la consola del navegador la acción final que el código dispararía si la validación pasara, saltándose el flujo intermedio por completo

## ¿Me trabé en algo? ¿Cómo lo destrabé?

sí, al no encontrar la función `checkPassword` en el código fuente pensé que necesitaba localizarla para entender cómo bypassearla. Me destrabé al darme cuenta de que no hacía falta: el valor que el servidor necesitaba (el hash) ya estaba hardcodeado en el HTML, así que bastaba con ejecutar directamente en consola las dos líneas que ponen ese valor y mandan el formulario, sin depender de que la función devolviera `true`