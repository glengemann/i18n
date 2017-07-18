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

...

Me han preguntado: "¿La refactorización es solo limpiar el código?" En principio la respuesta es
sí, pero creo que la refactorización va más allá porque provee una técnica para limpiar el código
de una manera más eficiente y controlada. Desde que uso la refactorización he notado que limpio el
código mucho más efectivamente que antes. Esto es porque conozco que refactorizaciones usar,
conozco como usarlas de una manera que minimizan errores y pruebo cada vez que es posible.

Ampliaremos un par de puntos de nuestra definición...

El segundo punto que quiero resaltar es que la refactorización no cambia el comportamiento observable
del software. El software aún cumple la misma función que hizo antes. Cualquier usuario,
tanto un usuario final u otro programador no puede decir que las cosas han cambiado.

#### Los Dos Sombreros

El segundo punto lo ejemplifica la metáfora de los dos sombreros de Kent Beck. Cuando usamos la refactotización
para desarrollar dividimos nuestro tiempo en dos actividades distintas: agregar funciones y
refactorizar. Cuando agregamos funciones no deberíamos cambiar el código; solo agregando
nuevas capacidades. Podemos medir nuestro progreso agregando pruebas y poniendolas a trabajar.
Cuando refactorizamos, procuramos no agregar funciones y solo reestructuramos el código.
No agregamos ninguna prueba (a menos que encontremos un caso que olvidamos antes); solo cambiamos
las pruebas cuando es absolutamente necesario para hacer frente a un cambio en una interface.

...

### ¿Por qué deberíamos refactorizar?

No quiero proclamar a la refactorización como la cura de todas las enfermedades. No es una "bala de plata".
Es una herramienta valiosa, un par de pinzas de plata que nos ayudan a mantener un buen agarre sobre
nuestro código. La refactorización es una herramienta que puede y debería usarse para varios propósitos.

#### La Refactorización Mejora el Diseño
