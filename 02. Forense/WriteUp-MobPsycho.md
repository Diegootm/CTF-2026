**Área:** Forense **Dificultad:** - **Plataforma:** cylab **Link del reto o Nombre:** Cylab **Resuelto por:** Diego **Fecha:** 14/09 **Tiempo que tardé:** 10 minutos

---

## ¿Qué pista/detalle me hizo saber por dónde ir?
cuando dijo un apk, investigue como se debe manipular e investigar un apk

## Herramienta(s) que usé

- unzip
- decode

## Pasos (solo lo esencial, tipo lista)

Empieza identificando el archivo con `file mobpsycho.apk`, que devuelve `Zip archive` — el dato clave, porque un APK es un ZIP. Listaste el contenido sin extraer y lo filtraste con `unzip -l mobpsycho.apk | grep -i flag`, lo que reveló de inmediato `res/color/flag.txt`, un archivo que no encaja entre los recursos normales de la app. Lo extrajiste y leíste con `cat`, encontrando una cadena hexadecimal. La decodificaste de hex a ASCII y obtuviste la flag. Menciona la pista del reto ("can you handle apks?") porque confirma que el desafío era saber tratar el APK como un contenedor, no descifrar algo complejo.

## Comando(s) o payload clave (si aplica)

```bash
unzip -l mobpsycho.apk | grep -i flag
```

## Flag

picoCTF{ax8mC0RU6ve_NX85l4ax8mCl_a3eb5ac2}

## ¿Qué aprendí / qué usaría de nuevo?

**un APK es simplemente un archivo ZIP con una estructura estándar de Android**. No necesitas herramientas especiales para abrirlo, `unzip` basta. Eso cambia todo el enfoque.

Dentro de esa estructura, estos son los sitios que hay que revisar y por qué:

La carpeta **`res/`** guarda los recursos: imágenes, layouts, definiciones de color. Es enorme y casi todo es relleno generado automáticamente. Justo por eso es un escondite perfecto — un archivo que no encaja, como un `flag.txt` entre archivos de color, salta a la vista si sabes filtrar. Aquí estaba tu flag.

La carpeta **`assets/`** contiene archivos crudos que el desarrollador incluyó a mano. Es el primer lugar donde mirar, porque nada llega ahí por defecto: todo lo que haya fue puesto a propósito.

Los archivos **`classes.dex`** (y `classes2.dex`, etc.) son el código de la app compilado a bytecode Dalvik. Si la flag no está suelta como archivo sino construida por el código, hay que decompilar estos con **jadx**, que los convierte a Java legible, o con **apktool**. Este es el paso para retos más difíciles.

El **`AndroidManifest.xml`** es la configuración de la app: permisos, componentes, actividades. Puede revelar actividades ocultas o dar pistas de la estructura.

El **`resources.arsc`** son los recursos compilados, incluidas las cadenas de texto. Un `strings resources.arsc | grep -i flag` a veces encuentra la flag directamente.

Y la técnica transversal, sirve para cualquier archivo grande: **no leas la salida a mano, fíltrala**. `unzip -l` para listar sin extraer, encadenado con `grep -i` para buscar sin importar mayúsculas, o `grep -v` para descartar el ruido conocido. Esa es la diferencia entre encontrar la flag en dos segundos o perderte en 700 líneas.

## ¿Me trabé en algo? ¿Cómo lo destrabé?
no

---

