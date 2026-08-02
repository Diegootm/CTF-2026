**Área:** Esteganografía / Criptografía **Dificultad:** - **Plataforma:** CITC 2024 **Link del reto o Nombre:** Mensaje de la naturaleza (140 pts) **Resuelto por:** Diego **Fecha:** 01/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía: _"La naturaleza te deja un mensaje oculto... solo el lenguaje de viejos servidores funciona para resolverlo, algunas teclas están mal."_ Esto apuntaba a dos cosas: (1) hay un mensaje escondido usando la **primera letra de cada línea** (acróstico) del texto entregado, y (2) "el lenguaje de viejos servidores" es una pista hacia **Base64** (el formato clásico de codificación usado en logs y servidores). Lo de "algunas teclas están mal" avisaba que, dentro de ese acróstico, algunos caracteres aparecían como **números** en vez de letras (una especie de "teclado roto"), pero en realidad esos números eran parte legítima del Base64 resultante.

## Herramienta(s) que usé

Python (extracción de la primera letra de cada línea + decodificador de Base64).

## Pasos (solo lo esencial, tipo lista)

- Tomé el texto completo del mensaje (30 líneas)
- Extraje el primer carácter de cada línea, en orden, respetando mayúsculas/minúsculas tal cual aparecían
- El resultado fue una cadena que a simple vista parecía basura, pero tenía la forma característica de **Base64**: `Y3VpZGVtb3MgbGEgbmF0dXJhbGV6YQ`
- Decodifiqué esa cadena como Base64 y obtuve el mensaje en texto plano: `cuidemos la naturaleza`
- Convertí esa frase a MD5 para intentar formar la flag en el formato que usan otros retos de la plataforma (`citc{hash_md5}`)

## Comando(s) o payload clave (si aplica)

```python
lines = [...]  # las 30 líneas del mensaje
firsts = ''.join(l[0] for l in lines)
# firsts = "Y3VpZGVtb3MgbGEgbmF0dXJhbGV6YQ"

import base64
print(base64.b64decode(firsts + "=").decode())
# -> "cuidemos la naturaleza"
```

```python
import hashlib
hashlib.md5("cuidemos la naturaleza".encode()).hexdigest()
# -> 7ec14fde5f321fb38cb7ee975070e60a
```

## Flag

**No se logró validar.** El mensaje decodificado en texto plano es correcto (`cuidemos la naturaleza`), pero no quedó claro el formato exacto que la plataforma esperaba para la flag final. Se probó:

```
citc{7ec14fde5f321fb38cb7ee975070e60a}
```

(MD5 de la frase tal cual, en minúsculas, con espacios simples) y no fue aceptada. Es posible que el formato correcto requiera otra variante — por ejemplo el texto con guiones bajos en vez de espacios antes de sacar el MD5 (`cuidemos_la_naturaleza`), con mayúscula inicial, sin espacios, u otra normalización distinta que no se identificó. **Reto dejado pendiente en este punto.**

## ¿Qué aprendí / qué usaría de nuevo?

Un acróstico (primera letra de cada línea) es una técnica común para esconder mensajes en textos largos "de relleno" temático (en este caso, sobre medio ambiente). Cuando el resultado de un acróstico se ve como una mezcla de mayúsculas, minúsculas y números sin sentido aparente, vale la pena probarlo directamente como Base64 antes de descartarlo — el patrón de caracteres (letras mixtas + algunos dígitos, sin símbolos raros) es una señal reconocible.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, en la parte final: aunque se descifró correctamente el mensaje oculto (`cuidemos la naturaleza`), no se pudo determinar con certeza el formato exacto que pedía la plataforma para la flag (mayúsculas, guiones bajos, con o sin espacios antes del MD5, etc.), y la plataforma no aceptó el intento con MD5 de la frase tal cual. Queda pendiente probar otras variantes de formato en un próximo intento.

---
