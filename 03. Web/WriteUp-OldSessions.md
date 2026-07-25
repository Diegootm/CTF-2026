Área: Web Exploiting Dificultad: Fácil Plataforma: picoCTF Nombre: OldSessions Resuelto por: Diego Fecha: 25/07/2026 Tiempo que tardé: 15 minutos

## ¿Qué pista/detalle me hizo saber por dónde ir?

el enunciado explicaba que el sitio nunca fuerza expiración de sesión ("once you login, you never have to log-out again"), y al mirar mi cookie `session` en DevTools su fecha de expiración estaba fijada varios años en el futuro (2027) — señal de que una sesión vieja/filtrada de otro usuario podía seguir siendo válida. Además, entre los comentarios de la página principal, el usuario `mary_jones_8992` dejó la pista directa: "Hey I found a strange page at /sessions"

## Herramienta(s) que usé

DevTools del navegador (pestaña Application → Cookies), navegación directa a /sessions

## Pasos que segui para resolver el ejercicio

- me registré/logueé normal en el sitio con mi propio usuario (diego)
- revisé Application → Cookies y vi mi cookie `session` con expiración configurada para 2027, confirmando que las sesiones no expiran nunca
- leí los comentarios de la Homepage y encontré la pista de `mary_jones_8992` mencionando una página en `/sessions`
- navegué a `/sessions` y encontré, sin ningún control de acceso, un listado con los tokens de sesión activos de todos los usuarios, incluyendo:
    - `session:Q9S5AQzoN17YYKXJePH2cWvR5vEQb04mBnvYJt9fHhM` → `{'_permanent': True, 'key': 'admin'}`
    - `session:8dSrZFYzyS9-cX5YLJTPz0vcTFWFmTh-l4wJzGPKf94` → `{'_permanent': True, 'key': 'diego'}` (la mía)
- reemplacé el valor de mi cookie `session` por el token del admin directamente en DevTools (Application → Cookies → editar Value)
- recargué la página y quedé autenticado como `admin` sin haber usado ninguna credencial suya
- la flag apareció en la sesión del admin

Comando(s) o payload clave (si aplica)

```js
document.cookie = "session=Q9S5AQzoN17YYKXJePH2cWvR5vEQb04mBnvYJt9fHhM; path=/";
location.reload();
```

Flag picoCTF{s3t_s3ss10n_3xp1rat10n5_ed8964c2}

## Qué aprendí / qué usaría de nuevo?

que la combinación de "sesiones que nunca expiran" + "un endpoint que expone tokens de sesión de otros usuarios sin control de acceso" es un patrón clásico de session hijacking; revisar fechas de expiración de cookies y prestar atención a comentarios/contenido de usuarios dentro de la app (a veces contienen pistas reales de endpoints ocultos) es un buen hábito en retos de este tipo

## ¿Me trabé en algo? ¿Cómo lo destrabé?

al principio no sabía bien qué hacer con mi propia cookie de sesión ni entendía la relevancia del enunciado. Me destrabé al leer con atención los comentarios de usuarios en la página, donde encontré la pista textual de la ruta `/sessions`, que resultó exponer directamente los tokens de todos los usuarios logueados