## Writeup
**Área:** Reversing**Dificultad:** Fácil  
**Plataforma:** CITC **Link del reto o Nombre:** BYPASS simple **Resuelto por:** Xavi  **Fecha:** 03/08  **Tiempo que tardé:** ~15 min

---

#### ¿Qué pista/detalle me hizo saber por dónde ir?

El archivo `reversing` no tenía extensión y no se podía "abrir" como texto normal. Al correr `file` sobre él, se confirmó que era un **ELF de 64 bits (ejecutable de Linux)**, no un archivo de texto. Una vez ejecutado, el programa pedía usuario/contraseña y mostraba en pantalla una consulta SQL simulada:

```
Ejecutando consulta: SELECT * FROM users WHERE username='%s' AND password='%s';
```

Ver una consulta SQL armada con inputs del usuario, impresa tal cual en pantalla, es la señal clásica de una **inyección SQL** (SQLi) clásica de tipo `' OR 1=1--`.

---

#### Herramienta(s) que usé

- `file` (identificar tipo de archivo)
- `strings` (ver cadenas legibles del binario)
- `objdump` / `nm` (desensamblar y listar símbolos, binario no _stripped_)
- Terminal Linux para ejecutar el binario directamente

---

#### Pasos (solo lo esencial, tipo lista)

1. Identifiqué el archivo con `file reversing` → ELF 64-bit, ejecutable de Linux.
2. Le di permisos con `chmod +x reversing`.
3. Corrí `strings reversing | grep -i ctf` y ya aparecía la flag en texto plano (spoiler fácil).
4. Para resolverlo "como se pensó", desensamblé con `objdump -d` la función `authenticate()`.
5. Encontré que antes de cualquier comparación de credenciales, el código hacía:

c

```c
   if (strstr(username, "' OR 1=1--") != NULL) return 1;
```

6. Ejecuté el binario y en el campo de usuario escribí exactamente esa subcadena.
7. El programa imprimió la consulta "inyectada" y dio acceso, mostrando la flag.

---

#### Comando(s) o payload clave

bash

```bash
file reversing
chmod +x reversing
strings reversing | grep -i ctf
objdump -d --no-show-raw-insn -M intel reversing | less
./reversing
```

Payload de usuario (dejando la contraseña en blanco o con cualquier valor):

```
' OR 1=1--
```

Resultado en pantalla:

```
Ejecutando consulta: SELECT * FROM users WHERE username='' OR 1=1--' AND password='f';
¡Acceso concedido! Aquí está tu bandera: CTF{sql_injection_success}
```

---

#### Flag

```
CTF{sql_injection_success}
```

---

#### ¿Qué aprendí / qué usaría de nuevo?

- Cuando un binario **no está _stripped_**, `strings` + `nm`/`objdump` suelen resolver el reto casi de inmediato, sin necesidad de un desensamblador gráfico.
- El patrón `strstr(input, "algo")` en el desensamblado es una señal directa de un "bypass" tipo whitelist/backdoor oculto en la lógica de autenticación.
- El texto de una "consulta SQL simulada" no significa que haya un motor SQL real detrás — puede ser solo un `printf` cosmético para dar ambientación al reto, mientras la validación real ocurre con comparación de strings en C.

---

#### ¿Me trabé en algo? ¿Cómo lo destrabé?

Al principio intenté escribir el payload sin la comilla simple (`OR 1=1--` en vez de `' OR 1=1--`), y el programa seguía negando el acceso porque `strstr` buscaba la subcadena **exacta**, comilla incluida. Se destrabó al comparar carácter por carácter el string esperado (visto en el desensamblado / en `strings`) contra lo que estaba escribiendo, y notar que faltaba la comilla `'` justo antes de `OR`.