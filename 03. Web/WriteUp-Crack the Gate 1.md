

**Área:** Explotation web **Dificultad:** Fácil **Plataforma:** picoCTF 2025 (vía CyLab Academy) **Link del reto o Nombre:** Crack the Gate 1 **Resuelto por:** Xavi **Fecha:** 26/07 **Tiempo que tardé:** ~15 min

---
## ¿Qué pista/detalle me hizo saber por dónde ir?

desde el inicio el nombre crack the gate ya daba una pista pues se refiere a fuerza bruta entonces ya intente hacer fuerza bruta pero luego encontre un bypass que ya hizo mas facil el problema
## Herramienta(s) que usé

curl
dcode rot13

## Pasos (solo lo esencial, tipo lista)

- lo primero fue intentar cualquier contraseña junto con el correo que te da el reto pero no sirive mucho al inicio
- Una vez revisando el codigo HTML se puede ver un mensaje encriptado que decia  NOTE: Jack - temporary bypass: use header "X-Dev-Access: yes" a lo cual use curl junto con este acceso 
- Una vez ya con esa pista simplemente mande un comando con curl a lo cual me devolvio la flag 

## Comando(s) o payload clave (si aplica)

```
curl -H "X-Dev-Access: yes" -H "Content-Type: application/json" \
  -X POST "http://amiable-citadel.picoctf.net:50314/login" \
  -d '{"email":"ctf-player@picoctf.org","password":"cualquiercosa"}'
```

## Flag

picoCTF{brut4_f0rc4_0d39383f}

## ¿Que aprendí / qué usaría de nuevo?

Bueno entendi que se puede tener acceso con una bypass lo cual puede ser peligroso para cualquier desarollador pero para ctf ps esta bien

## ¿Me trabé en algo? ¿Cómo lo destrabé?

Si pues no encontraba nada al inicoi pero descubri un texto cifrado dentro del html lo cual ya me dio la respuesta instantanemente haciendo el probelam mas sencillo