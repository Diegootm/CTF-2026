

**Área:** OSINT **Dificultad:** - **Plataforma:** CIDSI **Link del reto o Nombre:** The Dog (250 pts) **Resuelto por:** Diego **Fecha:** 24/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado hablaba en primera persona de "haber salido en el libro" y que el nombre estaba también "en mi lápida", además de venir acompañado de una imagen (`the_dog.jpg`). Eso apuntaba a que había que identificar a un perro específico y famoso (con historia/libro/registro incluso de su tumba), usando la imagen como pista visual.

## Herramienta(s) que usé

Búsqueda por imagen / búsqueda web para identificar al perro.

## Pasos (solo lo esencial, tipo lista)

- Analicé la imagen `the_dog.jpg`
- Busqué características del perro en la imagen para identificarlo
- Encontré que se trataba de **Pebbles**, el perro más longevo (el de mayor edad registrada)
- Convertí el nombre "Pebbles" a MD5
- Envolví el hash en el formato pedido por el reto

## Comando(s) o payload clave (si aplica)

```
Nombre identificado: Pebbles
MD5("Pebbles") -> 363bc9eaf4b338ffbb6dde6ce628e7e6
```

## Flag

`cidsi{363bc9eaf4b338ffbb6dde6ce628e7e6}`

## ¿Qué aprendí / qué usaría de nuevo?

En retos de OSINT con imágenes de animales/personas "famosas", conviene fijarse en las pistas del enunciado que sugieren un récord o hecho llamativo (aquí "el más longevo") — ese tipo de superlativos ("el más viejo", "el primero", "el único") suele ser la clave para encontrar rápido a quién identificar en una búsqueda.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

---

	