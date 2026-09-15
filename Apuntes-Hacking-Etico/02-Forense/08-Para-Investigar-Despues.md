# Para investigar después — Forense

- **Registro de Windows (Registry Forensics)**: el registro de Windows guarda muchísima información sobre actividad del usuario (programas ejecutados, USBs conectados, etc.). Útil si aparecen retos con imágenes de disco Windows.
  https://learn.cylabacademy.org/ (buscar en la biblioteca por "registry")

- **Análisis de logs de sistema (Linux/Windows Event Logs)**: reconstruir actividad a partir de archivos de log del sistema operativo.
  https://www.sans.org/white-papers/ (buscar white papers de SANS sobre log forensics)

- **Esteganografía dentro de archivos forenses**: a veces un reto de "forense" en realidad esconde un paso de esteganografía (ver carpeta [06-Esteganografia](Apuntes-Hacking-Etico/06-Esteganografia/00-Indice.md)) — no son excluyentes.

- **Carving de archivos manual con editores hexadecimales**: para cuando `binwalk`/`foremost` no detectan el archivo embebido automáticamente y hay que buscar la firma a mano.
  https://filesig.search.org/

- **Casos prácticos de Forense en CyLab Academy** (biblioteca de la plataforma usada para practicar):
  https://learn.cylabacademy.org/library/507?category=4&page=1
  https://learn.cylabacademy.org/library/456?category=4&page=1
  https://learn.cylabacademy.org/library/264?page=1&tag=12
