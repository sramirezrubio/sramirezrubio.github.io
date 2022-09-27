---
layout: post
title:  Problemas de rendimiento con la CPU
categories: [Blog, New Gods]
---

Llevo unos días con la desagradable sorpresa de tener problemas **graves** de rendimiento en el proyecto #NewGodsGame.

Como os comenté en el pasado post, se trata de un videojuego de estrategia por turnos, similar a XCOM, Fire Emblem o Mario+Rabbids.

## El problema
Cuando selecciono una unidad, los FPS caen a 20-30 y cuando la muevo, dependiendo de lo grande que sea la escena, pueden llegar a caer a los 4-5fps. Lo que resulta en que al mover las unidades, el juego, sea simplemente injugable.

Tras trastear bastante con el profiler de Unity, he conseguido detectar donde están los problemas de rendimiento, aunque no hay que ser un gran experto para darse cuenta... los picos de CPU hablan por si solos.

![](/images/cpu-profiler/cpu-profiler-performance.png)

![](/images/cpu-profiler/cpu-profiler-performance-2.png)

Considerando que trabajo en un AMD Ryzen Threadripper 2950X y un M1 Pro de Apple, y sobretodo, que ya me está afectando de esta manera, consideré necesario atajar el problema cuanto antes, ya que iba a repercutir, aún más, en el futuro del proyecto.

## La solución
El problema radicaba en que, cuando una unidad se movía, las posibles *posiciones de desplazamiento* se actualizaban constantemente, generando llamadas a ciertos loops en el Update(), con el coste que eso genera.

Nada que no solucionara algunos...
```md
private void Update()
{
    if (x) return;
    ...
}

```
...en los Update(), para bloquear código que no fuera necesario y sobretodo, refactorizar algunos loops que buscaban posiciones válidas en el espacio y generaban el PathFinding.

Con estos cambios, hemos recuperado el rendimiento normal del juego y vuelve a tirar a +300fps... si no lo tuviera capado a 60fps, tengo aprecio por mis componentes.

## ¿Qué hemos aprendido?
Ha habido suerte de detectar tempranamente este problema, porque sobre el PathFinding se van a construir bastantes sistemas y mecánicas que seguramente aumentarán la carga de procesamiento del mismo, por lo que partir de una base sólida y optima, es fundamental.
