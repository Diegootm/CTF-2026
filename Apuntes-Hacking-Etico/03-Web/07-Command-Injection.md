# Command Injection (Inyección de Comandos)

## ¿Qué es?
Ocurre cuando una aplicación toma la entrada del usuario y la pasa —sin validarla— a una función que ejecuta comandos del sistema operativo. Esto permite ejecutar instrucciones arbitrarias directamente en el servidor.

## Ejemplo
Un formulario que hace ping a una IP, ejecutando internamente algo como:
```bash
ping $usuarioInput
```
Si el input no se sanitiza, un atacante puede escribir:
```
127.0.0.1; ls -la
```
El sistema ejecuta el `ping` normal, y **además** ejecuta `ls -la` porque el `;` separa comandos en la terminal. Otros separadores que también suelen funcionar: `&&`, `||`, `|`, backticks (`` ` ``) o `$()`.

## Impacto
- Acceso a archivos del servidor.
- Control remoto del sistema.
- Instalación de malware / obtención de una shell remota.

## Cómo identificar un campo vulnerable
- Cualquier funcionalidad que "por detrás" claramente ejecuta un comando del sistema (ping, nslookup, conversión de archivos, herramientas de diagnóstico integradas en un panel de administración).
- Probar separadores de comandos (`;`, `&&`, `|`) seguidos de un comando inofensivo como `whoami` o `id`, y ver si aparece en la respuesta.

## Protección (para entender qué se está evadiendo)
- Escapar las entradas correctamente.
- Usar funciones "seguras" que no invoquen una shell del sistema directamente.
- Nunca concatenar directamente el input del usuario dentro de un comando.

## Relación con Web Exploitation y Miscelánea
Command Injection es la puerta de entrada típica hacia obtener una **shell remota** en el servidor — el siguiente paso natural después de lograrla suele ser el mismo "ciclo de exploit" descrito en [01-Fundamentos-de-Explotacion](01-Fundamentos-de-Explotacion.md): establecer conexión estable (por ejemplo con `nc`), explorar el sistema, y buscar la flag o escalar privilegios.
