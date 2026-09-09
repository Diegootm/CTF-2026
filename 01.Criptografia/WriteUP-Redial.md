
**Área:** Cripto **Dificultad:** dificil **Plataforma:** cidsi **Link del reto o Nombre:** redial**Resuelto por:** Diego **Fecha:** 23/07 **Tiempo que tardé:** ~1 hora

**Estado:** mensaje descifrado. Flag **no enviada** todavía por desconocer el formato.

**Mensaje obtenido:** `HAY QUE IR AL CONGRESO`

---

## 1. Reconocimiento inicial

Lo primero, siempre, antes de abrir nada en un editor de audio:

```bash
file redial.mp3
exiftool redial.mp3
binwalk redial.mp3
strings redial.mp3 | less
```

**Resultado:**

```
File Type       : MP3
Audio Bitrate   : 160 kbps
Sample Rate     : 44100
Channel Mode    : Stereo
Duration        : 23.07 s
Major Brand     : isom
Compatible Brands : isomiso2avc1mp41
Encoder Settings  : Lavf60.16.100
```

```
DECIMAL   HEXADECIMAL   DESCRIPTION
0         0x0           MP3 ID3 tag, v2.4
```

**Lectura de esto:**

- `binwalk` no encuentra nada embebido. Solo el tag ID3. No hay ZIP ni archivo oculto al final.
- `Lavf60.16.100` es la librería de **ffmpeg**. El archivo fue generado con ffmpeg.
- Los `Compatible Brands` (`isom`, `iso2`, `avc1`, `mp41`) son marcas del contenedor ISO Base Media, o sea MP4. Significa que **el original era un MP4 y lo convirtieron a MP3**. Los metadatos se arrastraron en la conversión.
- Nada de esto es la flag. Son rastros del proceso de creación del reto.

> **Falso positivo a evitar:** al pasar el archivo por CyberChef aparece la cadena `469;=ACEHJMPRTWZ]_adgilnptvx{}`. Parece codificada pero **no lo es**: es la tabla de contenido del header Xing/Info del MP3, un índice de posiciones para que el reproductor pueda saltar dentro del archivo. Los bytes van estrictamente en aumento, esa es la pista de que es estructura y no datos. Todo MP3 con VBR lo tiene.

---

## 2. La pista del nombre

`redial.mp3` — "remarcar", volver a marcar un número de teléfono.

Eso apunta a **DTMF** (Dual-Tone Multi-Frequency), los tonos del teclado telefónico. Con 23 segundos de duración encaja.

**Importante: esto fue una suposición, no una deducción.** Nada en los metadatos lo confirmaba. Había que verificarlo.

---

## 3. Verificación visual en Audacity

1. Abrir `redial.mp3` en Audacity
2. Clic en el nombre de la pista → **Spectrogram**
3. Clic en el nombre → **Preferencias de espectrograma**:
    - Scale: **Linear**
    - Window size: **4096**
    - Max Frequency: **1800 Hz**

**Lo que se ve:** dos rayas horizontales brillantes simultáneas en cada ráfaga, todas bajo 1700 Hz.

Esa es la firma inconfundible de DTMF. Cada tecla emite **dos** tonos a la vez:

||1209 Hz|1336 Hz|1477 Hz|
|---|---|---|---|
|**697 Hz**|1|2|3|
|**770 Hz**|4|5|6|
|**852 Hz**|7|8|9|
|**941 Hz**|*|0|#|

Se diseñó así a propósito: la combinación de dos tonos no puede ser imitada por la voz ni por ruido aleatorio, por eso funciona sobre una línea telefónica sin activar teclas por accidente.

---

## 4. Los decodificadores estándar fallan (y por qué)

Se probaron cuatro. **Todos dieron resultados distintos.**

### multimon-ng

```bash
sudo apt install libsox-fmt-mp3 multimon-ng sox
sox redial.mp3 -t raw -r 22050 -e signed -b 16 -c 1 - | multimon-ng -t raw -a DTMF -
```

Salida: 31 tonos → `4290783047025550266664777377666`

### ribt/dtmf-decoder

```bash
git clone https://github.com/ribt/dtmf-decoder.git
cd dtmf-decoder/
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip setuptools wheel
pip install numpy scipy matplotlib
sox ../redial.mp3 redial.wav
python3 dtmf.py -v -i 0.05 -l redial.wav
```

