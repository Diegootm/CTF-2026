Área: Cripto Dificultad: medio Plataforma: picoCTF Link del reto o Nombre:Castillo Perdido Resuelto por: Xavi  Fecha: 07/09 Tiempo que tardé: 30 min

## ¿Qué pista/detalle me hizo saber por dónde ir?

Bueno el problema daba un txt con varias lineas de base64 por lo que se sabia que habia que decodifcar y con ello se iria resolviendo el problema
## Herramienta(s) que usé

- decodificador de base64 
## Pasos (solo lo esencial, tipo lista)

1. Decodifique el archivo y todo eso lo puse en otro archivo 
2. A ese archivo con el texto  descifrado lo volvi a descodifcar 
3. revisando este ultimo archivo decodificado se podia ver por la firma de los hexadecimales que era un png con xxd wally3.txt | head 

## Comando(s) o payload clave (si aplica)

```
base64 -d wally
```

## Flag

`picoCTF{7845cfe9006463483f2e36f67d6ce20a}`

## ¿Qué aprendí / qué usaría de nuevo?
Al principio pense que tenia que decodificar con alguna herramienta como dcode pero luego recorde que kali ya tiene un decodificador preinstalado
## ¿Me trabé en algo? ¿Cómo lo destrabé?

Pues al ver que el ultimo archivo me arrojaba full binarios con cat entocnes decidi ver los hexadeciamles y coincidian con un png por lo que solo cambie la extension y la flag estaba dentro de la imagen 