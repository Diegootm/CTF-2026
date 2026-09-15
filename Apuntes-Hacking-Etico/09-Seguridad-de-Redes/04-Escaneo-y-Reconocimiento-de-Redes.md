# Escaneo y Reconocimiento de Redes

## Por qué es el primer paso siempre
Antes de atacar cualquier cosa, hay que saber **qué existe** en la red: qué hosts están activos, qué puertos tienen abiertos, qué servicios corren en cada uno. Sin este paso, se está "atacando a ciegas".

## nmap: la herramienta central
Ya lo vimos aplicado a Web en [03-Web/03-Reconocimiento-y-Enumeracion](03-Reconocimiento-y-Enumeracion.md), pero acá va el panorama completo pensado para redes en general, no solo servidores web.

### Descubrimiento de hosts activos (sin escanear puertos todavía)
```bash
nmap -sn 192.168.1.0/24
```
Hace un "ping sweep" a todo el rango de la red, para saber rápidamente quién está conectado, sin gastar tiempo escaneando puertos de hosts que ni siquiera están activos.

### Escaneo de puertos: TCP vs UDP
```bash
nmap -sS 192.168.1.5      # escaneo SYN (TCP), el más común, relativamente sigiloso
nmap -sU 192.168.1.5      # escaneo UDP (más lento, pero revela servicios como DNS/SNMP)
```

### Detección de servicios y versiones
```bash
nmap -sV 192.168.1.5
```
Muy importante: saber la **versión exacta** de un servicio permite después buscar si esa versión específica tiene vulnerabilidades públicas conocidas (bases de datos como [exploit-db.com](https://www.exploit-db.com/)).

### El motor de scripts NSE (Nmap Scripting Engine)
Nmap incluye cientos de scripts para tareas específicas de reconocimiento y hasta detección de vulnerabilidades conocidas:
```bash
nmap --script vuln 192.168.1.5          # prueba una batería de scripts de detección de vulnerabilidades
nmap --script smb-vuln* -p 445 192.168.1.5   # vulnerabilidades específicas de SMB (puerto 445, típico de Windows)
```

## Otras herramientas de reconocimiento de red

### netdiscover
Descubre hosts activos en una red local usando ARP (más rápido que un ping sweep de nmap en redes locales pequeñas).
```bash
netdiscover -r 192.168.1.0/24
```

### Enumeración de servicios específicos
Una vez identificado un puerto/servicio interesante, conviene enumerar más a fondo con herramientas específicas del servicio:
- **SMB** (puerto 445, típico en Windows): `enum4linux`, `smbclient`
- **SNMP** (puerto 161): `snmpwalk` — si la comunidad SNMP es la default (`public`), a menudo revela muchísima información de configuración del dispositivo.
- **FTP** (puerto 21): probar login anónimo (`anonymous`/sin contraseña) — sorprendentemente común que esté habilitado por error.

## Flujo recomendado de reconocimiento
1. `nmap -sn` (o `netdiscover`) para saber qué hosts están vivos en la red.
2. `nmap -sV -sС` sobre cada host de interés, para saber qué corre y qué versión.
3. Investigar si alguna versión detectada tiene vulnerabilidades públicas conocidas.
4. Enumerar a fondo los servicios más prometedores con herramientas específicas (SMB, SNMP, FTP, etc.).
