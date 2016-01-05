---           
layout: post
title: La distribución de Poisson explicada fácil
date: 2014-09-19 17:54:00 UTC
updated: 2014-09-19 17:54:00 UTC
comments: false
categories: distribuciones estadistica poisson
---

Esta entrada está basada en los vídeos de Khan Academy referentes a la **distribución de Poisson**.

Vamos a modelar una distribución que nos de la probabilidad del número de coches que pasan en una hora (**intervalo de tiempo**). También podríamos intentar modelar el número de llamadas que llega a una centralita en el **intervalo** de media hora o el número de paquetes que llegan a un router en el **intervalo** de un segundo.

Entonces, sea la variable aleatoria *X=número de coches que pasan en el **intervalo** de una hora*.

Antes de nada, tenemos que hacer dos presunciones:

1. Ninguna hora es diferente de cualquier otra (p.e. no consideramos que las horas nocturnas sean diferentes de la diurnas o fines de semana).
2. No hay manera de que el número de coches que pasen en una hora afecte al número de coches que pasa en otra hora (los intervalos son independientes).

Lo que vamos a hacer es intentar modelar esa probabilidad usando la distribución binomial, usando un pequeño truco y llevando al límite uno de sus parámetros.

Recordemos que la función de probabilidad de una distribución binomial es:

$$P(X=k)=\binom{n}{k} p^k (1−p)^{n−k}$$

Donde $P(X=k)$ son las probabilidades de obtener exactamente $k$ aciertos, $n$ es el número de experimentos, $\binom{n}{k}$ son las combinaciones de resultados del experimento que dan exactamente $k$ aciertos en cualquier orden y $p$ es la probabilidad de acertar.

> *Ejemplo1:* La probabilidad de que un alumno suspenda es del 30%. Escogemos 20 alumnos al azar, ¿Cuál es la probabilidad de que haya exactamente 4 alumnos suspendidos?.
<hr/>
Es una distribución binomial porque sólo tenemos dos estados posibles: suspendido o aprobado. Sus parámetros son: probablidad de acierto 0.3 y número de experimentos 20; p=0.3,n=20, o sea $Bin(n,p)\to Bin(20,0.3)$ que da $$P(X=4)=\binom{20}{4}(0.3)^4 (0.7)^16=0.13$$.

El mejor estimador es la esperanza $$E(X)=\lambda$$

por ejemplo, $9 \frac{coches}{hora}$ o $loquesea \frac{eventos}{longitud−intervalo−tiempo}$.

En una *distribución binomial* la esperanza es $E(X)=\lambda=n\,p$, o sea, el numero de experimentos x probabilidad de éxito del experimento.

Pensemos un poco en ello: Supongamos que lanzo 10 tiros de baloncesto siendo la probabilidad de encestar (acierto) de cada uno de ellos del 40%. ¿Cuál es el número de canastas esperado?. Pues $E(X)=10×0.4=4$. 

También podríamos preguntarlo de otra manera: Siendo la media de aciertos de 4 cada 10 tiros, ¿Cuál es la probabilidad de encestar?. Pues si $\lambda =n \, p \to p=\frac{\lambda}{n}$ entonces $p=4/10=40%$. O sea, 
$$p=\frac{λ}{n} (1)$$

