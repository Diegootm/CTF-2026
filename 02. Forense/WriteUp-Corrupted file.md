**Área:** Forense **Dificultad:** Fácil  **Plataforma:** [CyLab  **Link del reto o Nombre:** [Glory of the garden]      **Resuelto por:** [Diego] **Fecha:** [24/07/2026] **Tiempo que tardé:** [ej. 8min] 

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

decia que un par de bytes podia marcar la diferencia

## Herramienta(s) que usé

use xxd para ver los hexagecimales

## Pasos (solo lo esencial, tipo lista)

- me descargue el archivo 
- luego analise la cabecera de los hexagesimales para saber que tipo de archivo era por que no decia al fijarme el patro coincidia con el de un jpeg  entonces con un  `xxd file > file.hex` 
- lo converti en un archivo .hex luego lo edite con nano
- los primeros dos pares de la cabecera estaban mal, en vez de 5c78 era ffd8
- finalmente hice un `xxd -r file.hex > file_fixed.jpg`
- la flag estaba al ver la imagen
- 
## Comando(s) o payload clave (si aplica)


## Flag

picoCTF{r3st0r1ng_th3_by73s_2326ca93}

## ¿Qué aprendí / qué usaría de nuevo?



## ¿Me trabé en algo? ¿Cómo lo destrabé?

