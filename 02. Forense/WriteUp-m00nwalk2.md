Área: Forense Dificultad: Alta Plataforma: CIDSI  Link del reto o Nombre: m00nwalk2 Resuelto por: Axel Fecha: 23/09 Tiempo que tardé: ~30 min
**¿Qué pista/detalle me hizo saber por dónde ir?** 
El enunciado decía: "Revise la última transmisión. Pensamos que contiene un mensaje oculto" + "hay algunas pistas (clue1, clue2, clue3)" + el consejo "Use las pistas para extraer **otra** bandera del .wav". La palabra clave era "otra": eso me avisó que decodificar el `message.wav` como SSTV (que da una imagen con el texto `CTF{beep_boop_im_in_space}`) **no era la flag de este reto** — esa imagen es la flag de un reto previo ("m00nwalk" parte 1). El verdadero objetivo era usar los 3 archivos `clue*.wav` para conseguir la información necesaria y extraer una segunda flag escondida dentro del propio `message.wav`.

**Herramienta(s) que usé**  
`sstv` (decodificador Python de SSTV), `steghide`, análisis de espectrograma con `scipy`/`matplotlib`.

**Pasos (solo lo esencial, tipo lista)**

- Analicé el espectrograma de `message.wav`: la señal de sincronismo constante ~1500 Hz y la banda de "video" 1500–2300 Hz son la firma clásica de una transmisión **SSTV**.
- Decodifiqué `message.wav` con un decodificador SSTV → detectó automáticamente modo **Scottie 1** → salió una imagen (rotada 180°) con el texto `CTF{beep_boop_im_in_space}`. Confirmé que esta es la flag de otro reto, no la de este.
- Decodifiqué los 3 `clue*.wav` con el mismo método (cada uno en su propio modo SSTV: Martin 1, Scottie 2 y Martin 2) → cada uno reveló una imagen con una pista de texto:
    - Clue 1 → contraseña: `hidden_stegosaurus`
    - Clue 2 → pista conceptual: "The quieter you are the more you can HEAR" (apunta a esteganografía en audio)
    - Clue 3 → nombre propio: "Alan Eliasen the FutureBoy" (su sitio web trata sobre herramientas de esteganografía, y su descripción coincide con el funcionamiento de `steghide`)
- Con la contraseña obtenida del Clue 1, corrí `steghide` directamente sobre `message.wav` → extrajo un archivo de texto oculto (`steganopayload12154.txt`) con la flag real.

**Comando(s) o payload clave (si aplica)**

bash

```bash
steghide extract -sf message.wav -p hidden_stegosaurus
cat steganopayload12154.txt
```

**Flag**  
`picoCTF{the_answer_lies_hidden_in_plain_sight}`

**¿Qué aprendí / qué usaría de nuevo?**  
Cuando un reto de audio trae "pistas" adicionales aparte del archivo principal, no asumir que cada pista es solo "otra imagen SSTV más" — a veces son literalmente instrucciones/contraseña en texto plano para un segundo paso de esteganografía (`steghide`) sobre el archivo original. `steghide` puede ocultar datos arbitrarios dentro de archivos de audio/imagen sin alterar visiblemente el contenido "de portada" (en este caso, sin afectar la señal SSTV que ya decodificaba a una imagen "señuelo"). Vale la pena recordar: `steghide extract -sf <archivo> -p <password>` no requiere saber el nombre del archivo oculto de antemano, solo la contraseña correcta.

**¿Me trabé en algo? ¿Cómo lo destrabé?**  
Sí — inicialmente reporté como flag el texto visible en la imagen SSTV decodificada (`CTF{beep_boop_im_in_space}` / `picoCTF{beep_boop_im_in_space}`), que resultó incorrecto porque pertenece a otro reto (m00nwalk 1). Me destrabé releyendo el enunciado ("extraer **otra** bandera") y decodificando los 3 clues como imágenes SSTV independientes en vez de tratarlos como ruido o variantes del mismo mensaje; eso reveló la contraseña y la herramienta correctas (`steghide` + `hidden_stegosaurus`) para sacar la flag real oculta dentro del wav.