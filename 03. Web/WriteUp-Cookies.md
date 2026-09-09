Área: Explotation web Dificultad: Fácil Plataforma: picoCTF 2025 (vía CyLab Academy) Link del reto o Nombre: Cookies Resuelto por: Axel Fecha: 8/09 Tiempo que tardé: 25 min

¿Qué pista/detalle me hizo saber por dónde ir?
El ejercicio me da una instancia una pagina para inspecciinar como el ejercicio era de cookies y la pagina especificamnete te decia que metas el nombre de una para que te 
direccione a otro lado, eso fue la clave por que al poner el nombre de cualquier galleta el name se asigna con un valor y de eso trataba el ejercicio una vez estes en INspecionar con la tecla F12 
Herramienta(s) que usé
El inspector de la pagina

Pasos (solo lo esencial, tipo lista)
- Abrir el inspector de la pagina y ir ala parte de aplicacion, despues buscar storage y cookie 
- Una vez en cookie te aparece una tablita ahi se ve la parte de name y se puede cambair lo dificil de este reto es ir cambaindo el valor de name primero del 0 al 20
- E n el transcurso de ir probando los numeros y darle F5 para recargar la pagina en u punto te va a mostrar la falg con el valor name = 18 y te muestra la flag en pantalla
Comando(s) o payload clave (si aplica)
Flag
picoCTF{3v3ry1_l0v3s_c00k135_a4dadb49}

¿Que aprendí / qué usaría de nuevo?
Pues solo revisar bien el codigo ya que se encuentran pistas casi siempre en el codigo de la pagina

¿Me trabé en algo? ¿Cómo lo destrabé?
Al principio si mas que todo por que es uno de los primeros ejercicios que resuelve de  web
