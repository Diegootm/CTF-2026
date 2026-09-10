**Área:** Reversing/**Dificultad:** Fácil/**Plataforma:** CyLab/**Link del reto o Nombre:**Transformation/**Resuelto por:** Xavi/**Fecha:** 03/08/**Tiempo que tardé:** 10 min

**¿Qué pista/detalle me hizo saber por dónde ir?**  
el problema decia que habia una contraseña que estaba dentro del codigo entonces lo unico que hice fue revisar el codigo ya que el archivo era un .java entonces no fue tan dificl entender cual era la flag ya que no era un codigo muy largo
**Pasos (solo lo esencial, tipo lista)**

1. Aplicar un cat para revisar el contenido del archivo 
2. ejecutar el archivo con **javac SafeOpener.java**
3. Luego de ejecutar este comando entonces se genero un archivo sin la extension que me permitio ejecutar con  **java SafeOpener**
4. Despues de revisar el codigo encontre un cifrado base64 y lo desencripte .

**Comando(s) o payload clave**

javac SafeOpener.java # compila y genera SafeOpener.class
java SafeOpener # ejecuta (sin ./ ni extensión)

**Flag**  
`picoCTF{pl3as3_l3t_m3_1nt0_th3_saf3}`

**¿Qué aprendí / qué usaría de nuevo?**  
Que hay que revisar el codigo que tiene un archivo para poder encontrar alguna pista 

**¿Me trabé en algo? ¿Cómo lo destrabé?**  
En NADA jej