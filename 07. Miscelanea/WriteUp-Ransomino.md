**Área:** Miscelanea**Dificultad:** MUY DIFICIL (300 pts) **Plataforma:** CIDSI 
**Link del reto o Nombre:** Ransomino  **Resuelto por:** Axel  
**Fecha:** 28/09 **Tiempo que tardé:** UN CHINGO

### ¿Qué pista/detalle me hizo saber por dónde ir?

El archivo `Ransomino` es una sola línea de 6774 caracteres alfanuméricos, y en toda la cadena no aparece ningún `0`, `O`, `I` ni `l`. Esa ausencia es la firma de **Base58**. El enunciado dice "archivo codificado" y pide una ciudad, así que la idea era decodificarlo y buscar algo que diera una ubicación.

### Herramienta(s) que usé

- `file` para identificar el tipo de archivo.
- Python para decodificar Base58 con distintos alfabetos y de hex a bytes.
- Navegador para consultar la API pública de ThingSpeak.
- `md5sum` para el hash final.

### Pasos

1. **Identificar el formato.** `file` dice "ASCII text, very long lines". Al ver que faltan `0`, `O`, `I` y `l`, pensé en Base58.
2. **Decodificar con el alfabeto de Bitcoin.** El resultado (4961 bytes) tenía entropía 7.97, es decir, era casi aleatorio, así que no era el alfabeto correcto.
3. **Probar otros alfabetos Base58.** Con Flickr también salió ruido. Con el de **Ripple** (`rpshnaf39wBUDNEGHJKLM4PQRST7VWXYZ2bcdeCg65jkm8oFqi1tuvAxyz`) la entropía bajó a 3.5 y el resultado empezaba con `2f2a0a20...`, que es hexadecimal.
4. **Hex a bytes.** Al pasarlo a bytes salió código C de Arduino con un banner ASCII "RansomWorld IoT".
5. **Leer el firmware.** Es un ESP32 que:
    - se conecta al WiFi `Wifi-Pro` con clave `abcd1234`,
    - hace `GET` a `api.thingspeak.com`,
    - usa `channelID = "2271206"` (las API keys vienen truncadas con `XXXX`).
6. **Consultar el canal público.** Los canales públicos de ThingSpeak se pueden leer sin key. Con `location=true` se incluyen sus coordenadas:

```
https://api.thingspeak.com/channels/2271206/feeds.json?results=5&location=true
```

7. **Sacar la ubicación.** El JSON del canal devuelve:

```
"latitude":"28.538336","longitude":"-81.379234"
```

8. **Convertir coordenadas a ciudad.** `28.538336, -81.379234` es el centro de **Orlando, Florida**. Se puede verificar pegando las coordenadas en Google Maps o con OpenStreetMap/Nominatim.
9. **Calcular el MD5.**

bash

```bash
echo -n "Orlando" | md5sum
# d4d2ea493b6a2460e9b9f00712e0a234
```

### Comando(s) o payload clave


Flag

```
cidsi{d4d2ea493b6a2460e9b9f00712e0a234}
```

### ¿Qué aprendí / qué usaría de nuevo?

- Si en un texto codificado faltan `0`, `O`, `I` y `l`, hay que sospechar de Base58.
- Base58 tiene varios alfabetos (Bitcoin, Flickr, Ripple). Si el resultado tiene entropía alta y no da nada legible, hay que probar los demás.
- Un firmware de IoT filtrado casi siempre trae rutas a servicios externos (channel IDs, hosts, keys) que hay que seguir.
- Los canales públicos de ThingSpeak guardan latitud y longitud en el JSON, y eso es información OSINT útil.
- Con `echo -n` el MD5 sale sin el salto de línea. Sin `-n` daría otro hash y la flag sería incorrecta.

### ¿Me trabé en algo? ¿Cómo lo destrabé?

- Al decodificar con el alfabeto estándar de Bitcoin solo salió ruido. Lo destrabé probando los otros alfabetos y mirando la entropía del resultado, que en el de Ripple bajó de 7.97 a 3.5.
- Mi entorno no tenía acceso a `api.thingspeak.com` y el sitio de ThingSpeak bloquea a los bots. Lo resolví abriendo la URL del feed en el navegador y leyendo `latitude` y `longitude` del JSON.
- Entre las distintas capitalizaciones de "Orlando" solo una es la correcta. Seguí el formato del ejemplo del enunciado (`Valencia`).