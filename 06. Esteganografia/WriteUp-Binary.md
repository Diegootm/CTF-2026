**Área:** Stego **Dificultad:** medio **Plataforma:**  CIDSI **Link del reto o Nombre:** Binary? **Resuelto por:** Xavi **Fecha:**19/09/2026  **Tiempo que tardé:** 30 min

---
**¿Qué pista/detalle me hizo saber por dónde ir?**  
El enunciado tenía dos pistas clave: _"No todo en la vida es binario... ¿o sí?"_ apuntaba a que en algún punto habría datos binarios (0s y 1s) que no debían leerse como texto ASCII directo, sino interpretarse de otra forma. Y el dato aparentemente aleatorio _"la raíz cuadrada de 1369 es 37"_ resultó ser la clave para saber que una cadena de 1369 bits debía acomodarse en una matriz cuadrada de 37×37 píxeles — que además coincide con el tamaño estándar de un QR versión 9.

**Herramienta(s) que usé**

- `exiftool` — revisar metadatos (sin resultado)
- `xxd` — inspección de bytes en crudo
- `binwalk` — buscar archivos embebidos (sin resultado)
- `steghide` + fuerza bruta (crackeador de diccionario) — extracción de datos esteganográficos
- `base64` — decodificación de la primera capa extraída
- Python + `Pillow (PIL)` — reconstrucción de imagen a partir de bits
- `zbarimg` — lectura del código QR generado
- Python (conversión binario → ASCII) — decodificación final del contenido del QR

**Pasos (solo lo esencial, tipo lista)**

- Inspeccioné el JPEG con `exiftool`, `xxd` y `binwalk` — sin hallazgos
- Probé `steghide` con la contraseña `37` (de la pista) — no funcionó
- Hice fuerza bruta con diccionario stegcrackersobre `steghide` → contraseña real: `meowmeow`
- Extraje el contenido oculto: era un string en base64, y el out del archivo
- Decodifiqué el base64 → mensaje pista indicando que había que seguir buscando, junto con una cadena larga de 1369 bits
- Confirmé que 1369 = 37² (cuadrado perfecto, coincidía con la pista matemática del enunciado)
- Mapeé los 1369 bits a una imagen de 37×37 píxeles (blanco/negro) con Python/PIL
- La imagen resultante mostraba un patrón con los 3 "ojos" característicos de un QR
- Escaneé con `zbarimg` (el celular no lo detectaba) → obtuve una nueva cadena binaria de 184 bits
- Convertí esa cadena binaria (bloques de 8 bits) a texto ASCII con Python

**Comando(s) o payload clave (si aplica)**

python: Para generar QR a partir de binarios 
```python
from PIL import Image

bits = "TU_CADENA_COMPLETA_AQUI"
size = 37

img = Image.new("1", (size, size))
pixels = img.load()

for i, bit in enumerate(bits):
    x = i % size
    y = i // size
    pixels[x, y] = 0 if bit == "1" else 1  # prueba también al revés si no se ve claro

img = img.resize((size*10, size*10), Image.NEAREST)  # la agrandas para verla bien
img.save("salida.png")
```


```bash
# Fuerza bruta sobre steghide
stegseek binary.jpg /usr/share/wordlists/rockyou.txt

# Extraer con la contraseña encontrada
steghide extract -sf binary.jpg -p meowmeow

# Decodificar base64
"sigueBuscando" 

# Generar imagen desde bits (Python)
python3 solve.py   # bits -> imagen 37x37 con PIL

# Leer el QR
zbarimg salida.png

# Decodificar binario final a ASCII (Python)
python3 decode_final.py
```

**Flag**

```
citc{w3lc0me_t0_r4cc0n_c1ty}
```

**¿Qué aprendí / qué usaría de nuevo?**

- Cuando el `strings` de un archivo no muestra nada legible, no significa que no haya nada oculto — hay que ir por capas: metadatos → EOF/binwalk → esteganografía con contraseña.
- Los "datos random" en el enunciado de un CTF casi nunca son decorativos — casi siempre son una clave, un tamaño, o un parámetro a usar más adelante.
- Una cadena larga de bits no siempre es texto ASCII directo: puede representar una imagen (mapeo bit→píxel), y reconocer que la longitud es un cuadrado perfecto fue la señal para pensarlo así.
- `zbarimg` es más confiable que el escáner de un celular para leer QRs generados a mano/imperfectos (sin quiet zone, orientación rara, etc.).

**¿Me trabé en algo? ¿Cómo lo destrabé?**

- El QR no escaneaba con el celular a pesar de verse bien — lo destrabé usando `zbarimg`, que sí logró leerlo aunque no tuviera quiet zone perfecto.