> **Nota:** el `requirements.txt` del repo pide `numpy~=1.19.4`, que no compila en Python 3.13. Hay que ignorarlo e instalar numpy actual. En Debian también hay que usar venv por el bloqueo PEP 668 (`externally-managed-environment`).

Con distintos parámetros salían distintos resultados:

|Parámetros|Tonos detectados|Silencios|
|---|---|---|
|`-i 0.02`|~12|muchos|
|`-i 0.05 -l`|~23|casi ninguno|
|`-i 0.10`|~23|**ninguno**|

### La contradicción que resolvió el caso

En el segundo `0:02`, `-i 0.02` reportaba silencio absoluto y `-i 0.10` reportaba un `9` continuo. **No pueden tener razón los dos.**

Y `-i 0.10` no detectó ni un solo instante de silencio en 23 segundos, cuando el espectrograma mostraba huecos claros.

Eso apuntaba a que el problema no era la herramienta sino una suposición del formato.

---

## 5. Análisis espectral crudo — aquí está la clave

En vez de confiar en un decodificador, mirar directamente qué frecuencias dominan en cada instante.

**Script `peaks.py`:**

```python
import numpy as np
from scipy.io import wavfile

rate, data = wavfile.read('redial.wav')
if data.ndim > 1:
    data = data.mean(axis=1)
data = data.astype(np.float64)
data /= np.max(np.abs(data))

wlen = 4096
hop  = int(0.020 * rate)
freqs = np.fft.rfftfreq(wlen, 1/rate)
mask = (freqs > 500) & (freqs < 1800)
fsel = freqs[mask]

t = 0
while t + wlen < len(data):
    seg = data[t:t+wlen] * np.hanning(wlen)
    sp = np.abs(np.fft.rfft(seg))[mask]
    idx = np.argsort(sp)[::-1]
    picked = []
    for i in idx:
        if all(abs(fsel[i]-fsel[j]) > 60 for j in picked):
            picked.append(i)
        if len(picked) == 4:
            break
    picked.sort(key=lambda i: fsel[i])
    print("%6.2f  %s" % (t/rate,
        "  ".join("%4dHz(%.2f)" % (round(fsel[i]), sp[i]) for i in picked)))
    t += hop
```

**Salida (extracto):**

```
  0.00   770Hz(487.79)   910Hz(0.92)  1147Hz(1.35)  1211Hz(437.24)
  0.50   770Hz(490.48)   910Hz(0.96)  1147Hz(1.35)  1211Hz(437.19)
  0.90   694Hz(190.94)   770Hz(288.59)  1211Hz(261.55)  1335Hz(185.62)
  1.00   694Hz(419.46)   759Hz(0.74)  1335Hz(479.26)  1421Hz(1.78)
  1.50   851Hz(458.96)  1405Hz(1.30)  1475Hz(445.88)  1550Hz(0.93)
  3.00   942Hz(475.95)  1254Hz(2.17)  1335Hz(478.05)  1405Hz(1.50)
```

### El hallazgo

**Los tonos son continuos. No hay silencio entre ellos.**

Cada tono dura ~470 ms y encadena directo con el siguiente. En `0.90` se ve la transición: las cuatro frecuencias aparecen mezcladas mientras un tono muere y otro nace.

Todos los decodificadores estándar buscan silencios para separar pulsaciones. Aquí no hay ninguno, así que cada uno inventaba su propia segmentación.

Lo que sí hay son **caídas breves de amplitud** en cada transición, y esas sí se pueden detectar.

---

## 6. Decodificador propio

**Script `final.py`** — clasifica cada frame por frecuencia y segmenta por caídas de amplitud en vez de por silencio:

