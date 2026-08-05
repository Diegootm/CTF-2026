**Área:** Miscelánea / General Skills **Dificultad:** Fácil **Plataforma:** picoCTF 2026 **Link del reto o Nombre:** MY GIT **Resuelto por:** Diego **Fecha:** - **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

El README del repo clonado decía: _"Only flag.txt pushed by `root:root@picoctf` will be updated with the flag."_ Eso era la pista completa: el servidor de Git tiene un hook personalizado que revisa el **autor y el committer** del commit que subes, y solo entrega la flag si ambos campos dicen exactamente `root` / `root@picoctf` — sin importar que ese no sea tu usuario real. Git no verifica esos campos contra ninguna identidad real por defecto, así que se pueden falsificar libremente.

## Herramienta(s) que usé

`git` (clon por SSH, configuración local de identidad, commit y push).

## Pasos (solo lo esencial, tipo lista)

- Cloné el repo con el comando y contraseña que dio el reto:
    
    ```
    git clone ssh://git@foggy-cliff.picoctf.net:<puerto>/git/challenge.git
    ```
    
- Leí el `README.md`, que explicaba la regla del servidor (autor/committer deben ser `root <root@picoctf>`)
- Configuré la identidad de Git **local a este repo** (no la global) como `root` / `root@picoctf`
- Creé un archivo `flag.txt` con cualquier contenido
- Agregué y comiteé el archivo — como la identidad ya estaba configurada antes del commit, tanto el autor como el committer quedaron correctos desde el inicio
- Verifiqué con `git log` que ambos campos (autor y committer) decían `root <root@picoctf>` antes de pushear
- Hice `git push` — la flag apareció **directo en la respuesta del servidor remoto**, en la salida del propio comando `push` (el hook del servidor la imprime ahí, no la escribe de vuelta al archivo `flag.txt` del repo)

## Comando(s) o payload clave (si aplica)

```bash
git clone ssh://git@foggy-cliff.picoctf.net:<puerto>/git/challenge.git
cd challenge

git config user.name "root"
git config user.email "root@picoctf"

echo "dame la flag" > flag.txt
git add flag.txt
git commit -m "flag"

# verificar antes de pushear
git log -1 --format="Autor: %an <%ae>%nCommitter: %cn <%ce>"

git push
```

Salida del servidor al hacer push correctamente:

```
remote: Author matched and flag.txt found in commit...
remote: Congratulations! You have successfully impersonated the root user
remote: Here's your flag: picoCTF{...}
```

## Flag

`picoCTF{1mp3rs0n4t4_g17_345y_cd8540cd}`

## ¿Qué aprendí / qué usaría de nuevo?

`git config user.name` / `git config user.email` no verifican en absoluto que esos datos correspondan a un usuario real — cualquiera puede firmar un commit como quien quiera (por eso commits firmados sin GPG no son prueba de identidad). Además, aprendí que la flag no siempre aparece en un archivo del repo: en retos con un servidor Git personalizado, hay que **leer con atención la salida completa del comando `git push`**, porque el hook del lado del servidor puede imprimir la respuesta ahí mismo en vez de modificar el repo.

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Sí — al principio usé `git commit --amend --reset-author` sobre un commit que ya había hecho con mi identidad real, y aunque el autor quedó corregido, tardé en verificar también el campo _committer_ (que es distinto del autor y no se corrige solo con `--reset-author`). Además, la instancia del reto expiró a mitad de la prueba y hubo que clonar de nuevo con un puerto distinto. Al final, lo más simple fue configurar la identidad **antes** de crear el commit (en vez de corregirlo después), lo cual evitó el problema del committer por completo. También me confundí pensando que la flag debía aparecer en `flag.txt` tras un `git pull`, cuando en realidad ya había salido impresa en la respuesta del `git push` original.

---

_Tip: llenen esto apenas resuelvan, con la solución fresca. Si no hay tiempo, al menos completen Área, Herramienta, Flag y "Qué aprendí" — el resto es opcional._