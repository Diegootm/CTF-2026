
**Área:** OSINT **Dificultad:** - **Plataforma:** CIDSI **Link del reto o Nombre:** Mitología (300 pts) **Resuelto por:** Diego **Fecha:** 28/07 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado describía a un héroe de la mitología griega que "mató a un monstruo híbrido y domó a un caballo alado" — esto identifica a **Belerofonte** (mató a la Quimera y domó a Pegaso). El enunciado también decía que "el artífice de este reto y otros más publicó hace mucho en la nube el nombre de este personaje junto a una frase que necesitas", lo cual apuntaba a que había que rastrear al autor de los retos en redes sociales, no investigar más sobre el propio mito.

## Herramienta(s) que usé

- Búsqueda web (para identificar al héroe mitológico y luego al autor)
- Sherlock (para rastrear el username `0xb3t0` / `xb3t0` en redes sociales)
- GitHub (API y repos del autor)
- Instagram (perfil del autor)
- X / Twitter (búsqueda avanzada `from:usuario palabra`)
- Decodificador Base64

## Pasos (solo lo esencial, tipo lista)

- Identifiqué al héroe: **Belerofonte** (mató a la Quimera, domó a Pegaso)
- A partir de otros retos resueltos anteriormente, identifiqué que el autor firmaba como **`0xb3t0`**
- Usé Sherlock para buscar el username `0xb3t0`/`xb3t0` en redes sociales y confirmar en cuáles tenía presencia
- Encontré sus cuentas: GitHub (`xb3t0`), Instagram (`0xb3t0`) y X (`@xb3t0`)
- Revisé el perfil de Instagram y sus publicaciones — confirmaron que era la persona correcta (la bio tenía el mismo hash MD5 del primer reto resuelto), pero sin mención a Belerofonte
- Revisé repos de GitHub (`RecursosOsint`, blog personal) — sin resultado
- Busqué en X con el operador avanzado `from:xb3t0 Belerofonte` para filtrar solo sus publicaciones relacionadas
- Encontré una publicación suya de una **imagen que decía "Belerofonte"**, con un comentario de respuesta en **Base64**
- Decodifiqué el Base64 → obtuve la flag en texto plano

## Comando(s) o payload clave (si aplica)

```
Búsqueda en X: from:xb3t0 Belerofonte

Comentario encontrado codificado en Base64:
TGEgZmxhZyBlczog... (decodifica a "La flag es: ...")
```

## Flag

`cidsi{177da95e6868518d050fc02acc000759}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un reto de OSINT dice que la pista está "en la nube" sin especificar plataforma, conviene primero identificar el **usuario/handle** del autor (a partir de retos anteriores o firmas que haya dejado) y luego usar el buscador avanzado de cada red social (como `from:usuario palabra_clave` en X) para filtrar directo, en vez de revisar publicación por publicación a mano. También quedó confirmado que este autor suele esconder las flags en **Base64** dentro de comentarios/respuestas.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí — al principio transcribí a mano el texto en Base64 desde una captura de pantalla y el decodificado salía corrupto (el string no era múltiplo de 4, indicando un carácter mal copiado). Lo resolví pidiendo el texto copiado y pegado directamente desde X en vez de transcrito a mano, lo que dio el Base64 exacto y una decodificación limpia.

---

