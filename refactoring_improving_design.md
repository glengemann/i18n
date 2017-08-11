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

<<<<<<< HEAD
La primera definición como sustantivo:

[NOTE]: refactorización (sustantivo): un cambio hecho a la estructura interna del software
para hacerlo más facíl de entender y económico de modificar sin cambiar su comportamiento
observable.

Podemos encontrar ejemplos de refactorización en el catalogo, como `Extract Method`
y `Pull Up Field`. Una refactorización es usualmente un pequeño cambio en el software,
pero una refactorización puede implicar otras. Por ejemplo, `Extract Class` usualmente
implica `Move Method` y `Move Field`.
=======
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

Sin la refactorización el diseño del programa decae. Mientras las personas cambian
el código, cambios que se hacen para cumplir objetivos de corto plazo y sin la completa
comprensión del diseño del código, este pierde su estructura. Llega a ser
más difícil ver el diseño y leer el código. La refactorización es más que limpiar
el código. El trabajo se hace para remover trozos de código que no están realmente
en el lugar correcto. Perder la estructura del código tiene un efecto acumulativo.
El diseño es lo más difícil de ver, lo más difícil de preservar y lo que más rápidamente
decae. Refactorizaciones regulares ayudan al código a mantener su forma.

Un código pobremente diseñado usualmente necesita más código para hacer la misma cosa,
a menudo literalmente el código hace lo mismo en diferentes lugares. Un importante
aspecto de la mejora del diseño es eliminar el código duplicado. La importancia de
esto recae en las modificaciones futuras del código. Reducir la cantidad de código
no hará al sistema ejecutarse más rápido, por que el efecto sobre el rendimiento
del programa es raramente significativo. Sin embargo, reducir la cantidad de código
hace un gran diferencia cuando se necesita modificarlo. Mientras más código existe
más difícil es modificarlo correctamente. Hay más código para entender. Cambiamos
un pedazo de código aquí pero el sistema no hace hace lo que esperamos por que no
cambiamos ese otro pedazo de por allá que hace casi lo mismo en un contexto ligeramente
diferente. Elimiar la duplicidad asegura que el código dice todo una y solo una vez,
que es la esencia de un buen diseño.

#### La Refactorización hace al Software más facíl de Entender

Programar es en muchas maneras una conversación con una computadora.

### La refactorización ayuda encontrar errores

Además de ayudar a entender el código nos ayuda a ver errores. Debo admitirlo,
no soy muy bueno encontrando errores. Algunas personas pueden leer un pedazo de
código y ver errores, yo no puedo. Sin embargo, encuentro que si refactorizamos el
código trabajamos profundamente en la compresención de lo que el código hace y
colocamos rápidamente esta comprensión dentro del código. Cuando se aclara la estructura
del programa aclaramos ciertas suposiciones que hemos hecho hasta el punto en que
incluso no podemos evitar ver los errores.

Esto me recuerda una afirmación que comunmente Kent Beck hace sobre sí mismo: «No
soy un gran programador, yo solo soy un buen programador con buenos habitos.»
Refactorizar nos ayuda a ser mucho más efectivos cuando escribimos código robusto.

### La refactorización ayuda a programar más rápido

Al final todos los puntos anteriores conducen a este: La refactorización nos ayuda
a desarrollar código más rapidamente.

Esto suena contraintuitivo. Cuans se habla de refactorización la gente puede ver
fasilmente que esta mejora la calidad. Mejora el diseño, mejora la legibilidad, reduce
los errores y todo esto mejora la calidad. Pero, ¿todos estas cosas no reducen
la velocidad del desarrollo?

Creo firmenten que un buen diseño es esencial para el desarrllo rápido de software.
De hecho, el punto central de tener un buen diseño es permitir un rápido desarrollo.
Sin un buen diseño, podemos progresar rapidamente por un tiempo pero pronto el pobre
diseño comienza a retrazarnos. Dedicamos tiempo encontrando y corrigiendo errores en
lugar de agregar nuevas funciones. Los cambios toman más tiempo cuando intentamos entender
el sistema y encontrar el código duplicado. Las nuevas caracteristicas necesitan más
código cuando remendamos sobre un remiendo que remienda un remiendo sobre el código
original.

## ¿Cuando deberíamos refactorizar?

Cuando hablo sobre la refactorización a menudo me preguntan sobre para que momento
debería ser programado. ¿La refactorización debería ser planificada para dos semanas
cada par de meses?

En la mayoría de los casos estoy en contra de asignar tiempo para refactorizar.
Desde mi punto de vista la refactorización no es una actividad a la que podamos
asignar un momento para hacerla. La refactorización es algo que hacemos todo el
tiempo como rafagas. No decidimos refactorizar, refactorizamos por que queremos
hacer algo más y la refactorización te ayuda a hacer esa otra cosa.
