# Hashes y Crackeo de Contraseñas

## ¿Qué es un hash?
Un hash es el resultado de aplicar una función matemática a un dato, que produce una salida de **tamaño fijo** y **no se puede revertir**. No es cifrado: no existe una "clave" para volver del hash al dato original.

Se usan sobre todo para guardar contraseñas: en vez de guardar la contraseña real, un sistema guarda su hash. Cuando alguien intenta iniciar sesión, se hashea lo que escribió y se compara con el hash guardado.

## Por qué "no se revierte" pero sí se "crackea"
Como no se puede deshacer matemáticamente, la única forma de encontrar qué texto genera un hash es **probar candidatos** (contraseñas comunes, palabras de un diccionario, o combinaciones por fuerza bruta) y comparar sus hashes con el que tenemos. A eso le llamamos "crackear" un hash.

## Identificar el tipo de hash

| Algoritmo | Longitud típica (en hexadecimal) | Ejemplo |
|---|---|---|
| **MD5** | 32 caracteres | `5d41402abc4b2a76b9719d911017c592` |
| **SHA-1** | 40 caracteres | `aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d` |
| **SHA-256** | 64 caracteres | `2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824` |
| **bcrypt** | Empieza con `$2a$`, `$2b$` o `$2y$` | `$2b$12$KIXQ...` |
| **NTLM** (Windows) | 32 caracteres hexadecimales | Similar a MD5, pero por contexto (Windows) se sabe que es NTLM |

Si no reconocen el formato, herramientas como `hashid` o `hash-identifier` (o directamente pegarlo en CyberChef/dCode) los identifican automáticamente.

## Cómo crackear un hash

Dos estrategias principales:

1. **Ataque de diccionario**: se prueba una lista enorme de contraseñas comunes (la más famosa es `rockyou.txt`, que viene preinstalada en Kali). Es rápido y funciona sorprendentemente bien porque la gente reutiliza contraseñas predecibles.
2. **Fuerza bruta**: se prueban **todas** las combinaciones posibles dentro de un patrón (ej. "6 caracteres, solo minúsculas y números"). Es mucho más lento, se usa cuando el diccionario falla y se tiene una pista del formato de la contraseña.

### Con John the Ripper
```bash
john --wordlist=rockyou.txt hash.txt
```

### Con Hashcat (más rápido si hay GPU)
```bash
hashcat -m 0 -a 0 hash.txt rockyou.txt
```
El `-m 0` indica el modo (MD5 en este caso — cada algoritmo tiene su número, hashcat tiene una tabla de referencia).

## Dato importante para CTF
Si el reto menciona **salt** (un valor aleatorio añadido antes de hashear, para que la misma contraseña no dé siempre el mismo hash), van a necesitar ese salt para crackear correctamente — sin él, ni el diccionario ni la fuerza bruta van a coincidir nunca.
