
**Área:** Explotation web **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Cookies **Resuelto por:** Xavi **Fecha:** 8/09 **Tiempo que tardé:** ~30 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El nombre del reto ("Who doesn't love cookies?") y el formulario de búsqueda de galletas ya sugerían que el mecanismo central era una cookie. Al enviar distintos nombres de galleta, noté que el servidor devolvía una cookie con un valor numérico distinto según la galleta elegida, lo que indicaba que ese número funcionaba como índice/id interno.

## Herramienta(s) que usé

- Navegador (DevTools → Application/Storage → Cookies)
- Edición manual de cookies

## Pasos (solo lo esencial, tipo lista)

- Envié distintos nombres de galleta en el formulario de búsqueda y observé que el valor de la cookie cambiaba según la galleta.
- Al notar que era un número editable, probé modificar manualmente el valor de la cookie desde DevTools.
- Cambié el valor a **18** (fuera del rango normal de galletas del formulario) y recargué la página.
- Con ese valor, el contenido mostrado en pantalla cambió, revelando la flag.

## Comando(s) o payload clave

```
Cookie: name=18
```

## Flag

```
(pendiente - agrégala aquí)
```

## ¿Qué aprendí / qué usaría de nuevo?

Aprendí que confiar en un valor del lado del cliente (la cookie) para decidir qué contenido mostrar es una mala práctica de seguridad, ya que cualquier usuario puede modificarlo directamente. Es una técnica que probaría de nuevo: probar valores fuera del rango esperado (índices altos, negativos, no numéricos) cuando una cookie parece actuar como índice o id.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Al inicio no era obvio qué números probar. Me destrabé probando sistemáticamente distintas galletas para mapear qué valores usaba el sitio normalmente, y luego animándome a probar un número fuera de ese rango (18), lo cual disparó el comportamiento distinto que reveló la flag.