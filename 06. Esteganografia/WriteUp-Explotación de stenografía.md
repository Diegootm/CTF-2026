**Área:** Stego **Dificultad:** medio **Plataforma:**  CIDSI **Link del reto o Nombre:** ## Explotación de stenografía **Resuelto por:** Xavi **Fecha:**07/09/2026  **Tiempo que tardé:** 30 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado mencioaba que la flag estaba dentro de la bomba y ademas que al hacer strings al archivo dado decia que tenia mas capas 
## Herramienta(s) que usé

strings 
exiftool archivo.jpg | grep -i thumbnail  //Para saber si existia thumbnail

exiftool -b -ThumbnailImage archivo.jpg > thumb.jpg //para sacarlo

## Pasos (solo lo esencial, tipo lista)

- Al inicio busque con exiftool y iba revisando todos los archivos o imagenes que arrojaba a lo cual lo unico que sirivo era el thumbnail
- Para confirmar tambien vi el binwalk donde descrubi que habian dos imagenes mas dentro la imagen
- fui extrayendo las imagenes y saque dos imagenes pero cuando les hacia exiftool o revisaba no encontraba nada y pense que no se podria 
- Al final use aperi'Solve para revisar la imagen y resulta que tenia la flag en la ultima imagen  que tiene una bomba nuclear de los simpsons
## Comando(s) o payload clave (si aplica)

```
exiftool -b -ThumbnailImage archivo.jpg > thumb.jpg
```

## Flag

citc{boomm}  //pasar a md5 

## ¿Qué aprendí / qué usaría de nuevo?

Ha no rendirse y que la flag puede estar en lo que tnemos en frente 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

con los distintos formatos de color de aperi Solve encontre la flag como texto en la imagen