**Área:** Miscelanea **Dificultad:** - **Plataforma:** SCESI-competencia **Link del reto o Nombre:** Oculto(100 pts) **Resuelto por:** Axel **Fecha:** Dia de la competencia **Tiempo que tardé:**  -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?
Una vez descifrabas el archivo que te daban: https://sharebin.eu/embed/6d483bed64d1ccf8f8545cc28a06b8b8 te daba una imagen de una cadena en Base64  

## Herramienta(s) que usé
1. Primero se analiza el archivo descargado con:

```
file 6d483bed64d1ccf8f8545cc28a06b8b8
```

El resultado indica que es texto ASCII con una línea muy larga, por lo que no es directamente una imagen.

2. Al revisar el contenido:

```
strings 6d483bed64d1ccf8f8545cc28a06b8b8
```

se observa una cadena enorme similar a:

```
hVVORw0KGgoAAAANSUhEUg...
```

Esto tiene apariencia de datos binarios codificados en **Base64**.

3. Se decodifica:

```
base64 -d 6d483bed64d1ccf8f8545cc28a06b8b8 > imagen.png
```

Pero al ejecutar:

```
file imagen.png
```

no es reconocida correctamente como PNG.

4. Revisando los primeros bytes del archivo decodificado se obtiene:

```
85 55 4E 47 0D 0A 1A 0A
```

La cabecera correcta de un PNG debería ser:

```
89 50 4E 47 0D 0A 1A 0A
```

Por tanto, los primeros bytes fueron modificados intencionalmente.

La firma hexadecimal estándar de PNG es:

```
89 50 4E 47 0D 0A 1A 0A
```

5. Se pueden corregir esos primeros 8 bytes con Python:

```
from pathlib import Path

archivo = Path("imagen.png")

datos = bytearray(archivo.read_bytes())

datos[:8] = bytes.fromhex(
    "89504e470d0a1a0a"
)

Path("imagen_reparada.png").write_bytes(datos)
```

Luego:

```
file imagen_reparada.png
```

ya devuelve algo como:

```
PNG image data, 415 x 304, 8-bit/color RGB
```

6. Al abrir la imagen reparada aparece directamente el texto:

```
UMSSCTF{
imagen_si
mple_2709}
```
## Pasos (solo lo esencial, tipo lista)

- El ejercicio te dab un archivo el cual El reto ocultaba una imagen PNG dentro de una cadena Base64. 
- Después de decodificarla, la imagen seguía sin abrir porque su **magic number o firma de archivo había sido alterada**. 
- Comparando la cabecera con la firma estándar de PNG y reparando los primeros bytes, fue posible visualizar la imagen que contenía la bandera.
## Comando(s) o payload clave (si aplica)

## Flag

umssctf{imagen_simple_2709}

## ¿Qué aprendí / qué usaría de nuevo?


## ¿Me trabé en algo? ¿Cómo lo destrabé?


---
