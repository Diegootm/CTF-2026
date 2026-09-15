# SQL Injection

## ¿Qué es?
Es una vulnerabilidad donde una consulta SQL se construye **concatenando directamente** datos que escribió el usuario, sin validarlos ni escaparlos. Eso permite que un atacante modifique la lógica de la consulta original.

## Ejemplo clásico
Supongamos que un login construye esta consulta:
```sql
SELECT * FROM users WHERE username = '$input'
```
Si el atacante escribe como usuario:
```
' OR 1=1--
```
La consulta se convierte en:
```sql
SELECT * FROM users WHERE username = '' OR 1=1--'
```
`OR 1=1` es siempre verdadero, así que la consulta devuelve **todos los usuarios** — típicamente esto basta para saltarse un login sin conocer ninguna contraseña. El `--` comenta el resto de la consulta para que no rompa la sintaxis.

## Impacto
- Acceso no autorizado a datos (ver información de otros usuarios).
- Modificación o eliminación de registros.
- En casos graves, acceso remoto al servidor (dependiendo de permisos de la base de datos).

## Cómo se identifica un campo vulnerable
- Probar caracteres especiales SQL (`'`, `"`, `;`, `--`) en cualquier campo de entrada y ver si el comportamiento de la aplicación cambia (errores, resultados inesperados).
- Un error de sintaxis SQL visible en la respuesta es una señal clarísima de que el campo es vulnerable.

## Tipos de SQLi que van a encontrar
- **Basada en errores**: el servidor devuelve mensajes de error de SQL que revelan información sobre la base de datos.
- **Basada en UNION**: se usa `UNION SELECT` para combinar la consulta original con una que extrae datos de otra tabla.
- **Ciega (blind)**: no hay mensajes de error visibles, pero se puede inferir información observando diferencias sutiles en la respuesta (verdadero/falso) o el tiempo que tarda en responder (time-based).

## Protección (para entender qué NO va a funcionar como ataque)
- Uso de consultas preparadas (prepared statements) o un ORM.
- Escapar correctamente las entradas.
- Principio de privilegios mínimos en la base de datos.

## Herramienta clave: sqlmap
Automatiza la detección y explotación de SQLi — no hace falta construir los payloads a mano en la mayoría de los casos.
```bash
sqlmap -u "http://example.com/producto?id=1" --dbs
```
Esto intenta detectar si el parámetro `id` es inyectable y, de serlo, lista las bases de datos disponibles. A partir de ahí se puede seguir extrayendo tablas, columnas y datos con más parámetros de sqlmap.
