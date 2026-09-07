

**Área:** Cripto **Dificultad:** Medio **Plataforma:** CIDSI **Link del reto o Nombre: ## La transmutación de la alquimista    **Resuelto por : Xavi **Fecha:** 7/09/26 **Tiempo que tardé:** 30 minutos 

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El problem nos da una codificacion que tenemos que decodificar entonces la cosa es encontrar los cifrados correctos
Operaciones Alquímicas de Mercuria:

- "Calcinación" - Reduce los metales a 16 espíritus elementales  // XOR
- "Digestión" - La retícula hexagonal purifica la esencia oculta   //Base 64
- "Sublimación" - Gira 13 ciclos lunares para fijar el espíritu      //rot13 
- "Putrefacción" - Los pares de runas perecen y renacen con sus lugares intercambiados //intercambiar posicion de cada para el 0 en el lugar del 1, del 2 en 3 y asi sucesivamente
- "Multiplicación" - El resultado final multiplica el valor: mercurio transformado en oro solar         //El mensaje final que habla sobre el  oro 
## Herramienta(s) que usé

dcode donde los comandos usados:
## Pasos (solo lo esencial, tipo lista)
Primero probe con ASCII me devolvia un base 64 que no llevaba a nada entonces pense en lo que decia sobre el hexadecimal por los 16 entonces tambien se puede hacer con ciberchef de hex a ascii funciona pero tambien fucniona XOR da el mismo resultado

Despues se decodifica ese texto base64 y devuelve un cifrado parecido a base 64 pero como dice el ejercicio hay que aplicar un rot13 

Despues intercambiar las posiciones de los caracteres como se menciono antes, intercambiar posicion de cada para el 0 en el lugar del 1, del 2 en 3 y asi  

y ya por ultimo volver a aplicar base64 y devulve la flag 

## Comando(s) o payload clave (si aplica)


## Flag

citc{8b94291ae4263cdbc053871a19e52c53}
## ¿Qué aprendí / qué usaría de nuevo?

que se debe comprender bien los enunciados ya que puede estar el camino para la respuesta 
## ¿Me trabé en algo? ¿Cómo lo destrabé?

Pues al principio no encontraba nada con ASCII etnoces tuve que probar con otras cosas como ciberchef y ya por ultimo el XOR 00 que arroja el mismo resultado