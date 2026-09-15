# Para investigar después — Web

- **CSRF (Cross-Site Request Forgery)**: forzar a un usuario autenticado a ejecutar una acción sin su consentimiento, aprovechando que su navegador reenvía cookies automáticamente. Muy relacionado con XSS pero con un mecanismo distinto.
  https://portswigger.net/web-security/csrf

- **Deserialización insegura**: cuando una aplicación reconstruye objetos a partir de datos que el usuario controla (ej. cookies serializadas), y eso permite inyectar objetos maliciosos que ejecutan código al deserializarse.
  https://portswigger.net/web-security/deserialization

- **SSTI (Server-Side Template Injection)**: cuando el motor de plantillas de un framework web (Jinja2, Twig, etc.) interpreta input del usuario como código de plantilla, permitiendo ejecución remota de código.
  https://portswigger.net/web-security/server-side-template-injection

- **GraphQL security**: cada vez más APIs usan GraphQL en vez de REST, y tiene sus propias vulnerabilidades típicas (introspección expuesta, batching attacks).
  https://portswigger.net/web-security/graphql

- **Race conditions en aplicaciones web**: explotar condiciones de carrera enviando peticiones simultáneas para saltarse validaciones (ej. usar un cupón dos veces).
  https://portswigger.net/web-security/race-conditions

- **PortSwigger Web Security Academy completo**: labs gratuitos organizados por vulnerabilidad, con teoría incluida — el mejor lugar para profundizar cualquiera de los temas de esta carpeta.
  https://portswigger.net/web-security/all-labs
