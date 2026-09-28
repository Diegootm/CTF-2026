
**Área:** Forense **Dificultad:** Medio **Plataforma:** CITC **Link del reto o Nombre:** los 5 secretos de juan **Resuelto por:** Xavi **Fecha:** 27/09 **Tiempo que tardé:** 1 hour

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

Bueno al inicio decia que a Juan le gustaba hacer combinaciones insipirado en su numero favorito que ees el cinco con ello ya me fui guiando durante todo el ejercicio
## Herramienta(s) que usé

exiftool 
binwalk
7z e 
file
xxd
stegseek
seteegcracker
jhon
## Pasos (solo lo esencial, tipo lista)

- primero para extraer el zip uve qu usar la herramienta de jhon para sacar el hash de la contraseña y despues use un ddiccionario qde este mismo que es rockyou.txt
- COn este encontre la contraseña que es luego de descifrarlo entregaban 5 archivos txt
- Al ir revisando cada archivo cada uno tenia una flag que era falsa entonces me di cuejta que solo uno no tenia una contraseña falsa y era el secret4.txt
- AL revisar el archivo secret4.txt me di cuenta que era un jpg y era un snoopy el cual no tenia nada que ver con la contraseña
- Probe con stegcracker con el diccionario de rockoytxt con caracteres de 5 pero ni aun asi, no encontre nada
- luego tuve que generear con crunch todas las posibles combinaciones de 5 caracteres y probar con steegseek que era mucho mas rapido asi hallando la contraseña y la flag dentro del archivo devuelto
## Comando(s) o payload clave (si aplica)

```
```bash
crunch 5 5 abcdefghijklmnopqrstuvwxyz -o combos5.txt

stegseek secret4.txt combos5.txt   //Para probar
```

- `5 5` = longitud mínima y máxima, ambas 5
- `abcdefghijklmnopqrstuvwxyz` = el set de caracteres a usar
- `-o combos5.txt` = archivo de salida

## Flag

picoCTF{Th3_Sh4m4n_s3cr3t_fl@g}

## ¿Que aprendí / qué usaría de nuevo?

Que hay que seguir buscando a pesar de encontrar pistas falsas que incluso a veces son pistas para poder hallar la flag verdader 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

pues estaba analizando todos los archivos y pense que podria encontrar algo especialmente en el secret5.txt pero no encontre nada entonces me di cuenta que lo unico que debia hallar era la contraseña para aplicar steghide al archivo de secret4.jpg y solo asi halle la flag 