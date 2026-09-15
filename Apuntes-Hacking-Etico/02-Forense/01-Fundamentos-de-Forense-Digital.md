# Fundamentos de Forense Digital

## ¿Qué es el análisis forense digital?
Es la disciplina de **recuperar y analizar rastros digitales** dejados en un sistema — archivos borrados, datos ocultos, registros de actividad — para reconstruir qué pasó.

## Forense en CTF vs. forense en la vida real
A diferencia de los CTF, el forense del mundo real rara vez es un "acertijo": por ejemplo, reensamblar particiones de un disco duro dañado para recuperar su sistema de archivos. Los retos de CTF, en cambio, suelen ser **problemas ingeniosos** diseñados para enseñarte una herramienta o técnica puntual, con la flag escondida en el lugar menos obvio.

## Mentalidad para resolver forense en CTF
- **Aborden cada reto con mente abierta.** No es raro que la flag esté en un lugar inusual: dentro de metadatos, en un comentario de código, en la marca de tiempo de un archivo, en un paquete de red perdido entre miles.
- **Revisen SIEMPRE lo básico primero**, en este orden:
  1. `file` — ¿qué tipo de archivo es realmente?
  2. `exiftool` — ¿qué metadatos tiene?
  3. `strings` — ¿hay texto legible escondido?
  4. Si es una imagen de disco o memoria, recién ahí usar herramientas más pesadas (Autopsy, Volatility).
- **La herramienta correcta importa tanto como saber usarla.** Vale la pena invertir tiempo en aprender bien 3-4 herramientas (ver [07-Herramientas](Apuntes-Hacking-Etico/02-Forense/07-Herramientas.md)) en vez de conocer superficialmente 20.

## Los 3 pilares que vamos a cubrir
1. **Archivos**: identificarlos correctamente y extraer sus metadatos (archivos [02](02-Firmas-y-Formatos-de-Archivo.md) y [03](03-Metadatos.md)).
2. **Tiempo**: entender qué cuentan las marcas de tiempo de un archivo o sistema (archivo [04](04-Timestamps-y-Lineas-de-Tiempo.md)).
3. **Red y memoria**: analizar tráfico capturado o el estado de la RAM de una máquina (archivos [05](05-Forense-de-Red-Wireshark.md) y [06](06-Forense-de-Memoria-RAM.md)).
