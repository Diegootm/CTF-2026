# Google Dorks

## ¿Qué son?
Es una técnica de búsqueda avanzada que utiliza operadores especiales del motor de Google para encontrar información difícil de localizar con una búsqueda normal — archivos expuestos por error, páginas de administración, información sensible indexada accidentalmente.

## Operadores principales

**Buscar archivos de un formato específico dentro de un sitio:**
```
filetype:pdf site:edu.co "calendario academico"
```

**Buscar archivos .txt que contengan una palabra específica:**
```
intext:"password" filetype:txt
```

**Buscar páginas que contengan una palabra en su título:**
```
intitle:"index of" "backup"
```

**Buscar una URL con una estructura específica:**
```
inurl:"/view/index.shtml"
```

## Operadores extra útiles

```
# Buscar una frase exacta
"password"

# Excluir un término de la búsqueda
joystick -controller

# Actúa como comodín
email * @company.com
```

## Combinándolos
La potencia real de los dorks está en **combinar varios operadores**. Por ejemplo:
```
site:example.com filetype:pdf intext:"confidencial"
```
Esto busca específicamente PDFs dentro de `example.com` que contengan la palabra "confidencial" — una búsqueda muy dirigida que sería imposible replicar escribiendo la frase normal en Google.

## Tip para CTF
Si el reto da un dominio o nombre de organización como punto de partida, combinar `site:` con `filetype:` suele ser de los primeros pasos más productivos — muchas veces revela documentos, backups o páginas que no deberían estar indexadas públicamente.
