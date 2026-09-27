
**Área:** Stego **Dificultad:** facil **Plataforma:**  CIDSI **Link del reto o Nombre:** Paisaje **Resuelto por:** Xavi **Fecha:**27/09/2026  **Tiempo que tardé:** 15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

Pues la como siempre en stego hay que revisar todo lo que puede estar ocutlo pero lo que me hizo darme cuenta fue que decia que la palbra AGETIC me serviria mas adelante entonces use esa palabra como contraseña y ya practicamente se resolvio

## Herramienta(s) que usé

exiftool 
file
steghide
binwalk
## Pasos (solo lo esencial, tipo lista)

- Primero verifique se realmente sea un jpg o imagen lo cual resulto veridico
- Trate de seguir buscando y no encontre nada oculto entonces hice un exiftool
- En los metadatos estaba una pista que decia AGETIC
- aplique steghide con esa contraseña y me devolvio un txt el cual ya tenia la flag escondida dentro del texto 
- Decia usar las iniciales del área Centro de Gestión de Insidentes Informáticos de la AGETIC para poder ver la Flag ;)
- y daba este texto INVCGIIESTCGIIIGCGIIAPCGIIARCGIIASCGIIERMCGIIEJCGIIORYPRCGIIACTCGIIICCGIIACCGIIADCGIIADCGIIIA
- lugar donde esta la flag mezclada entre otros caracteres pues se quita los caracteres CGII y se encontrara la flag
## Comando(s) o payload clave (si aplica)

```
steghide extract -sf Topografia.jpg -p "AGETIC"
```

## Flag

cidsi{d58054ab9b51e1750e5c361aac27e8e0}

INVESTIGAPARASERMEJORYPRACTICACADADIA
## ¿Qué aprendí / qué usaría de nuevo?

Que siempre que nos den alguna palabra calve o alguna palabra es par despstar o bien puede ser alguna clave o contraseña para ayudarnos a descrifrar algo mas 

## ¿Me trabé en algo? ¿Cómo lo destrabé?

solo le di la vuelta a la palabra clave y halle la flag 
