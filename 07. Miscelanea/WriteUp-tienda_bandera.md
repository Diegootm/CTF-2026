**Área:** Miscelanea **Dificultad:** Medio **Plataforma:** picoCTF  (vía Cylab Academy)  
**Link del reto o Nombre:** tienda_bandera **Resuelto por:** Axel **Fecha:**25/09  
**Tiempo que tardé:** 10min

### ¿Qué pista/detalle me hizo saber por dónde ir?

Me daban el código fuente `store.c` de una "tienda de flags", y la pista decía: _"¡El cumplido de dos puede hacer algunas cosas raras cuando los números se vuelven realmente grandes!"_ — eso apuntaba directo a un **integer overflow por complemento a dos**. Revisando el código vi esta parte:

c

```c
int total_cost = 900*number_flags;
if(total_cost <= account_balance){
    account_balance = account_balance - total_cost;
}
```

`total_cost` es un `int` de 32 bits con signo, sin ningún chequeo de límite superior sobre `number_flags`. Si metía una cantidad lo bastante grande, la multiplicación desbordaba y `total_cost` se volvía negativo, lo que hacía que en vez de restarme plata, me la sumara.

### Herramienta(s) que usé

- `nc` (netcat) para conectarme al servicio
- Python (para calcular el valor exacto de overflow)

### Pasos (solo lo esencial, tipo lista)

- Analicé `store.c`: el saldo inicial es 1100, y para comprar la flag real hace falta `account_balance > 100000`.
- La única vía era la compra de la flag trucha, que multiplica `900 * number_flags` en un `int` de 32 bits sin validar el resultado.
- Calculé con un script en Python (simulando aritmética de 32 bits con signo) qué cantidad hacía que `900*number_flags` desbordara a negativo y que, al sumarse al saldo, superara los 100000.
- Encontré `number_flags = 2386095` → `total_cost = -2147481796` → nuevo saldo = `2147482896`.
- Me conecté al servicio y mandé la secuencia: comprar la flag trucha con esa cantidad, volver al menú, y comprar la flag 1337.

### Comando(s) o payload clave (si aplica)

bash

```bash
printf "2\n1\n2386095\n2\n2\n1\n" | nc xebec.cylabacademy.net 48732
```

(Secuencia: `2` Buy Flags → `1` flag trucha → `2386095` cantidad que desborda → `2` Buy Flags de nuevo → `2` flag 1337 → `1` confirmar compra)

### Flag

academy{m0n3y_bag5_A25Fa481}


### ¿Qué aprendí / qué usaría de nuevo?

Que los enteros con signo en C pueden desbordar y volverse negativos, y que eso se puede explotar cuando el código no valida límites antes de operar con la entrada del usuario. Es un patrón que sirve para reconocer en cualquier reto de "compra"/"balance" en C.

### ¿Me trabé en algo? ¿Cómo lo destrabé?

No me trabé en la lógica (el desborde era claro por la pista), pero calcular a mano el valor exacto de `number_flags` que produce el overflow deseado hubiera sido tedioso — lo resolví con un pequeño script en Python que simula la aritmética de 32 bits con signo y prueba valores hasta encontrar uno que cumpla la condición.