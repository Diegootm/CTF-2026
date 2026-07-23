
**Área:** Cripto **Dificultad:** Medio **Plataforma:** picoMini by CMU-Africa (picoCTF) **Link del reto o Nombre:** Crack the Power **Resuelto por:** Diego **Fecha:** 23/07 **Tiempo que tardé:** ~10 min

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El nombre del reto, "Crack the Power", apuntaba directo al exponente (`e`). Al ver que `e = 20` era muy pequeño (en RSA normal suele ser 65537), sospeché de un ataque de exponente bajo: si el mensaje `m` es corto, `m^e` puede ser menor que `n`, y entonces nunca se llega a aplicar el módulo — el cifrado `c = m^e mod n` termina siendo simplemente `c = m^e`.

## Herramienta(s) que usé

Python con la librería `gmpy2` (para sacar raíces enteras exactas de números gigantes).

## Pasos (solo lo esencial, tipo lista)

- Identifiqué las 3 piezas del RSA: `n` (módulo), `e = 20` (exponente público), `c` (cifrado)
- Con `e` tan chico, planteé la hipótesis de que `m^e < n`, es decir que el módulo nunca se "activó"
- Calculé la raíz entera 20 de `c` con `gmpy2.iroot(c, e)`
- La función devolvió `exact = True`, confirmando que la raíz es exacta (no aproximada) → validó la hipótesis
- Convertí el número resultante (`m`) a bytes para obtener el texto plano

## Comando(s) o payload clave (si aplica)

```python
import gmpy2

n = ...  # módulo dado en el reto
e = 20
c = ...  # cifrado dado en el reto

root, exact = gmpy2.iroot(gmpy2.mpz(c), e)
assert exact  # confirma que m^e < n, sin reducción módulo n

m = int(root)
flag = m.to_bytes((m.bit_length() + 7) // 8, 'big')
print(flag)
```

## Flag

`picoCTF{t1ny_e_af0d7666}`

## ¿Qué aprendí / qué usaría de nuevo?

Cuando el exponente público `e` de RSA es muy chico y el mensaje es corto, no hace falta factorizar `n` ni romper RSA de verdad: basta con sacar la raíz entera e-ésima de `c` directamente, porque `m^e` nunca superó a `n` y el cifrado es "RSA sin módulo". Un exponente bajo (`e` pequeño, tipo 3, 5, 20...) siempre es una señal para sospechar de este ataque.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Los números son enormes (cientos de dígitos), así que sacar la raíz 20 con matemática normal de Python no es preciso ni rápido. Lo resolví usando `gmpy2.iroot()`, que calcula raíces enteras exactas de números arbitrariamente grandes y además indica si el resultado es exacto o no.
