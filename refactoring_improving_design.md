## Capítulo 2. Principios de la Refactorización

El ejemplo anterior debío habernos dado una idea clara de lo que trata la refactorización. Ahora
es tiempo de detenernos y mirar los principios claves de la refactorización y algunos de los problemas
sobre los que hay que pensar al hacer la rafactorización.

### Definiendo la Refactorización

Siempre soy un poco desconfiado de las definiciones porqué cada quien tiene la suya,
pero cuando se escribe un libro se tiene que elegir una propia. En este caso baso mi
definición en el trabajo hecho por el grupo Ralph Johnson y asociados.

La primera cosa que podemos decir es que la palabra *Refactorización* tiene dos definiciones
dependiendo del contexto. Podríamos encontrar esto fastidioso (y de hecho lo es), pero
esto sirve como otro ejemplo más de las realidades de trabajar con lenguajes naturales.

La primera definición como sustantivo:

[NOTE]: refactorización (sustantivo): un cambio hecho a la estructura interna del software
para hacerlo más facíl de entender y económico de modificar sin cambiar su comportamiento
observable.

Podemos encontrar ejemplos de refactorización en el catalogo, como `Extract Method`
y `Pull Up Field`. Una refactorización es usualmente un pequeño cambio en el software,
pero una refactorización puede implicar otras. Por ejemplo, `Extract Class` usualmente
implica `Move Method` y `Move Field`.
