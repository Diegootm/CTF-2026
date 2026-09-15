# Fundamentos de OSINT

## ¿Qué es OSINT?
**OSINT** (Open Source Intelligence, o "Inteligencia de Fuentes Abiertas") es la recolección y análisis de información obtenida de **fuentes públicas o abiertas**, con un propósito específico. No involucra hackear nada — todo lo que se busca ya es públicamente accesible, el trabajo está en saber **dónde** y **cómo** buscarlo.

## Fuentes típicas de información
- Redes sociales.
- Registros públicos (dominios, empresas, propiedades).
- Metadatos de archivos (ver también [03-Metadatos en Forense](03-Metadatos.md), es el mismo concepto aplicado a investigar personas/lugares).
- Foros y comunidades online.
- Documentos públicos (PDFs institucionales, informes, actas).

## Cómo se ve un reto de OSINT en CTF
Generalmente te dan una pista de partida (un nombre de usuario, una foto, un correo, un nombre de dominio) y el objetivo es **encadenar información pública** hasta llegar a un dato específico que responde la pregunta del reto (ej. "¿en qué ciudad vive esta persona?", "¿cuál es el correo real detrás de este dominio?").

## Mentalidad para resolver OSINT
1. **Empiecen por lo obvio**: busquen el dato de partida tal cual en Google, en redes sociales, con búsqueda inversa de imágenes.
2. **Anoten cada pista nueva** que encuentren — un usuario puede llevarlos a un correo, el correo a un dominio, el dominio a más nombres de usuario.
3. **Crucen información entre plataformas**: la gente reutiliza nombres de usuario, fotos de perfil y correos entre distintas redes sociales — esa reutilización es la base de casi todas las herramientas de esta área.
4. **No subestimen los metadatos**: una foto puede tener coordenadas GPS exactas en su metadata (ver [04-Imagenes-y-Geolocalizacion](04-Imagenes-y-Geolocalizacion.md)).
