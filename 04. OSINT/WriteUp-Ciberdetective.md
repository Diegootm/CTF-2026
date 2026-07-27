
**Área:** OSINT **Dificultad:** - **Plataforma:** CIDSI **Link del reto o Nombre:** - **Resuelto por:** Diego **Fecha:** 24/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El reto daba solo un fragmento de un mensaje, incompleto: "¡Sálvese quien pueda! ¡Ha caído una brasa en el g...". Busqué esa frase textual y apareció como parte del libro **El pájaro de fuego**, lo cual apuntaba a que el autor del mensaje era el autor de esa obra.

## Herramienta(s) que usé

Buscador web (para localizar la frase y el libro).

## Pasos (solo lo esencial, tipo lista)

- Busqué la frase exacta del fragmento del mensaje
- Encontré que pertenece al libro "El pájaro de fuego"
- Identifiqué al autor: en un primer momento aparecía como "Óscar Alfaro", pero la flag no era correcta con ese nombre
- Busqué el nombre completo del autor: **Óscar González Alfaro**
- Convertí el nombre completo a MD5
- Envolví el hash en el formato pedido por el reto

## Comando(s) o payload clave (si aplica)

```
Nombre completo del autor: Óscar González Alfaro
MD5("Óscar González Alfaro") -> 1c7028e33dd152b4071e25ef879215a9
```

## Flag

`cidsi{1c7028e33dd152b4071e25ef879215a9}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un reto de OSINT pide el nombre de una persona (autor, personaje histórico, etc.) y la primera versión del nombre (apodo o nombre corto/incompleto) no da la flag correcta, hay que buscar el **nombre completo real** de esa persona — muchos autores son conocidos por una forma abreviada de su nombre, pero el reto suele esperar el nombre completo tal como aparece en fuentes oficiales/biográficas.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí: al principio usé "Óscar Alfaro" (el nombre más común/corto del autor) para generar el MD5, y la flag no fue aceptada. Lo resolví buscando el nombre completo del autor ("Óscar González Alfaro") y generando el hash con ese nombre completo.

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._