```python
import numpy as np
from scipy.io import wavfile

rate, data = wavfile.read('redial.wav')
if data.ndim > 1:
    data = data.mean(axis=1)
data = data.astype(np.float64)
data /= np.max(np.abs(data))

LOW  = [697, 770, 852, 941]
HIGH = [1209, 1336, 1477, 1633]
KEYS = [['1','2','3','A'],
        ['4','5','6','B'],
        ['7','8','9','C'],
        ['*','0','#','D']]

wlen = 2048
hop  = int(0.005 * rate)          # 5 ms
freqs = np.fft.rfftfreq(wlen, 1/rate)
bin_of = lambda f: int(np.argmin(np.abs(freqs - f)))
lo_bins = [bin_of(f) for f in LOW]
hi_bins = [bin_of(f) for f in HIGH]

times, digs, amps = [], [], []
t = 0
while t + wlen < len(data):
    seg = data[t:t+wlen] * np.hanning(wlen)
    sp = np.abs(np.fft.rfft(seg))
    lo = np.array([sp[b] for b in lo_bins])
    hi = np.array([sp[b] for b in hi_bins])
    r, c = int(np.argmax(lo)), int(np.argmax(hi))
    times.append(t/rate); digs.append(KEYS[r][c]); amps.append(lo[r] + hi[c])
    t += hop

times = np.array(times); amps = np.array(amps); digs = np.array(digs)
dip = amps < 0.75 * np.median(amps)      # caida = separacion entre pulsaciones

press = []
cur_d, cur_start = digs[0], times[0]
i = 1
while i < len(digs):
    if digs[i] != cur_d:
        press.append((cur_d, cur_start, times[i]))
        cur_d, cur_start = digs[i], times[i]; i += 1
    elif dip[i]:
        j = i
        while j < len(digs) and dip[j]:
            j += 1
        if times[j-1] - times[i] >= 0.010 and times[i] - cur_start > 0.05:
            press.append((cur_d, cur_start, times[i]))
            cur_start = times[j] if j < len(times) else times[-1]
        i = j
    else:
        i += 1
press.append((cur_d, cur_start, times[-1]))
press = [p for p in press if p[2]-p[1] > 0.05]

for n,(d,a,b) in enumerate(press,1):
    print("%3d %8.3f %8.3f %8.1fms   %s" % (n,a,b,(b-a)*1000,d))
print("\nSecuencia:", ''.join(d for d,a,b in press))
```

**Salida — 35 pulsaciones:**

```
  #   inicio      fin  dur(ms)  tecla
  1    0.000    0.444    444.0    4
  2    0.469    0.943    473.9    4
  3    0.973    1.447    473.9    2
  4    1.472    2.948   1476.6    9
  5    2.973    3.447    473.9    0
  6    3.477    3.951    473.9    7
  7    3.976    4.455    478.9    7
  8    4.475    4.954    478.9    8
  9    4.974    5.453    478.9    8
 10    5.478    5.907    429.0    3
 11    5.932    6.405    473.9    3
 12    6.430    6.904    473.9    0
 13    6.934    7.408    473.9    4
 14    7.433    7.907    473.9    4
 15    7.937    8.406    468.9    4
 16    8.441    8.910    468.9    7
 17    8.940    9.409    468.9    7
 18    9.439    9.912    473.9    7
 19    9.937   10.411    473.9    0
 20   10.441   10.865    424.0    2
 21   10.895   12.437   1541.5    5
 22   12.472   12.941    468.9    0
 23   12.971   13.444    473.9    2
 24   13.469   14.397    927.9    2
 25   14.422   15.345    922.9    6
 26   15.380   15.794    414.1    6
 27   15.834   16.747    912.9    6
 28   16.782   17.221    439.0    4
 29   17.256   17.695    439.0    7
 30   17.735   18.174    439.0    7
 31   18.209   18.658    449.0    7
 32   18.683   19.605    922.9    3
 33   19.630   20.977   1346.9    7
 34   21.017   21.461    444.0    7
 35   21.486   22.918   1431.7    6
```

### Cuantización

Las duraciones son múltiplos exactos de **~470 ms**:

- 424–479 ms → **1 pulsación**
- 913–928 ms → **2 pulsaciones**
- 1347–1542 ms → **3 pulsaciones**

Ahí está la información. **No es DTMF simple, es multi-tap** (T9 clásico): el número de toques sobre una tecla determina qué letra de su grupo se elige.

---

## 7. Decodificación multi-tap

|Tecla|Letras|
|---|---|
|2|A B C|
|3|D E F|
|4|G H I|
|5|J K L|
|6|M N O|
|7|P Q R S|
|8|T U V|
|9|W X Y Z|

El **`0` funciona como espacio** entre palabras.

