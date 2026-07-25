

**Área:** Cripto **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Mod26 **Resuelto por:** Xavi **Fecha:** 23/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

Los valores entregados (`g`, `p`, `A`, `b`, `enc`) son el patrón clásico de un intercambio de claves Diffie-Hellman: un generador, un primo grande, una clave pública, y un secreto propio. Eso indicaba que había que calcular el secreto compartido antes de poder desencriptar nada.
## Herramienta(s) que usé

exiftool (en una imagen donde estaba la clave)
openssl (para abrir el archivo )

## Pasos (solo lo esencial, tipo lista)

- Inspeccionar el archivo cuando se dijo que se debia buscar la clave privada revise en la imagen .jpg para revisar si habia algo y estaba una clave .
- Guardar la clave en un archivo `private.pem`.
- Desencriptar directamente con OpenSSL usando la clave privada.
## Comando(s) o payload clave (si aplica)

```
openssl pkeyutl -decrypt -inkey private.pem -in flag.enc -out flag.txt
cat flag.txt
```

## Flag

picoCTF{rs4_k3y_1n_1mg_3a1b0454}

## ¿Que aprendí / qué usaría de nuevo?

Bueno aprendi que el algoritmo RSA debe tener una clave publica y privada para poder desencriptar el contenido 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

No sabia como usar la clave privada pero luego me di cuenta que era con openssl
