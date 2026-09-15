# Para investigar después — Criptografía

Temas que no cubrimos a fondo en la teoría base, pero que pueden aparecer en retos medios/difíciles. Dejamos solo el link y de qué trata — investigar cuando haga falta.

- **Ataque de Padding Oracle (CBC)**: permite descifrar datos cifrados con AES-CBC explotando cómo un servidor responde a errores de "padding" mal formado. Muy común en retos de dificultad media-alta.
  https://en.wikipedia.org/wiki/Padding_oracle_attack

- **Curvas elípticas (ECC)**: criptografía asimétrica alternativa a RSA, cada vez más común. Los ataques son distintos a los de RSA.
  https://cryptohack.org/courses/ (curso "Elliptic Curves" de CryptoHack)

- **Ataques a JWT (JSON Web Tokens) mal firmados**: aunque es más un tema de Web, tiene una base criptográfica fuerte (algoritmos de firma, claves débiles).
  https://jwt.io/

- **Length Extension Attack**: permite falsificar un hash extendido (típicamente en MD5/SHA-1) sin conocer la clave secreta, si el hash se calculó de forma insegura.
  https://en.wikipedia.org/wiki/Length_extension_attack

- **Lattice attacks (LLL) para RSA**: ataques matemáticos más avanzados que exigen entender álgebra lineal, usados cuando fallan los ataques "clásicos" de RsaCtfTool.
  https://github.com/mimoo/RSA-and-LLL-attacks

- **Curso completo de CryptoHack**: si quieren profundizar en cripto moderna con retos progresivos, este es el mejor recurso gratuito que existe hoy.
  https://cryptohack.org/