Secuencia normalizada: `44 2 999 · 0 · 77 88 33 · 0 · 444 777 · 0 · 2 555 · 0 · 222 666 66 4 777 33 7777 666`

|Pulsaciones|Letra|Palabra|
|---|---|---|
|4×2, 2×1, 9×3|H, A, Y|**HAY**|
|7×2, 8×2, 3×2|Q, U, E|**QUE**|
|4×3, 7×3|I, R|**IR**|
|2×1, 5×3|A, L|**AL**|
|2×3, 6×3, 6×2, 4×1, 7×3, 3×2, 7×4, 6×3|C,O,N,G,R,E,S,O|**CONGRESO**|

## `HAY QUE IR AL CONGRESO`

El mensaje encaja con el contexto: **CITC = Congreso Internacional de Tecnología y Ciberseguridad**, el evento de AGETIC donde se celebra la competencia.

---

## 8. Pendiente: el formato de la flag

**Tenemos la frase pero no hemos podido enviar la flag porque no sabemos el formato esperado.**

Variantes a probar:

```
HAY_QUE_IR_AL_CONGRESO
HAYQUEIRALCONGRESO
hayqueiralcongreso
```

**Cómo resolverlo:** mirar el formato de las flags de otros retos ya resueltos en la misma plataforma y replicarlo exactamente.

### Dudas abiertas

1. **El grupo del `6`.** Se midieron 5 pulsaciones consecutivas y se dividieron como 3+2 (O+N) porque encajaba con "CONGRESO". Otra división daría otra palabra. La lectura es coherente pero no está verificada de forma independiente.
    
2. **¿El mensaje es la flag o una instrucción?** "Hay que ir al congreso" podría ser una pista que remite a otro recurso (la web del CITC, otra parte del reto) donde estaría la flag real. Un reto de 600 puntos rara vez termina en una frase suelta.
    
3. **Comprobaciones que faltan:**
    
    ```bash
    strings redial.mp3 | grep -iE "flag|agetic|citc|\{"
    exiftool -a -u -g1 redial.mp3
    binwalk -e redial.mp3
    ```
    

---

## 9. Herramientas usadas

|Herramienta|Para qué|Instalación|
|---|---|---|
|**exiftool**|metadatos|`sudo apt install libimage-exiftool-perl`|
|**binwalk**|archivos embebidos|`sudo apt install binwalk`|
|**Audacity**|espectrograma, confirmación visual|`sudo apt install audacity`|
|**sox** + `libsox-fmt-mp3`|conversión de audio|`sudo apt install sox libsox-fmt-all`|
|**ffmpeg**|conversión MP3 → WAV|`sudo apt install ffmpeg`|
|**multimon-ng**|decodificador DTMF (falló)|`sudo apt install multimon-ng`|
|**ribt/dtmf-decoder**|decodificador DTMF con timeline (falló)|`git clone https://github.com/ribt/dtmf-decoder.git`|
|**dCode DTMF**|decodificador web|https://www.dcode.fr/dtmf-code|
|**Python + numpy/scipy**|análisis espectral propio|`pip install numpy scipy`|

**Lo que resolvió el reto fue el script propio, no ninguna herramienta de terceros.**

Setup completo para retos de audio:

```bash
sudo apt install ffmpeg sox libsox-fmt-all audacity binwalk \
                 multimon-ng libimage-exiftool-perl python3-full
```

---

## 10. Lecciones

1. **Cuando varias herramientas dan respuestas distintas, el problema no es la herramienta.** Es una suposición del formato que ninguna cumple. Aquí: los decodificadores DTMF asumen silencio entre tonos, y este audio no lo tenía.
    
2. **Mirar los datos crudos antes que probar más herramientas.** Media hora de análisis espectral valió más que cuatro decodificadores con parámetros distintos.
    
3. **El nombre del archivo es una pista.** `redial` → teléfono → DTMF. Pero es hipótesis, hay que confirmarla contra el espectrograma.
    
4. **La duración de los tonos puede ser información.** Aquí la cuantización en múltiplos de 470 ms era el mensaje entero. Un decodificador que solo reporta dígitos la descarta.
    
5. **Ojo con las conversiones que pierden datos.** Al pasar los números por un conversor ASCII, los valores de tres dígitos (443–447) se truncaron a `,` y se perdieron cinco símbolos distintos.