**Área:** Forense **Dificultad:** Medio (500 pts)  **Plataforma:** Cidsi **Link del reto o Nombre:** Pagos  
**Resuelto por:** Axel  **Fecha:** 27/09 **Tiempo que tardé:** 15min

### ¿Qué pista/detalle me hizo saber por dónde ir?

Al hacer `strings` sobre el `.pcapng` aparecieron dos cosas:

- Rutas y nombres SMB en UTF-16 como `\\192.168.27.206\sniper`, `wallet.enc`, `wallet.lnk` y `transfer.info`.
- Un bloque de texto en base64 que, al decodificarlo, resultó ser "lorem ipsum" con **código Morse escondido**. El Morse decía:

```
NOTE: USE YOUR USERNAME TO EXTRACT THE WALLET ADDRESS
```

Con eso el camino quedó claro: el archivo `wallet.enc` está cifrado y la clave es el nombre de usuario de la sesión SMB.

### Herramienta(s) que usé

- `strings` para la primera inspección y para ver cadenas UTF-16 (`strings -el`).
- Python + scapy para reensamblar los streams TCP y parsear los mensajes SMB2. En mi caso `tshark` no estaba disponible.
- `base64`, para decodificar el blob y el resultado final.
- `openssl`, para descifrar `wallet.enc`.

### Pasos

1. **Reconocer el tráfico.** Todo el tráfico es SMB2 (puerto 445) entre `192.168.27.100` (cliente) y `192.168.27.206` (servidor), con 3 conexiones TCP principales.  
    _Wireshark:_ Statistics → Conversations → TCP.
2. **Buscar strings.** Con `strings -n 6 Pagos.pcapng` aparecen las rutas SMB y el blob base64.
3. **Decodificar el blob base64.** Se juntan los fragmentos, se decodifica y entre el lorem ipsum salen puntos y guiones (Morse).  
    Sus palabras están separadas por 7 puntos (`.......`). Decodificado: `NOTE: USE YOUR USERNAME TO EXTRACT THE WALLET ADDRESS`.
4. **Sacar el usuario.** En los mensajes NTLMSSP tipo 3 (autenticación) el usuario es `sniper`, con dominio `WORKGROUP`.  
    _Wireshark:_ filtro `ntlmssp.auth.username`.
5. **Reensamblar los streams TCP** con scapy, ordenando por número de secuencia. Después parseé los frames NetBIOS/SMB2 y filtré las respuestas **READ** (comando 8) grandes, que contienen los datos de los archivos.  
    _Wireshark:_ File → Export Objects → SMB.
6. **Identificar `wallet.enc`.** Uno de los READ empieza con `Salted__` (144 bytes). Es la firma de un archivo cifrado con `openssl enc` en formato salted. Lo guardé como `wallet.enc`. El otro READ grande (8393 bytes) era el blob base64 del paso 3.
7. **Descifrar con el usuario como contraseña.** Probé combinaciones de cifrado y hash. La correcta fue AES-256-CBC con SHA-256:

bash

```bash
openssl enc -d -aes-256-cbc -md sha256 -in wallet.enc -pass pass:sniper
```

8. **Decodificar el resultado.** La salida seguía en base64:

bash

```bash
openssl enc -d -aes-256-cbc -md sha256 -in wallet.enc -pass pass:sniper | base64 -d
```

```
Do the transfer to this Bitcoin wallet

flagHunters{827b86172465ff938f962b010d7b544f}
```

### Comando(s) o payload clave

bash

```bash
openssl enc -d -aes-256-cbc -md sha256 -in wallet.enc -pass pass:sniper | base64 -d
```

### Flag

```
cidsi{827b86172465ff938f962b010d7b544f}
```

### ¿Qué aprendí / qué usaría de nuevo?

- Correr `strings` (y `strings -el` para UTF-16) sobre un pcap antes de abrir Wireshark da pistas rápidas.
- Un archivo que empieza con `Salted__` es de OpenSSL. Si el descifrado da basura, hay que probar otros `-md` (md5 o sha256) y con o sin `-pbkdf2`, porque cambiaron los valores por defecto entre versiones.
- Las credenciales NTLM del tráfico SMB dan el usuario, y aquí ese usuario era la contraseña.
- Los mensajes ocultos pueden venir en capas: base64 → texto → Morse → clave → cifrado → base64 otra vez.

### ¿Me trabé en algo? ¿Cómo lo destrabé?

- `tshark` no estaba instalado, así que reensamblé los streams con scapy. La primera vez usé `Raw` y me dio muy pocos bytes, porque scapy había disecado SMB y no lo marcaba como Raw. Lo arreglé usando `bytes(p[TCP].payload)`.
- Con `openssl` casi todas las combinaciones daban basura. Solo `aes-256-cbc` con `-md sha256` dio texto legible, así que conviene probarlas todas en un bucle.