Sherlock es una herramienta OSINT que busca un mismo nombre de usuario en más de 400 redes sociales y plataformas a la vez, y te dice en cuáles existe una cuenta con ese username.

Este manual está basado en el repositorio oficial de Sherlock Project (v0.16.0).

---

## 0. Instalación

### 0.1 Instalar Docker (Debian/Ubuntu)

Si ya tienes Docker instalado (como en tu caso), puedes saltar a la sección 0.2.

```bash
# Actualizar paquetes
sudo apt update

# Instalar dependencias
sudo apt install -y ca-certificates curl gnupg

# Agregar la llave GPG oficial de Docker
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Agregar el repositorio de Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verificar instalación
sudo docker --version
```

**Opcional — correr Docker sin `sudo`:**

```bash
sudo usermod -aG docker $USER
```

Cierra sesión y vuelve a entrar (o reinicia) para que el cambio de grupo tome efecto.

### 0.2 Descargar la imagen oficial de Sherlock

El comando oficial del repo es este — Docker descarga la imagen automáticamente la primera vez que la corres:

```bash
docker run -it --rm sherlock/sherlock
```

Como lo corriste sin argumentos, solo te mostró el mensaje de uso (`usage: ...`) — eso confirma que la imagen ya está descargada y funcionando. Para usarla de verdad, hay que pasarle uno o más usernames (ver sección 1).

Puedes confirmar que la imagen quedó descargada con:

```bash
docker images | grep sherlock
```

### 0.3 (Alternativa) Instalar Sherlock sin Docker

Métodos oficiales, según el repo:

|Método|Notas|
|---|---|
|`pipx install sherlock-project`|`pip` o `uv` también sirven en vez de `pipx`|
|`docker run -it --rm sherlock/sherlock`|el que ya usaste|
|`dnf install sherlock-project`|Fedora/RHEL|

También hay paquetes de la comunidad para Debian (≥13), Ubuntu (≥22.10), Homebrew, Kali y BlackArch, pero no están mantenidos oficialmente por el proyecto. Nota: los paquetes de ParrotOS y Ubuntu 24.04 están reportados como rotos actualmente — se recomienda usar `pipx`/`pip`/`uv` o Docker en esos casos.

Con `pipx`/`pip`, el comando queda simplemente como `sherlock user123` (sin el prefijo `docker run -it --rm sherlock/sherlock`), y todas las opciones descritas en este manual son las mismas.

---

## 1. Uso básico

**Buscar un solo usuario:**

```bash
docker run -it --rm sherlock/sherlock xb3t0
```

**Buscar varios usuarios en una sola ejecución:**

```bash
docker run -it --rm sherlock/sherlock xb3t0 0xb3t0
```

Por defecto, Sherlock imprime en la terminal cada sitio revisado y si encontró o no una cuenta con ese username.

---

## 2. Guardar resultados en tu máquina (montar un volumen)

Como el contenedor se borra al terminar (`--rm`), cualquier archivo que Sherlock genere dentro del contenedor se pierde a menos que montes una carpeta local. Sherlock trabaja sobre el directorio `/app` dentro del contenedor, así que hay que mapear tu carpeta local ahí:

```bash
mkdir -p resultados
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 --txt --output xb3t0.txt
```

- `-v "$(pwd)/resultados:/app"` → conecta tu carpeta local `resultados` con `/app` dentro del contenedor
- `--txt` → habilita la creación del archivo de texto (no se genera por defecto)
- `--output xb3t0.txt` → nombre del archivo (solo válido con **un** usuario a la vez)

Al terminar, el archivo queda en `./resultados/xb3t0.txt` en tu máquina.

**Con varios usernames a la vez**, usa `--folderoutput` en vez de `--output`:

```bash
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 0xb3t0 --txt --folderoutput resultados_multiples
```

Esto crea un archivo `.txt` por cada username dentro de la subcarpeta `resultados_multiples`.

---

## 3. Exportar en otros formatos

```bash
# CSV
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 --csv

# Excel (xlsx)
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 --xlsx

# JSON (carga un data.json externo, no exporta resultados en json)
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 --json data.json
```

Nota: `--json` no es para exportar el resultado, sino para **cargar** un archivo `data.json` (local o una URL) con la lista de sitios a revisar — útil si quieres usar una lista personalizada o una versión más reciente que la incluida en la imagen.

---

## 4. Opciones útiles (lista real del `--help`)

|Opción|Qué hace|
|---|---|
|`--print-found`|Solo muestra los sitios donde SÍ encontró el usuario|
|`--print-all`|Muestra también los sitios donde NO lo encontró|
|`--timeout <segundos>`|Tiempo de espera por sitio (por defecto: 60s)|
|`--site <nombre_sitio>`|Limita la búsqueda a uno o más sitios específicos (repetir la opción para varios)|
|`--proxy <url>` / `-p`|Usa un proxy, ej. `socks5://127.0.0.1:1080`|
|`--nsfw`|Incluye sitios de contenido para adultos|
|`--browse` / `-b`|Abre automáticamente en el navegador los perfiles encontrados|
|`--txt`|Habilita la creación del archivo de texto de salida|
|`--csv`|Exporta resultados en CSV|
|`--xlsx`|Exporta resultados en Excel|
|`--no-color`|Desactiva colores en la terminal|
|`--verbose` / `-v` / `-d` / `--debug`|Muestra información extra de depuración|
|`--ignore-exclusions`|Ignora exclusiones predefinidas (puede dar más falsos positivos)|
|`--local` / `-l`|Fuerza el uso del archivo `data.json` local en vez de uno remoto|
|`--dump-response`|Muestra la respuesta HTTP cruda de cada sitio (para depurar)|

Ejemplo combinando varias:

```bash
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 --print-found --timeout 10 --txt --output xb3t0.txt
```

---

## 5. Buscar solo en un sitio específico

```bash
docker run -it --rm sherlock/sherlock xb3t0 --site Reddit --site Instagram
```

---

## 6. Interpretando la salida

```
[+] Reddit: https://reddit.com/user/xb3t0        <- encontrado
[-] Instagram: Not Found!                         <- no existe con ese username
```

- `[+]` = existe una cuenta con ese username en ese sitio
- `[-]` = no se encontró
- Un `[+]` **no confirma que sea la misma persona** — siempre hay que entrar al perfil y verificar (foto, bio, actividad) que coincide con quien buscas.

---

## 7. Comando recomendado para tu caso

```bash
mkdir -p resultados
docker run -it --rm -v "$(pwd)/resultados:/app" sherlock/sherlock xb3t0 --print-found --txt --output xb3t0.txt
```

Luego revisa `./resultados/xb3t0.txt` para tener la lista completa de plataformas donde tiene cuenta, y entra una por una a buscar la mención que necesitas.