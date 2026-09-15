# Herramientas de Criptografía

## Online (sin instalar nada)

| Herramienta | Para qué | Enlace |
|---|---|---|
| CyberChef | "Navaja suiza": decodifica, cifra/descifra, encadena operaciones, tiene identificador automático ("Magic Wand") | https://cyberchef.org/ |
| dCode | Identifica y resuelve decenas de cifrados clásicos automáticamente | https://www.dcode.fr/en |
| Boxentriq | Identificador de cifrados, útil cuando no reconocen el tipo | https://www.boxentriq.com/ |
| Cryptii | Alternativa a CyberChef, interfaz más simple | https://cryptii.com/ |
| FactorDB | Base de datos de factorizaciones — pegar `n` de RSA y ver si ya está factorizado | http://factordb.com/ |

## Línea de comandos

| Herramienta | Para qué | Instalación |
|---|---|---|
| OpenSSL | Certificados, cifrado/descifrado rápido, firmas | Viene preinstalado en la mayoría de distros Linux |
| John the Ripper | Crackeo de hashes y contraseñas | `sudo apt install john` |
| Hashcat | Crackeo de hashes (más rápido, usa GPU) | `sudo apt install hashcat` |
| RsaCtfTool | Ataques automáticos contra RSA débil | https://github.com/RsaCtfTool/RsaCtfTool |
| hashid / hash-identifier | Identificar qué tipo de hash es un string dado | `sudo apt install hashid` |

## Python (para scriptear ataques)
```bash
pip install pycryptodome gmpy2 sympy
```
- **pycryptodome**: cifrar/descifrar AES, DES, RSA desde Python.
- **gmpy2 / sympy**: aritmética con números grandes, útil para factorización y cálculos de RSA.

## Diccionarios para crackeo
- `rockyou.txt`: viene preinstalado en Kali Linux en `/usr/share/wordlists/rockyou.txt.gz` (hay que descomprimirlo con `gunzip`).
