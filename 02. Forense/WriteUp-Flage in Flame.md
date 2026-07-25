
**Área:** Forense **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Mod26 **Resuelto por:** Xavi **Fecha:** 23/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

cuando vi el base64 tan largo me desconcerte pero reconoci que era un base 64 una vez decodificado me dio una imagen, cuando la obtuve ya sabia que hacer...
## Herramienta(s) que usé

exiftool (en una imagen donde estaba la clave)
tesseract
## Pasos (solo lo esencial, tipo lista)

- decodicar el archivo logs.txt devolviendo una imagen 
- Usar la herramienta de tesseract para hallar el texto escondido dentro de la imagen 
- encontre 7069636F4354467B666F72656E736963735F 616E616C797369735F69735F61 6D617A696E675F35636363376362307D que es la flag codificado en ASCCI code 
## Comando(s) o payload clave (si aplica)

```
base64 -d logs.txt > salida //Para transformar todo ese texto en una imagen


tesseract imagen.png resultado    //para que me devuelva el txt de la imagen encontrado 
```

## Flag

picoCTF{forensics_analysis_is_amazing_5ccc7cb0}

## ¿Que aprendí / qué usaría de nuevo?

que tesseract es una herramienta que busca texto dentro de una imagen para extraerlos 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

pues por mas que buscaba pistas o la flag no encontraba nada hasta que descubri la herramienta tesseract con la cual pude encontrar rapidamente la flag escondida 