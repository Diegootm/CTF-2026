Writeup: [Bloqueador de bits -1]
Área: Forence Dificultad: Medio Plataforma: CyLab Link del reto o Nombre: [Bloqueador de bits -1]
Resuelto por: Axel Fecha: 09/08/26 Tiempo que tardé: unos 10 min

¿Qué pista/detalle me hizo saber por dónde ir?
con este comando bdeinfo basicamente identificas la mayoria del problema

Herramienta(s) que usé
varios comandos que los pongo enla lista
Pasos (solo lo esencial, tipo lista)
- Identificación del archivo: bitlocker-1.dd es una imagen de disco (100 MB) con firma -FVE-FS- en el MBR → volumen cifrado con BitLocker.
- Inspección con bdeinfo (libbde-utils): reveló cifrado AES-CBC 128-bit y dos protectores de clave: una "Recovery password" y una "Password" (la contraseña de usuario que Jacky puso, la débil).
- Extracción del hash: usé bitlocker2john.py (del repo oficial de John the Ripper) para parsear la metadata FVE y generar el hash crackeable en formato $bitlocker$1$... correspondiente al protector de contraseña de usuario.
- Cracking con hashcat (modo 22100 - BitLocker) contra el diccionario rockyou.txt:
   hashcat -m 22100 -a 0 hash.txt rockyou.txt

→ Contraseña recuperada: jacqueline

- Desencriptado del volumen con dislocker-file, usando la contraseña encontrada, generando una imagen NTFS plana descifrada.
- Extracción de archivos con sleuthkit (fls + icat, sin necesitar montar vía FUSE) → encontré flag.txt en la raíz del volumen.
Flag
picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}

¿Qué aprendí / qué usaría de nuevo?


¿Me trabé en algo? ¿Cómo lo destrabé?
