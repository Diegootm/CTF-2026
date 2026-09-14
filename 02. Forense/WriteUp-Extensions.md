**Área:** Forence **Dificultad:** Medio **Plataforma:** picoCTF  (vía CyLab Academy) **Link del reto o Nombre:** Extensions **Resuelto por:** Axel**Fecha:** 13/09 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El ejercicio te da un archivo flag.txt el cual con la ayuda de una terminal vemos qe tienen oculto probamos con exiftool, binwalk,strings y nos da pistas de que puede estar oculto pero con file flag.txt se confirma que si hay un archivo oculto y es una imagen desde la terminal se deberá poder abrircon: xdg-open flag.txt  o si primero cambiar la extencion con: 
mv flag.txt flag.png
xdg-open flag.png 
## Herramienta(s) que usé

Terminal de linux y el comando xdg-open

## Pasos (solo lo esencial, tipo lista)

- Ver que tiene oculto el archivo flag.txt
- Deducir que es una imagen.png
- abrir esa imagen con el comando:  xdg-open flag.png
- al ejecutarlo se te guardara una imagen en el escritorio donde te muestra la flag
## Comando(s) o payload clave (si aplica)

## Flag

picoCTF{now_you_know_about_extensions}

## ¿Que aprendí / qué usaría de nuevo?

## ¿Me trabé en algo? ¿Cómo lo destrabé?
