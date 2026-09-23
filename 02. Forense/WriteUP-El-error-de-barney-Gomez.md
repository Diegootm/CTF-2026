**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** el errro de barney gomez **Resuelto por:** Diego **Fecha:** 12/08 **Tiempo que tardé:** 5 minutos


---

## ¿Qué pista/detalle me hizo saber por dónde ir?

En el enunciado hablaba de una sesion y pense en un formulario y en metodo POST

## Herramienta(s) que usé

Wireshark (filtros de visualización para el protocolo http)

## Pasos (solo lo esencial, tipo lista)

- Abrí `sprinfield.pcapng` con Wireshark
- Revisé `Estadísticas → Jerarquía de protocolo` — confirmó tráfico HTTP corriendo sobre el puerto lo que me confirmo que habia un formulario en dentro de la seccion de hipertexto
- Filtré primero con `http.request.method == "POST"`
- solo me aparecio una sola opcion y dentro de la seccion de html encontre la flag: flag{No_lloren_por_mi_ya_estoy_muerto}  


## Comando(s) o payload clave (si aplica)

Filtro de Wireshark usado para encontrar la petición clave:

```
http.request.method == "POST" 
```


## Flag

`cidsi{ae9819895a70e724f1408d7b62a50ecd}`

lo que se encontro
No_lloren_por_mi_ya_estoy_muerto
## ¿Qué aprendí / qué usaría de nuevo?



## ¿Me trabé en algo? ¿Cómo lo destrabé?



---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._