**Área:** Explotation web **Dificultad:** Medio **Plataforma:** picoCTF  (vía CyLab Academy) **Link del reto o Nombre:** Secretos **Resuelto por:** Axel**Fecha:** 13/09 **Tiempo que tardé:** ~25 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El ejercicio te da una instancia que seria esta: http://saturn.picoctf.net:61830/  pero eso no te ayuyda en nada como dice el nombre de ste ejercicio hay secretos ocultos y gracias a una pista que decia carpeta,carpeta,carpeta lo que deduciendo hay cosas ocultas en varias carpetaslo cual para buscarlo oprimir ctrl + u y buscar ahi al revisar detenidamente hay una parte que dice secret el cual en el buscador poner: http://saturn.picoctf.net:61830/secret/ y de la misma forma con ctrl + u y d ela misma forma hay una parte que dice hidden y de la misma forma: http://saturn.picoctf.net:61830/secret/hidden y ahi aparece algo importante y clave paar encontrar la flag con: http://saturn.picoctf.net:61830/secret/hidden/superhidden/ y ya te muestra la flag en pantalla   
## Herramienta(s) que usé

El inspector de la pagina,  ctrl + u 

## Pasos (solo lo esencial, tipo lista)

- primero con la instancia buscar carpetas ocultas se encuentra la primera con ctrl+u es secret
- Una ves con secret igual con ctrl + u buscar la otra carpeta que es hidden y de ahi estamos cerca d ela flag 
- Ya con hidden se ve que no te muestra nada pero con el superhidden entras a una parte que una ves le das ctrl+u te muestra la flag
## Comando(s) o payload clave (si aplica)

## Flag

picoCTF{succ3ss_@h3n1c@10n_790d2615}

## ¿Que aprendí / qué usaría de nuevo?
que hay que buscar bien por todo lado mas si es un ejercicio de web
## ¿Me trabé en algo? ¿Cómo lo destrabé?

si al principio no sabia que buscvar con la pista se hizo mas facil