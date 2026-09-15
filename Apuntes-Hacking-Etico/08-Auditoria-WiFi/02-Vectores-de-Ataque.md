# Vectores de Ataque (WPA2)

## A. WPS (Wi-Fi Protected Setup)

### Pixie Dust Attack
Explota una mala generación de números aleatorios en el router para deducir el PIN de WPS (y con él, la contraseña de la red) casi al instante — mucho más rápido que un ataque de fuerza bruta tradicional contra el PIN.

## B. Captura del 4-Way Handshake
Cuando un dispositivo se conecta a una red WPA2, intercambia **4 paquetes** con el router para negociar el cifrado de la sesión — a esto se le llama el "4-way handshake".

**El ataque:**
1. Se fuerza la desconexión de un cliente ya conectado, enviándole paquetes de **Deauth** (desautenticación).
2. Al reconectarse automáticamente (la mayoría de dispositivos lo hacen solos), se captura el handshake completo.
3. Con el handshake capturado, se usa fuerza bruta o un diccionario de contraseñas **offline** (en la propia máquina del atacante, sin necesitar seguir conectado a la red) para intentar reconstruir la contraseña.

## C. Ataque PMKID (sin necesidad de clientes conectados)
A diferencia del ataque de handshake, este **no requiere que haya clientes conectados** al momento del ataque. Se captura una trama específica que el router envía, que contiene el PMKID (un valor derivado de la contraseña de la red), el cual también se ataca de forma offline con diccionario o fuerza bruta.

**Ventaja sobre el handshake:** al no depender de forzar una desconexión/reconexión de un cliente real, es más sigiloso y funciona incluso si no hay nadie conectado a la red en ese momento.

## Resumen comparativo

| Ataque | Requiere clientes conectados | Velocidad | Depende de |
|---|---|---|---|
| Pixie Dust (WPS) | No | Muy rápido | Router con WPS mal implementado |
| 4-Way Handshake | Sí (o forzar reconexión) | Depende del diccionario | Fuerza de la contraseña |
| PMKID | No | Rápido de capturar, crackeo depende del diccionario | Router vulnerable a exponer el PMKID |
