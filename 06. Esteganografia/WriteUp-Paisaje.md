
**Área:** Stego **Dificultad:** facil **Plataforma:**  CIDSI **Link del reto o Nombre:** Paisaje **Resuelto por:** Xavi **Fecha:**30/07/2026  **Tiempo que tardé:** 15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

Pues fue sencillo pues solo debia analizar el archivo en este caso un jpg y ya encontre la flag rapidamente 

## Herramienta(s) que usé

exiftool 
file
## Pasos (solo lo esencial, tipo lista)

- Primero verifique se realmente sea un jpg o imagen lo cual resulto veridico
- Trate de seguir buscando y no encontre nada oculto entonces hice un exiftool
- En los metadatos estaba la flag en llaves servida {}
## Comando(s) o payload clave (si aplica)

```
exiftool Paisaje.jpg
```

## Flag

cidsi{BlackMagic}

## ¿Qué aprendí / qué usaría de nuevo?

Que a veces la flag esta literalmente en el enunciado 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

solo le di la vuelta a la palabra clave y halle la flag 
