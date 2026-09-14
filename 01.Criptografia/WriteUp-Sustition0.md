

**Área:** Cripto **Dificultad:** Medio **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Sustition0 **Resuelto por:** Diego **Fecha:** 23/07 **Tiempo que tardé:** ~20 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?
El ejercicio me decia que el mensaje estaba desordenado y que me dio una llave al incio y que podria ser cifrado por sustititucion y eso ya me hizo sospechar 

## Herramienta(s) que usé
- Decode - Substitution Cipher

## Pasos (solo lo esencial, tipo lista)
- primero lleve conte la llamce que me dio y justo eran 26 letras, las 26 del abecedario
- seguidamente lo lleve a analizar para ver que posible cifrado tenia y me salio substitution cipher 
- ahi dentro en la columna plain item coloque el avecedario normal y en coded item la key que el ejercicio me dio 

## Comando(s) o payload clave (si aplica)


## Flag

picoCTF{5UB5717U710N_3V0LU710N_59533A2E}

## ¿Que aprendí / qué usaría de nuevo?

Un cifrado de sustitución reemplaza cada letra del alfabeto por otra, siguiendo una correspondencia fija. La `a` siempre se convierte en la misma letra, la `b` en otra, y así con las 26. Esa correspondencia completa es la clave: una permutación del alfabeto, es decir las mismas 26 letras pero en otro orden.

La diferencia con el cifrado César o ROT que había visto antes es importante y vale la pena dejarla clara en el write-up. En César todas las letras se desplazan la misma cantidad fija (un solo número como clave), así que basta probar 25 opciones. En la sustitución cada letra va a una letra arbitraria sin patrón, así que la clave no es un número sino la tabla entera. Eso hace que el número de claves posibles sea gigantesco: 26 factorial, unas 403 septillones de combinaciones. Por fuerza bruta es imposible.

**Cómo se rompe entonces, y por qué la pista hablaba de frecuencia**

Aunque el espacio de claves es enorme, el cifrado tiene una debilidad fatal: no oculta la estructura del idioma. Si en el texto original la `e` es la letra más frecuente, en el texto cifrado la letra que la reemplaza también será la más frecuente. El cifrado cambia los símbolos pero no las estadísticas. Por eso funciona el **análisis de frecuencia**: cuentas qué letra aparece más en el mensaje cifrado y la mapeas a la `e` (o a la `a` en español), sigues con las siguientes más comunes, y vas reconstruyendo la tabla apoyándote en palabras que empiezan a tener sentido. Ese era el camino que ofrecía la pista.

**Lo que hizo especial a este reto**

En este caso no hizo falta el análisis de frecuencia porque la clave venía servida: la primera línea del mensaje era la tabla de sustitución completa, 26 letras distintas. El desafío no fue romper el cifrado sino darse cuenta de que esa línea era la clave y averiguar en qué dirección se leía el mapeo. Aquí conviene que anotes el detalle que te costó: probar el sentido del mapeo y verificarlo contra un fragmento conocido. Como sabías que la flag empieza por `picoCTF{`, comparar tu resultado contra eso te confirmaba si la tabla estaba en el sentido correcto sin tener que descifrar todo a ciegas. Ese truco — usar texto plano conocido (_known-plaintext_) para validar la clave — es lo más reutilizable que sacaste.

**Qué usaría de nuevo**

El flujo que funcionó: identificar el tipo de cifrado (dCode Cipher Identifier lo detectó como sustitución), reconocer que la primera línea era la clave, cargar la tabla en el decodificador de sustitución de dCode, y verificar contra `picoCTF{` para ajustar las correspondencias que fallaban. Y la lección concreta que te ahorró tiempo al final: cuando casi toda la flag sale bien pero unas letras no traducen, no rehagas todo — compara letra por letra contra el texto esperado y corrige solo esas filas de la tabla. También el detalle de mayúsculas y minúsculas, que fue lo que te frenó al final: una tabla de sustitución puede necesitar que ambos casos estén mapeados, y una opción mal puesta en la herramienta deja letras sin convertir.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

en como usar la key que me dio el ejercicio en el decode

