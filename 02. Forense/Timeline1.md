Writeup: [Timeline1 ]
Área: Forence Dificultad: Medio Plataforma: CyLab Link del reto o Nombre: [Timeline1]
Resuelto por: Axel Fecha:hoy Tiempo que tardé: unos 15 min

¿Qué pista/detalle me hizo saber por dónde ir?
que el ejerciio te daba un archivo y en las pistas te daba entender que ra una imagen y usando Sleuthkit y con otros comando llegabas a decifralo

Herramienta(s) que usé
1. Descomprimimos la imagen (gunzip)
2. Confirmamos que era un filesystem ext4 (fsstat)
3. Generamos el bodyfile con todos los metadatos, incluidos archivos borrados: fls -r -m / -o 0 partition4.img > bodyfile.txt
4. Convertimos eso en una línea de tiempo legible: mactime -b bodyfile.txt -d > timeline.csv
5. Miramos el final del timeline (fecha más reciente: 1 dic 2025) buscando archivos con macb (las 4 marcas de tiempo idénticas = señal de que el archivo se creó/tocó todo de una vez, típico de acciones antiforenses o archivos "plantados")
6. Encontramos /etc/chat — nombre fuera de lugar para /etc/ — con timestamps macb
Extrajimos su contenido con icat -o 0 partition4.img <inodo> → Base64 → decodificado → leetspeak → flag
Flag
picoCTF{573417h13r_7h4n_7h3_1457_58527bb222}

¿Qué aprendí / qué usaría de nuevo?
que hay varios comandos que te apresuran las cosas 

¿Me trabé en algo? ¿Cómo lo destrabé?
si al principio  no sabia como usar el archivo que me dieron los comando en si 
