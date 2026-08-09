# Writeup: [Registro de acertijos]

**Área:** Forence **Dificultad:** Fácil **Plataforma:** CyLab  **Link del reto o Nombre:** [Registro de acertijos]      
**Resuelto por:** Axel **Fecha:** No me acuerdo lo estoy subiendo recien hoy **Tiempo que tardé:** unos 10 min 

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El ejercicio te daba un documento pdf que era bait no te servia, habia que descifralo primero 

## Herramienta(s) que usé

- primero exiftool la mas ciomun pero no funca
- luego de buscar una que servia y es:  pypdf (la sucesora moderna de PyPDF2)
dcode.fr para descifrar el texto que estaba en base64:  '/Author': 'cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9jYTc2YmJiMn0='} 

## Pasos (solo lo esencial, tipo lista)
- darse cuenta que el pdf no te llevaba a nada y primero descifralo con la herramienta que si funcionaba 
- una ves echo eso te daba un cifrado en base 64, habia que descifrar eso mas y te daba la flag 
## Comando(s) o payload clave (si aplica)


## Flag

picoCTF{puzzl3d_m3tadata_f0und!_ca76bbb2}

## ¿Qué aprendí / qué usaría de nuevo?

que no solo te dbes fiar de lo que te dan 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

si al principio no te daba nada habia que descifralo primero
