**Área:** Esteganografía **Dificultad:** Fácil **Plataforma:** CITC 2024 **Link del reto o Nombre:** New Hope (100 pts) **Resuelto por:** Diego **Fecha:** 01/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía que el mensaje "se estaba transmitiendo en un canal de comunicación abierto" y que "existen mensajes ocultos en estas transmisiones" — eso apuntaba a un mensaje escondido en el propio audio, no en algo que se pueda escuchar directamente. La técnica clásica para esto es esconder texto o una imagen dentro del **espectrograma** (la representación visual de las frecuencias a lo largo del tiempo).

## Herramienta(s) que usé

Audacity, usando la vista de **Espectrograma** (Spectrogram) en vez de la vista de onda normal.

## Pasos (solo lo esencial, tipo lista)

- Descargué el archivo de audio `New-Hope`
- Lo abrí en Audacity — a simple vista, en la forma de onda normal, no se veía nada raro
- Cambié la vista de la pista de "Waveform" a **"Spectrogram"** desde el menú desplegable de la pista
- Con la escala en **Bark**, el mensaje apareció directamente dibujado en las frecuencias del audio, formando texto legible
- El texto oculto en el espectrograma decía: `aw3rt1`
- Ese texto era la flag en texto plano; solo faltaba envolverlo en el formato pedido por la plataforma

## Comando(s) o payload clave (si aplica)

```
Audacity → clic en el menú desplegable de la pista ("New-Hope ⌄") → Spectrogram
Escala usada: Bark
```

## Flag

`cidsi{82c123f61188c03b029e8bbe4121c4e5}`

(el texto oculto en el espectrograma era `aw3rt1`, que se convierte al hash de arriba para el formato de flag del reto)

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un reto de esteganografía es un archivo de audio y el enunciado insinúa "mensajes ocultos en transmisiones", lo primero que hay que probar es la vista de **espectrograma** en Audacity — es la forma más común de esconder texto/imágenes en audio, y no requiere herramientas complicadas, solo cambiar el tipo de vista de la pista. La escala **Bark** en particular ayudó a que el texto se viera nítido y legible.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

---

