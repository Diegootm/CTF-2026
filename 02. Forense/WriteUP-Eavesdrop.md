# Writeup: [#Eavesdrop]

**Área:** Forence **Dificultad:** Medio **Plataforma:** CyLab  **Link del reto o Nombre:** [ Eavesdrop]      
**Resuelto por:** Axel **Fecha:** 10/09/2026**Tiempo que tardé:** unos 20 min 

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

El ejercico daba un archivo pcap en donde hay varias cosas que revisar en este caso si encontrabas El directorio TCP correcto te daba una conversacion en donde te dab algo importantre esto: 
*sigh* openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123  

## Herramienta(s) que usé

- wireshark(clave) 
- Despues use tshark el cual lo instalas con: suso apt install tshark. Esta herramienta te sirve para ejercicio de este estilo que tienen mensajes ocultos en este caso un  .txt escondido  
## Pasos (solo lo esencial, tipo lista)
- Primero buscar en wireshark el directorio que te de una pista clara una ves lo encuentrs te dara:  *sigh* openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123  
- despues de eso abrimos la terminal y ejecutamos esto paso a paso: 
  **1. Identificar las conversaciones TCP**  
Con Wireshark o `tshark` inspeccioné las conversaciones TCP del pcap:

```
tshark -r capture.pcap -q -z conv,tcp
```

Esto mostró tres flujos: uno hacia el puerto **9001** (chat) y otro hacia el puerto **9002** (transferencia de archivo), ambos hablando con `10.0.2.4`.

**2. Seguir el stream del chat (puerto 9001)**

```
tshark -r capture.pcap -q -z follow,tcp,ascii,0
```

En Wireshark equivale a: click derecho sobre un paquete del stream → _Follow → TCP Stream_.

La conversación revela que los dos usuarios hablan de un archivo cifrado con **openssl des3**, y uno de ellos escribe el comando completo, incluida la contraseña:

```
openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
```

**3. Seguir el stream de la transferencia de archivo (puerto 9002)**

```
tshark -r capture.pcap -q -z follow,tcp,raw,2
```

Esto entrega los bytes en hexadecimal del archivo transferido. El contenido comienza con `53616c7465645f5f` que en ASCII es `Salted__`, la firma característica de un archivo cifrado con OpenSSL (formato salted).

**4. Reconstruir el archivo binario**  
Convertí el hexdump a bytes reales y lo guardé como `file.des3`:

python

```python
data = "53616c7465645f5f3c4b26e8b8f91e2c4af8031cfaf5f8f16fd40c25d40314e6497b39375808aba186f48da42eefa895"
with open("file.des3", "wb") as f:
    f.write(bytes.fromhex(data))
```

**5. Desencriptar usando la contraseña filtrada en el chat**

```
openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
```
**6. Leer el resultado**

```
cat file.txt
``` 
## Comando(s) o payload clave (si aplica)


## Flag

picoCTF{nc_73115_411_0ee7267a}

## ¿Qué aprendí / qué usaría de nuevo?

Que este tipo d eejercicios se puede complicar sino sabes lo necesario por ejemplo tshark te simplificaba el trabajo si sabias de la herramienta
## ¿Me trabé en algo? ¿Cómo lo destrabé?

si al ser varios directorios en wireshark revisar uno por uno quita tiempo en competencia
