# Forense de Memoria (RAM)

## ¿Qué es?
Es el análisis de un **volcado de memoria RAM** (un archivo que contiene una "foto" completa de lo que estaba en la memoria de una computadora en un momento dado). A diferencia del disco, la RAM contiene información que desaparece al apagar el equipo: procesos corriendo, contraseñas en texto plano que estaban siendo usadas, conexiones de red activas, malware que nunca se guardó en disco, etc.

## Por qué importa en CTF
Un volcado de memoria puede revelar cosas que jamás aparecerían analizando solo el disco duro: procesos maliciosos ejecutándose, contenido de la clipboard, contraseñas escritas en memoria, o archivos abiertos en el momento de la captura.

## Herramienta principal: Volatility
Volatility es el framework estándar para analizar volcados de memoria. Funciona por "plugins", cada uno enfocado en extraer un tipo de información distinto.

### Flujo básico de uso (Volatility 3)
1. **Identificar el perfil/sistema operativo** del volcado (Volatility 3 suele detectarlo automáticamente, a diferencia de la versión 2 que requería especificarlo a mano).
2. **Listar procesos en ejecución** al momento de la captura:
   ```bash
   vol -f volcado.raw windows.pslist
   ```
3. **Buscar conexiones de red activas**:
   ```bash
   vol -f volcado.raw windows.netscan
   ```
4. **Extraer el historial de comandos o la clipboard**, según el plugin disponible.
5. **Volcar (dump) un proceso sospechoso a disco** para analizarlo por separado, por ejemplo con Ghidra si resulta ser un ejecutable.

## Qué buscar en un reto de memoria
- Procesos con nombres raros o que no deberían estar corriendo.
- Contraseñas o texto plano flotando en la memoria (se pueden buscar con `strings` sobre el propio volcado, igual que en cualquier archivo).
- Archivos abiertos al momento de la captura.
- Conexiones de red hacia direcciones IP sospechosas.

## Tip práctico
Antes de sumergirse en Volatility con plugins complejos, prueben simplemente:
```bash
strings volcado.raw | grep -i "flag"
```
Muchos retos "fáciles" de memoria esconden la flag como texto plano en algún proceso, y aparece directo con este comando sin necesitar análisis más profundo.
