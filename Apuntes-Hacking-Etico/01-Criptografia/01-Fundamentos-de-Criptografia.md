# Fundamentos de Criptografía

## ¿Qué es la criptografía?
Es el arte y la ciencia de proteger información transformando datos legibles (**texto plano**) en datos ilegibles (**texto cifrado**), de forma que solo quien tenga la clave correcta pueda revertir el proceso y leer el contenido original.

## Tres conceptos que se confunden todo el tiempo

Esto es lo primero que hay que tener clarísimo, porque en CTF cada uno se ataca distinto:

| Concepto | ¿Qué hace? | ¿Se puede revertir? | Ejemplo |
|---|---|---|---|
| **Codificar** | Representa los datos en otro formato, sin ocultar nada realmente | Sí, siempre, sin clave | Base64, Hex, URL encoding |
| **Cifrar** | Oculta el contenido usando una clave | Sí, solo con la clave correcta | AES, RSA, César |
| **Hashear** | Genera una "huella digital" de tamaño fijo | No (es de un solo sentido) | MD5, SHA-256 |

**Por qué importa distinguirlos:** si ven algo con `==` al final y letras/números raros, seguramente es **codificación** (Base64) y se decodifica gratis, sin buscar ninguna clave. Si ven un hash de 32 caracteres hexadecimales, no lo van a "descifrar" — lo van a **crackear** (probar combinaciones hasta que el hash coincida).

## ¿Por qué la cripto de CTF no es "matemática pura"?
En la vida real, romper AES o RSA bien implementados es prácticamente imposible con la tecnología actual. En los CTF casi nunca se rompe el algoritmo en sí — se explota una **mala implementación**:
- Una clave reutilizada.
- Un número aleatorio que no era tan aleatorio.
- Un cifrado clásico usado donde debía ir uno moderno.
- Parámetros de RSA mal elegidos (primos pequeños, exponente `e` muy chico, etc.)

Por eso, más que "saber matemáticas avanzadas", en CTF ayuda más **reconocer patrones** y saber qué herramienta prueba cada ataque común. Ese es el enfoque de los siguientes archivos.

## El flujo mental para resolver un reto de cripto
1. **Miren el texto/archivo que les dan.** ¿Tiene un patrón reconocible? (ver [02-Cifrados-Clasicos](02-Cifrados-Clasicos.md) y [03-Codificaciones-Comunes](03-Codificaciones-Comunes.md))
2. **Miren el contexto del reto** (nombre, descripción, archivos adjuntos). Muchas veces el nombre del reto es una pista directa ("Cesar's salad" → cifrado César).
3. **Prueben herramientas automáticas de identificación** antes de perder tiempo a mano (dCode, CyberChef "Magic Wand").
4. **Si hay código fuente o script de cifrado**, léanlo — casi siempre ahí está el error que hay que explotar.
