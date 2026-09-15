Area: Cripto Dificultad: Medio Plataforma: Cylab **Link del reto o Nombre: ## ClusterRSA **Resuelto por : Axel Fecha: 7/09/26 Tiempo que tardé: 30 minutos

¿Qué pista/detalle me hizo saber por dónde ir?
El problema te da un archivo de texto el cual te da Este problema se basa en una variante de RSA llamada Multi-Prime RSA. El primer consejo nos indica que en lugar de usar solo dos números primos ($n = p \times q$), la clave pública $n$ se compuso multiplicando más de dos números primos.

Con los 4 factores primos:
9671406556917033398439721
9671406556917033398454847
9671406556917033398314601
9671406556917033397931773
Calculé φ(n) = (p₁-1)(p₂-1)(p₃-1)(p₄-1)
Obtuve la clave privada d = e⁻¹ mod φ(n)
Desciframos: pt = ct^d mod n
Herramienta(s) que usé
gmp-ecm y pari-gp están disponibles. Instalemos gmp-ecm que es excelente para factorizar números con múltiples factores primos medianos.
- Tambien un poco de conocimiento sobre  RSA que normalmemte usa el productode 2 primos pero en estre caso son 4 


Pasos (solo lo esencial, tipo lista)
- Si realizamos la factorizacion exacta y real del numero n que proporciona el texto: 
$$n = 8749002899132047699790752490331099938058737706735201354674975134719667510377522805717156720453193651$$
- Con 4 factores más pequeños en lugar de 2 grandes, el algoritmo ECM (Elliptic Curve Method) de gmp-ecm puede encontrarlos rápidamente (Pollard rho normal es mucho más lento para este caso).
- Con los 4 factores primos:
9671406556917033398439721
9671406556917033398454847
9671406556917033398314601
9671406556917033397931773
- Calculé φ(n) = (p₁-1)(p₂-1)(p₃-1)(p₄-1)
- Obtuve la clave privada d = e⁻¹ mod φ(n)
- Desciframos: pt = ct^d mod n
Comando(s) o payload clave (si aplica)
Flag
picoCTF{mul71_rsa_bcbee34d}

¿Qué aprendí / qué usaría de nuevo?
conocimiento general sobre RSA y entender bien el ejercicio 

¿Me trabé en algo? ¿Cómo lo destrabé?
Al inicio no entendi por domdne empezar pero despued de investigar se llego a una respuesta
