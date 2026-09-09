
**Área:** Explotation web **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Insp3ct0r **Resuelto por:** Xavi **Fecha:** 27/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado del ejercicio me decia que revise especificamente el codigo de la pagina luego cuando entre a la pagina se me decia que uso Html, css, y java script entonces era claro que habia que buscar en el codigo
## Herramienta(s) que usé

El inspector de la pagina 

## Pasos (solo lo esencial, tipo lista)

- Primero revise el html y encontre al final del dcodigo una tercia parte de la flag que decia que faltaban dos partes mas 
- Revise el css y encontre la segunda parte de la flag entonces asumi que lo ultimo estaria en javascript que asi fue 
- Entre a la parte de Red y filtre los archivos de jss donde encontre el  codigo de javascript y la ultima parte de la flag 
## Comando(s) o payload clave (si aplica)

```
```

## Flag

picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?302945a7}

## ¿Que aprendí / qué usaría de nuevo?

Pues solo revisar bien el codigo ya que se encuentran pistas casi siempre en el codigo de la pagina 
## ¿Me trabé en algo? ¿Cómo lo destrabé?

solo inspector de la pagina, no me trabe