
**Área:** Cripto **Dificultad:** Medio **Plataforma:** picoCTF 2022 **Link del reto o Nombre:** código morse **Resuelto por:** Diego **Fecha:** 23/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El nombre del reto y el enunciado ya lo decían directamente: era código Morse. El archivo entregado era un audio (`morse_chal.wav`), así que el Morse venía codificado como sonido (pitidos cortos/largos) en vez de como texto con puntos y rayas.

## Herramienta(s) que usé

Decodificador de Morse por audio online: https://morsecw.com/decodificador.html

## Pasos (solo lo esencial, tipo lista)

- Descargué el archivo `morse_chal.wav` del reto
- Lo subí al decodificador de audio Morse (morsecw.com)
- El decodificador devolvió el texto: `WH47 H47H 90D W20U9H7`
- Seguí el formato pedido por el enunciado: todo en minúsculas, guiones bajos en vez de espacios, envuelto en `picoCTF{}`

## Comando(s) o payload clave (si aplica)

```
Texto decodificado del audio: WH47 H47H 90D W20U9H7
Formateado: wh47_h47h_90d_w20u9h7
```

## Flag

`picoCTF{wh47_h47h_90d_w20u9h7}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando el Morse viene como archivo de audio (.wav) y no como texto de puntos/rayas, conviene un decodificador que acepte audio directamente en vez de transcribir a mano — ahorra tiempo y evita errores de oído. `morsecw.com/decodificador.html` funciona bien para esto.

## ¿Me trabé en algo? ¿Cómo lo destrabé?
