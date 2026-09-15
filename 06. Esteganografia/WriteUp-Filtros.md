**Área:** Stego **Dificultad:** medio **Plataforma:**  CIDSI **Link del reto o Nombre:** ##  Filtros **Resuelto por:** Xavi **Fecha:** 14/09/2026  **Tiempo que tardé:** 30 min

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

Al revisar cada imagen con exiftool me di cuenta que habia un apartado del artista tenia un valor hexadecimal en cada imagen entonces eso me hizo sospechar asi junte todo y halle la flag 
## Herramienta(s) que usé

- `file`, 7z, cyberchef
## Pasos (solo lo esencial, tipo lista)

- primero trate de buscar informacion en el zip el cual no encontre nada entonces tuve que buscar en los metadatos
- entonces me di cuenta que iban cambiando enotnces lo uni todo dandome directamente la flag 
- solo habia que juntar los hexadecimales 
## Comando(s) o payload clave (si aplica)

```python
for i in $(seq 1 38); do
  exiftool -Artist -s3 "$i"
done
```

## Flag

citc{16cffa7b89682e0d784c246ec78eacf5}

## ¿Qué aprendí / qué usaría de nuevo?

Que a veces los enuncaidos son para distraer y que el conjunto de datos puede dar la flag ya que estaba buscando la imagen que contenga la flag pero a veces la flag puede estar en mas de un archivo

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Pues me trabe al estar buscando solo las imagenes que tenian filtros pensando que podria estar en una de esas pero me equiovque y tuve que buscar otras cosas para darme cuenta de que la flag estaba en el conjunto de archivos no solo en uno 