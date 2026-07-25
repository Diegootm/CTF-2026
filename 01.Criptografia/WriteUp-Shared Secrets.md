**Área:** Cripto **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Shared Secrets **Resuelto por:** Xavi **Fecha:** 23/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado menciona que se necesita la llave privada 
## Herramienta(s) que usé

Python 3 (`pow()` para exponenciación modular)

## Pasos (solo lo esencial, tipo lista)

- Calcular el secreto compartido Diffie-Hellman: `S = pow(A, b, p)`.
- Revisar el script fuente del reto (`reconstruir.py` / generador) para confirmar cómo se derivaba la clave de cifrado real a partir de `S`. Se descubrió que la clave era simplemente `S % 256` (un solo byte), y que el cifrado era XOR de ese byte contra cada byte del mensaje.
- Convertir `enc` de hexadecimal a bytes.
- Aplicar XOR byte por byte entre `enc` y `S % 256` para recuperar el texto plano.
## Comando(s) o payload clave (si aplica)

```
shared = pow(A, b, p) key_byte = shared % 256 enc_bytes = bytes.fromhex(enc) flag = bytes([x ^ key_byte for x in enc_bytes]) print(flag.decode())
```

## Flag

picoCTF{dh_s3cr3t_1b25e19f}
## ¿Que aprendí / qué usaría de nuevo?

`encryption.py` (dato del ejercicio) aunque no se pueda ejecutar tal cual (traía `b = '???'` como placeholder), leerlo me dijo exactamente cómo se derivaba la clave real (`shared % 256`, un solo byte) y qué operación desencriptaba el mensaje (XOR). La próxima vez, antes de suponer un esquema "estándar" de cifrado, reviso primero si hay un script fuente disponible

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Al conseguir el script `encryption.py` original: ahí vi que la clave no era un hash completo sino solo `shared % 256`, y que el cifrado era un XOR de un solo byte, mucho más simple 
