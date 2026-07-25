Área: Web Exploiting Dificultad: Fácil Plataforma: picoCTF Nombre: WebDecode Resuelto por: Diego Fecha: 25/07/2026 Tiempo que tardé: 15 minutos

## ¿Qué pista/detalle me hizo saber por dónde ir?

entre a la pagina y el pestania de about, e su contenido decia que inspecciones esa pagina y

## Herramienta(s) que usé

DevTools del navegador: application -> Frames -> about.html y section  y dCode para descifrar

## Pasos que segui para resolver el ejercicio

- primero revise todas la pestinias de la pagina
- en la pestania de about decia : try inspecting the page!! you might find ir there
- entonces inspecciones la pagina 
- DevTools del navegador: application -> Frames -> about.html y section 
- en el atributo se section en notify_true habia algo raro y era una encriptado en base64: cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfZGYwZGE3Mjd9
- lo lleve a dCode y lo descifre

## Comando(s) o payload clave (si aplica)



## Flag 

picoCTF{web_succ3ssfully_d3c0ded_df0da727}

## Qué aprendí / qué usaría de nuevo?

que siempre hay que darle una pequenia revision a los atributos de las etiquetas

## ¿Me trabé en algo? ¿Cómo lo destrabé?

