
**Área:** Stego **Dificultad:** facil **Plataforma:**  CIDSI **Link del reto o Nombre:** Paisaje **Resuelto por:** Xavi **Fecha:**30/07/2026  **Tiempo que tardé:** 15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

el audio no tenia nd oculto pero en sus metadatos puede que si 
## Herramienta(s) que usé

- exiftool
- unzip
- script bash
- strings
## Pasos (solo lo esencial, tipo lista)

- primero revise con un exiftool el turtles.pm3 y me salio algo raro:
 Album                           : agetic789
Txxx                            : isomiso2avc1mp41
- con esto me quede y ya no revise mas
- intente descomprimir el zip y me pedia contrasenia e intente con uno de estos datos de los metadatos y dio con el agetic789 que era su contrasenia
- dentro tenia archivos que resultaron ser jpg y eran imagenes y no tenia nd 
- use el comando 
```bash
xxd 1 | head
strings 1 | head 
```
-  y note que tenia hexadeciimales que era raro que haya y eso en todas la iamgens 
## Comando(s) o payload clave (si aplica)
hice un script para sacar de todas la imagens 
```bash
#!/bin/bash

#sacar el hexadecimales
for i in $(seq 1 14); do
strings "$i" | grep -aoE '0x[0-9a-fA-F]{2,}' | head -n $i

done
```

como resutlado 
0x43
0x63
0x7b
0x65
0x64
0x61
0x34
0x61
0x62
0x35
0x39
0x31
0x35
0x62
0x37
0x31
0x65
0x34
0x35
0x39
0x61
0x36
0x36
0x36
0x62
0x37
0x39
0x32
0x33
0x62
0x65
0x34
0x36
0x61
0x33
0x7d

## Flag

citc{eda4ab5915b71e459a666b7923be46a3}

## ¿Qué aprendí / qué usaría de nuevo?

 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

