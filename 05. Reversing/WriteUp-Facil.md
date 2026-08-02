**Área:** Exploiting / Reversing **Dificultad:** Fácil **Plataforma:** CITC 2024 **Link del reto o Nombre:** Facil (200 pts) **Resuelto por:** Diego **Fecha:** 01-02/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El enunciado decía: _"Las cosas no son como parecen... deberás revisar un poco más. Las condiciones deben saltarse."_ Eso ya adelantaba dos cosas: (1) lo que el programa muestra a simple vista no es la respuesta real, y (2) hay que modificar/saltarse un salto condicional (`jz`/`jne`) del ensamblador — es decir, un reto clásico de **parchar un binario** (patch the binary), no de explotar una vulnerabilidad de memoria.

## Herramienta(s) que usé

- `file`, `xxd`, `exiftool`, `binwalk` (identificación inicial del binario)
- `strings` (para encontrar pistas en el texto embebido)
- `objdump -d -M intel` (desensamblado completo)
- `readelf -S` (para mapear direcciones virtuales a offsets reales del archivo)
- `python3` (para escribir el parche binario directamente, sin radare2)

## Pasos (solo lo esencial, tipo lista)

### 1. Identificación del archivo

`file` confirmó que era un ejecutable ELF de 32 bits, estático (compilado con toda la libc adentro, por eso pesaba 2.4 MB). `binwalk` mostró varios falsos positivos (rutas Unix, "ESP32") que son solo strings normales de la libc estática — no eran pistas reales.

### 2. Correr el binario a ciegas

```bash
./Facil
./Facil contrasenia
./Facil -h
```

Sin importar el argumento (o sin ninguno), siempre imprimía lo mismo:

```
La flag es: cidsi{oh_no,me_encontraste!}
```

Esto ya olía a trampa — "oh no, me encontraste" es una burla, no una flag real.

### 3. Buscar pistas con `strings`

```bash
strings Facil | grep -i -E "flag|pass|correct"
```

Ahí aparecieron dos símbolos C++ (nombres "desmangleados" de funciones) clave:

```
_ZN7MyClass18buildFlagVerdaderaEv                                  -> MyClass::buildFlagVerdadera()
_ZN7MyClass12buildTheFLagENSt7__cxx1112basic_stringIcSt...          -> MyClass::buildTheFLag(string)
```

Es decir, el binario tiene **dos funciones distintas**: una construye "la flag" (la falsa, que se ve siempre) y otra construye la "flag VERDADERA", que nunca se estaba llamando.

### 4. Desensamblar y encontrar la lógica

Con `objdump -d -M intel Facil` ubicamos `main` y la función `buildTheFLag`. En `main`, el programa arma un string **fijo, hardcodeado en el binario**, y se lo pasa a `buildTheFLag` — confirmando por qué daba igual qué escribiéramos en la terminal: **nunca usa el input del usuario**, todo el "input" que compara ya viene precocinado dentro del propio ejecutable.

Dentro de `buildTheFLag`, encontramos la comparación clave:

```asm
call   operator!=(string, string)   ; compara dos strings fijos
test   al,al
je     804d6a7                      ; si son IGUALES -> imprime la flag FALSA
                                     ; si no, cae aquí abajo:
call   buildFlagVerdadera           ; -> construye la flag REAL
```

Como ambos strings comparados son constantes del binario, esa comparación **siempre** da "iguales", así que siempre salta hacia la flag falsa. La única forma de llegar a `buildFlagVerdadera` es anular ese salto.

### 5. Parchar el binario

La instrucción a anular está en la dirección `0x0804d695` (2 bytes: `74 10`, el opcode de `je`). Como esa dirección es virtual (de cuando el programa corre en memoria) y no coincide directamente con la posición dentro del archivo, usamos `readelf -S Facil` para ubicar dónde vive la sección `.text` tanto en memoria como en el archivo, y así calcular el offset real.

Con un script de Python (sin necesidad de radare2, que no estaba disponible en los repos), se escribió directo al archivo, reemplazando esos 2 bytes (`74 10` → `90 90`, dos `nop`) para que el salto nunca ocurra y el programa siempre caiga en la llamada a `buildFlagVerdadera`.

### 6. Correr el binario parchado

```bash
chmod +x Facil
./Facil
```

Esta vez sí imprimió la flag real.

## Comando(s) o payload clave (si aplica)

```bash
# Mapear direccion virtual a offset real del archivo
readelf -S Facil | grep -A1 "\.text"

# Parche con python (reemplaza 74 10 por 90 90 en el offset calculado)
# (ver script patch.py del writeup para el detalle completo)
```

Fragmento clave del ensamblador que se modificó:

```asm
804d693: test   al,al
804d695: je     804d6a7    <- ESTA instrucción (2 bytes: 74 10) se convirtió en NOP NOP (90 90)
804d697: call   buildFlagVerdadera
```

## Flag

`no_Er4_TaN_facil1337`
y con el formato de cidsi

## ¿Qué aprendí / qué usaría de nuevo?

Cuando un binario siempre da la misma respuesta sin importar el input que le des, es una señal fuerte de que **no está usando tu input en absoluto** — hay que revisar `main` en el desensamblado para confirmar de dónde saca realmente los datos que compara. Además, los nombres de función en C++ (aunque estén "mangled" como `_ZN7MyClass18build...`) casi siempre sobreviven en `strings` y pueden delatar la lógica del programa (en este caso, literalmente había una función llamada "flag verdadera" separada de la función normal). Para parchar un binario sin `radare2`, `readelf -S` + un script de Python que calcule el offset real (VA - dirección de sección + offset de sección) es una alternativa simple y confiable.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí, dos cosas: (1) al principio pensé que había que darle la contraseña correcta por línea de comandos, hasta que el desensamblado de `main` mostró que el programa ni siquiera lee `argv` — arma un string fijo internamente, así que ningún input externo cambia el resultado. (2) `radare2` no estaba disponible en los repositorios de `apt` de mi Debian; lo resolví parchando el binario directamente con Python, calculando el offset real del archivo a partir de la dirección virtual con `readelf -S`.

---

