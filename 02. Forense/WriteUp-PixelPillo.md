**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** pixel Pillo **Resuelto por:** Diego **Fecha:** 12/08 **Tiempo que tardé:** 10 minutos

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

la pista me dijo que tiene algo oculto

## Herramienta(s) que usé

- strings
- dcode

## Pasos (solo lo esencial, tipo lista)

- le hice un exiftool pero no me dio nada
- le hice un strings ImaGeN_CITC.jpg y al final de todo me salio un secret Start el cual tenia esto: 
`95Wc2l2Xul3XttSeldGcfB3K5V2ZwtHcnZHc`
- el cual estaba encriptado y lo lleve a decode
- probe base 64 fuerza bruta y me salio esto 
pvgp{pgey+p_pgey+m_yn_ivqn} y me parecio que segui encriptado
-  entonces lo lleve otra vez y estaba rotado y con ROT me salio |   |citc{ctrl+c_ctrl+z_la_vida}
- lleve a md5 lo siguiente ctrl+c_ctrl+z_la_vida y reclame la flag
## Comando(s) o payload clave (si aplica)


## Flag

citc{1ca33e5c64d26f9a5856fd2aa962b804}

## ¿Qué aprendí / qué usaría de nuevo?



## ¿Me trabé en algo? ¿Cómo lo destrabé?



---

