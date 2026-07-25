

**Área:** Forense **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Mod26 **Resuelto por:** Xavi **Fecha:** 23/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El problema ya empezaba dando una imagen entonces empeze a buscar informacion dentro de esta imagen
## Herramienta(s) que usé

exiftool (en una imagen donde estaba la clave)
steghide 

## Pasos (solo lo esencial, tipo lista)

- Inspeccionar la imagen pues sabia que encontraria algo para encontrar la flag
- en los metadatos estaba una pista que decia "steghide:cEF6endvcmQ=" que la parte despues de sthegide era un cifrado de base64 que era pAzzword
- use la herramienta usando la contraseña y extraje el archivo flag.txt que contenia la bandera
## Comando(s) o payload clave (si aplica)

```
steghide extract -sf img.jpg -p pAzzword
```

## Flag

picoCTF{h1dd3n_1n_1m4g3_5d4cba73}

## ¿Que aprendí / qué usaría de nuevo?

que siempre se deben buscar opciones para revisar bien un archivo sea png o cualquier otra en este caso era steghide

## ¿Me trabé en algo? ¿Cómo lo destrabé?

no sabia que hacer con la contraseña hasta que dieron una pista clara con steghide y asi pude encontrar rapidamente la flag 