Ya con esto dicho, en nuestro ejemplo de los coches podríamos intentar modelar la función de probabilidad como una $Bin$,pero para ello necesitamos realizar experimentos y testear si son aciertos o fallos. La idea de [Poisson](https://en.wikipedia.org/wiki/Sim%C3%A9on_Denis_Poisson) fue coger nuestra hora y dividirla en 60 minutos, sabiendo que la media por hora es

$$λ \frac{coches}{hora}=\frac{60 minutos}{1 hora} \frac{λ coches}{60 minutos}$$

Y entonces decimos: Si en el minuto actual pasa un coche, lo doy como acierto y si no pasa lo doy como fallo, es decir:

1. El número de aciertos sería las veces que ha pasado un coche en el minuto considerado, y
2. El número de fallos las veces que en el minuto considerado 
  2.1 o bien no ha pasado ninguno 
  2.2 o bien han pasado más de uno


Así, en una primera aproximación, siendo $P(X=k)$ la probabilidad de que pasen exactamente $k$ coches (por ejemplo 4) en una hora, quedaría como $Bin(60,\frac{λ}{60})$:

$$P(X=k)=\binom{60}{k} \binom{\lambda}{60}^k \left(1−\frac{\lambda}{60}\right)^{60−k}$$

Donde vemos que que el número de experimentos n es 60, y la probabilidad de acertar p es $\frac{\lambda}{60}$, ver fórmula (1). El problema evidente aquí está en los casos en los que pasa más de un coche (o se recibe más de una llamada en una centralita) que se consideran como fallos. Para solucionarlo, podríamos mejorar la resolución temporal, haciendo que el experimento dure un segundo y realizando 3.600 pruebas ;-) Reduciendo más y más el intervalo llegaríamos a la expresión de la [distribución de Poisson](https://en.wikipedia.org/wiki/Poisson_distribution), pero esta sólo es en realidad una binomial en que el número de pruebas tiende a infinito y por lo tanto el intervalo de estas tiende a cero.

Para llegar a la expresión, primero recordamos:

La definición de e: 
limx→∞(1+ax)x=ea(1)
Y el desarrollo: 
x!(x−k)!=x(x−1)(x−2)...(x−k+1)(2)
que tiene k número de términos.
Por ejemplo: 
7!(7−2)!=7⋅6⋅5⋅4⋅3⋅2⋅15⋅4⋅3⋅2⋅1=7⋅(7−2+1)=7⋅6=42

7⋅6 tiene 2 términos.
Entonces: 
limn→∞(Bin(n,p))=(nk)pk(1−p)n−k=limn→∞x!(x−k)!k!λknk(1−λn)n(1−λn)−k
Ahora pasamos el k! partiendo a λk, y nk en su lugar, también desarrollamos x!(x−k)! y queda
limn→∞n(n−1)(n−2)...(n−k+1)nkλkk!(1−λn)n(1−λn)−k
En cuanto a limn→∞n(n−1)(n−2)...(n−k+1)nk, vemos que tiende a uno porque el grado del polinomio del numerador tiene que ser k debido a que hay k número de términos (3), entonces basta dividir numerador y denominador por nk.
En cuanto a λkk!, observamos que es una constante que no depende de n.
(1−λn)n tiende a uno debido a que λn tiende a cero.
Nos queda 
limn→∞(1−λn)−k=e−λ
por la definición (2) del número e.
O sea que la función de probabilidad de Poisson es un límite cuyo valor equivale a 
P(X=k)=λkk!e−λ
Ejemplo2: De media, pasan 9 coches en una hora. Suponiendo ciertas las presunciones 1 y 2: ¿Cuál es la probabilidad de que en una hora cualquiera pasen exactamente 2 coches?.
P(x=2)=922!e−9=0.05=5%
Ejemplo3: En un restaurante entran de media 2 clientes cada 3 minutos. Suponiendo ciertas las presunciones 1 y 2: ¿Cuál es la probabilidad de que entren cuatro o menos clientes en un periodo de nueve minutos?.
λ=6clientes9minutos

P(X="igual−o−menor−que−4")=P(X=0)+P(X=1)+P(X=2)+P(X=3)+P(X=4)=600!e−6+611!e−6+...=e−6(1+6+18+...)=0.28=28%
Otra forma habitual de expresar la fórmula es en función de pn 
P(X=k)=λkk!e−λ=(pn)kk!e−pn
Según la wikipedia
La distribución de Poisson se aplica a varios fenómenos discretos de la naturaleza (esto es, aquellos fenómenos que ocurren 0, 1, 2, 3,… veces durante un periodo definido de tiempo o en un área determinada) cuando la probabilidad de ocurrencia del fenómeno es constante en el tiempo o el espacio. Ejemplos de estos eventos que pueden ser modelados por la distribución de Poisson incluyen:
El número de autos que pasan a través de un cierto punto en una ruta (suficientemente distantes de los semáforos) durante un periodo definido de tiempo.
El número de errores de ortografía que uno comete al escribir una única página.
El número de llamadas telefónicas en una central telefónica por minuto.
El número de servidores web accedidos por minuto.
El número de animales muertos encontrados por unidad de longitud de ruta.
El número de mutaciones de determinada cadena de ADN después de cierta cantidad de radiación.
El número de núcleos atómicos inestables que se han desintegrado en un determinado período.
El número de estrellas en un determinado volumen de espacio.
La distribución de receptores visuales en la retina del ojo humano.
La inventiva de un inventor a lo largo de su carrera.
La media y la varianza de la distribución son λ.
Notemos que el número de experimentos n no aparece en la fórmula. La función sólo depende de la media y del valor exacto de k.
Comprobemos que es realmente una función de probabilidad. 
∑k=0,∞λkk!e−λ=e−λ∑k=0,∞λkk!=e−λeλ=1

La suma de toda las probabilidades es 1.
En la gráfica se observa la forma de campana de la función dados diferentes valores de λ. El máximo de la función (probabilidad máxima) coincide con su media.
grafica
En la gráfica aparecen curvas por claridad pero evidentemente deberían aparecer puntos debido a que se trata de una distribución continua.
Este es un pequeño homenaje a Salman Khan. Os recomiendo una de sus charlas: (TED) Usemos el vídeo para reinventar la educación.
Fuentes: Varios y Khan Academy 
Written with StackEdit.