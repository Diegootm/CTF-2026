# VPN y Cifrado en Tránsito

## ¿Por qué hace falta cifrar el tráfico?
Como vimos en [05-Sniffing-y-Analisis-de-Trafico](05-Sniffing-y-Analisis-de-Trafico.md), cualquiera que logre interceptar tráfico sin cifrar puede leerlo directamente. El cifrado en tránsito soluciona esto asegurando que, aunque alguien capture los paquetes, no pueda leer su contenido sin la clave correspondiente.

## TLS/SSL: el cifrado detrás de HTTPS
Cuando ven `https://` en vez de `http://`, significa que la comunicación entre el navegador y el servidor está cifrada usando **TLS** (el sucesor de SSL, aunque "SSL" se sigue usando coloquialmente para referirse a TLS también).

### El proceso simplificado (handshake TLS)
1. El navegador y el servidor negocian qué versión de TLS y qué algoritmos de cifrado van a usar.
2. El servidor presenta su **certificado digital**, que prueba su identidad (firmado por una autoridad certificadora de confianza).
3. Se establece una clave de sesión compartida (usando criptografía asimétrica para el intercambio inicial, y luego cifrado simétrico —más rápido— para el resto de la comunicación, ver [01-Criptografia](Apuntes-Hacking-Etico/01-Criptografia/00-Indice.md)).
4. A partir de ahí, todo el tráfico entre navegador y servidor va cifrado con esa clave de sesión.

### Relación con OSINT y Certificados
Los certificados TLS quedan registrados públicamente en los **CT Logs**, que es exactamente lo que consultan herramientas como `crt.sh` (ver [04-OSINT/05-Dominios-y-Certificados](05-Dominios-y-Certificados.md)) para descubrir subdominios.

## VPN (Virtual Private Network)
Una VPN crea un **túnel cifrado** entre un dispositivo y un servidor remoto, de forma que todo el tráfico que pasa por ese túnel queda protegido de ojos externos (por ejemplo, de otros dispositivos en una red Wi-Fi pública).

### Usos típicos
- Proteger el tráfico en redes públicas no confiables (cafés, aeropuertos).
- Acceder de forma segura a recursos internos de una empresa desde afuera.
- Ocultar la IP real de origen frente al servicio de destino (aunque el proveedor de la VPN sí la conoce).

### Protocolos de VPN comunes
- **OpenVPN**: de código abierto, muy configurable, ampliamente soportado.
- **WireGuard**: más moderno, más simple y generalmente más rápido que OpenVPN.
- **IPsec**: usado mucho en entornos empresariales, a menudo combinado con IKEv2.

## Por qué esto importa para CTF
- Si un reto de forense muestra tráfico HTTPS/TLS, no van a poder leer el contenido directamente sin una clave de sesión — es una señal de que el reto probablemente NO se resuelve mirando el contenido cifrado, sino los metadatos alrededor (IPs, tamaños de paquete, timing, o el propio handshake TLS que sí viaja sin cifrar).
- Entender que TLS protege el **contenido** pero no siempre oculta **con quién te estás comunicando** (la IP de destino sigue siendo visible en la mayoría de los casos, salvo con protecciones adicionales como ECH) es una distinción útil en varios retos de forense de red.
