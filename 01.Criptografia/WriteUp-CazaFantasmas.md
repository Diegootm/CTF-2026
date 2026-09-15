**Área:** Cripto **Dificultad:** medio **Plataforma:** citc **Link del reto o Nombre:** cazaFantasmas **Resuelto por:** Xavi **Fecha:** 14/09 **Tiempo que tardé:** ~1 hora

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

el enunciado decia que se daba un audio el cual se escuchaba embrujado de derecho y reveresa entonces asumi que habia que meterle reversa para encontrrar algo y asi como fue encontre algo ademas que decia viernes 13 entonces ya pude pensar en rot13 para resolver  

## Herramienta(s) que usé

Un desencriptador de morse online  https://morsecw.com/decodificador.html
tambien ciberchef para el rot13
dCode para pasarlo a AScii

## Pasos (solo lo esencial, tipo lista)

- Descargue el archvio y estuve bastante tiempo analizando el codigo morse y intentando descifrar hasta que entendi que el audio debia ser revertido
- Despues de revertirlo habia que usar rot13 pero  yo no encontre nada ya que aun habia que pasarlo a sistema ASCII fue entonces que despues de varios intentos pude hallar la flag  
## Comando(s) o payload clave (si aplica)


## Flag

picoCTF{un4_id4_y_un4_vuelt4}

## ¿Qué aprendí / qué usaría de nuevo?

Audacity par poder analizr y escuchar bien el audio ya que al inicio el reproductor no emitia ningun sonido por lo cual estuve viendo igual el spectrograma pero no habia nada pues no se habia escondido nada alli

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Despues de tantos intentos solo puse en ciberchef el codigo morse revertido y para decodificar from codigo morse y rot13 debajo despues pase a dcode y entonces me dijo ASCII y pude recien encontrar la flag tan esperada 

