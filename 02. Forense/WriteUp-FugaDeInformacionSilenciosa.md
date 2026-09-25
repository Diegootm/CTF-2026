**Área:** Forense **Dificultad:** - **Plataforma:** CITC/CIDSI **Link del reto o Nombre:** Qué Rollo (250 pts) **Resuelto por:** Diego **Fecha:** 12/08 **Tiempo que tardé:** -

---

## ¿Qué pista/detalle me hizo saber por dónde ir?

en el enunciado hablaba del protocolo dns
## Herramienta(s) que usé

- wireshark
- dcode
## Pasos (solo lo esencial, tipo lista)

- entre a wireshark con el pcapng que nos dio
- filtre por dns
- dentro del primero me aparecio esto
  dentro de un query este archivo: RE5TX1R1bm4zbDFuZ19FeGZpbHRyNHRpMG5fRDN0M2N0M.exfil-data.net que me sono raro y en el otro dns este tambien  2Q.exfil-data.net: type A, class IN
- entonces uni lo siguiente : RE5TX1R1bm4zbDFuZ19FeGZpbHRyNHRpMG5fRDN0M2N0M2Q 
- lo meti a dcode y me detecto un base64 y al decodificar me salio esto: DNS_Tunn3l1ng_Exfiltr4ti0n_D3t3ct3d entonces lo lleve a md5 y lo entregue 
## Comando(s) o payload clave (si aplica)


## Flag

`cidsi{514ced0cecf02e505dd335043fd6e1e4}`
DNS_Tunn3l1ng_Exfiltr4ti0n_D3t3ct3d
## ¿Qué aprendí / qué usaría de nuevo?



## ¿Me trabé en algo? ¿Cómo lo destrabé?



---

