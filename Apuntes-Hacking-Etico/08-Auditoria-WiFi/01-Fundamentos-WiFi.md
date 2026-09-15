# Fundamentos Wi-Fi

Para auditar una red, primero hay que entender cómo viaja la información en el aire y cuáles son los eslabones débiles.

## Modos de la tarjeta de red (NIC)
Las tarjetas Wi-Fi normalmente ignoran el tráfico que no es para ellas. Para auditar, hay que cambiar eso:

- **Modo Managed (Administrado)**: estado normal de cualquier dispositivo. Solo procesa paquetes destinados específicamente a su MAC.
- **Modo Monitor**: la tarjeta "escucha" todo el tráfico de radiofrecuencia en el aire en un canal específico, sin necesidad de estar conectada a ningún router. Es esencial para capturar *handshakes* e inyectar paquetes.

## Evolución de la seguridad Wi-Fi

| Protocolo | Estado | Notas |
|---|---|---|
| **WEP** | Obsoleto | Vulnerable a ataques estadísticos; se rompe en minutos |
| **WPA / WPA2** | Estándar actual (mayoría de redes) | Su seguridad depende de la longitud de la contraseña y de tener el WPS apagado |
| **WPA3** | El nuevo estándar | Usa el protocolo SAE, que mitiga los ataques de diccionario *offline* que sí funcionan contra WPA2 |

## Por qué WPA2 sigue siendo el foco principal de la auditoría
La gran mayoría de redes domésticas y de oficina todavía usan WPA2 (WPA3 recién se está adoptando masivamente), así que los vectores de ataque más relevantes en la práctica siguen siendo los que explotan debilidades de WPA2 — ver el siguiente archivo.
