# Para investigar después — Esteganografía

- **Esteganografía en video**: los mismos principios de LSB e imágenes se pueden aplicar frame por frame en un video — un área menos común en CTF, pero que aparece en retos avanzados.
  https://www.aperisolve.com/ (algunas herramientas de la suite manejan también frames de video)

- **Esteganografía en texto (whitespace steganography)**: ocultar información usando espacios, tabulaciones o caracteres invisibles dentro de un documento de texto.
  https://en.wikipedia.org/wiki/Whitespace_(programming_language) — no es esteganografía en sí, pero ayuda a entender cómo el whitespace puede "cargar" información

- **PNG chunks personalizados**: el formato PNG permite chunks (bloques de datos) personalizados que no todos los visores muestran — investigar la estructura interna de un PNG con un editor hexadecimal puede revelar chunks con datos ocultos que binwalk/zsteg no siempre detectan.
  https://en.wikipedia.org/wiki/PNG#File_format

- **Esteganografía en PDF**: capas ocultas, texto blanco sobre blanco, objetos superpuestos — técnicas específicas del formato PDF.
  https://learn.cylabacademy.org/ (buscar en la biblioteca por "steganography" y filtrar por PDF)
