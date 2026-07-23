
**Área:** Cripto **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** hashcrack **Resuelto por:** Diego **Fecha:** 23/07 **Tiempo que tardé:** ~15 min

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía que el admin usaba "contraseñas débilmente hasheadas" — eso ya apuntaba a que no había que romper el algoritmo matemáticamente, sino adivinar contraseñas comunes y compararlas contra el hash se podia haber hecho con ataque de diccionario o con dcode. El servidor iba dando hashes de distinta longitud en cada ronda.

## Herramienta(s) que usé

Terminal (`nc`), y dcode para ir identificando el tipo de hash y descrifrarlo

## Pasos (solo lo esencial, tipo lista)

- Me conecté al servidor con `nc verbal-sleep.picoctf.net 50927`
- El servidor dio un hash: `482c811da5d5b4bc6d497ffa98491e38` (32 caracteres hex → MD5)
- Probé una lista de contraseñas comunes calculando su MD5 hasta encontrar coincidencia → `password123`
- Al acertar, el servidor dio un segundo hash: `b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3` (40 caracteres hex → SHA-1)
- Repetí el proceso con SHA-1 → `letmein`
- El servidor dio un tercer hash: `916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745` (64 caracteres hex → SHA-256)
- Esta contraseña no estaba en listas cortas, así que usé una wordlist más grande (10k-most-common y Pwdb_top-100000 de SecLists) → `qwerty098`
- Al acertar las 3, el servidor reveló la flag

## Comando(s) o payload clave (si aplica)

|Algoritmo|Longitud en hex|Bits|
|---|---|---|
|MD5|32 caracteres|128 bits|
|SHA-1|40 caracteres|160 bits|
|SHA-256|64 caracteres|256 bits|

## Flag

`picoCTF{UseStr0nG_h@shEs_&PaSswDs!_ccc21957}`

## ¿Qué aprendí / qué usaría de nuevo?

La longitud del hash (en caracteres hexadecimales) identifica el algoritmo al instante: 32=MD5, 40=SHA-1, 64=SHA-256. Y contra hashes sin salt, no hace falta "descifrar" nada — basta un buen diccionario de contraseñas comunes e ir probando.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

La contraseña del tercer hash (SHA-256) no estaba en una lista corta de contraseñas comunes. Lo resolví bajando una wordlist más grande (SecLists, con decenas de miles de contraseñas reales filtradas de brechas) y probando contra esa.
