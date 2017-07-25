# Database Benchmarking

PostgreSQL se distribuye con un programa de benchmarking llamado `pgbench` que se
puede usar para una variedad de pruebas. Las pruebas que se incluyen por defecto
son útiles, además, incluye un lenguaje de scripting que permite adaptar las pruebas
de la base de datos. Incluso podemos usar el núcleo de `pgbench`; un programa para
base de datos eficiente, escalable y multi-cliente; para escribir completamente
pruebas a la medida. Existen algunas otras pruebas estándares de la industria
que permiten comparar a PostgreSQL con otras bases de datos, aúnque sin una auditoría
de los resultados en la mayoría de los datos.

## Pruebas por defecto de pgbench

...

...

## Definición de Tablas

The main table definition SQL adds these tables:

```sql
CREATE TABLE pgbench_branches(bid int not null, bbalance int, filler char(88));
ALTER TABLE pgbench_branches ADD primary key (bid);

CREATE TABLE pgbench_tellers(tid int not null, bid int, tbalance int, filler char(84);
ALTER TABLE pgbench_tellers ADD primary key (tid);

CREATE TABLE pgbench_accounts(aid int not null, bid int, abalance int, filler char(84));
ALTER TABLE pgbench_accounts ADD primary key (aid);

CREATE TABLE pgbench_history(tid int, bid int, aid int, delta int, mtime timestamp, filler char(22));
```

La intención de varios campos de filtrado es asegurarse que cada fila insertada
en la base de dato es realmente de 100 bytes. De hecho esto no trabaja como ...

De lo que deberíamos darnos cuenta es que cada tipo de registro insertado por la
prueba estándar de pgbench es extremadamente limitado, solo un pequeño número de
bytes se insertan en cada uno de los registros. En la practica, siempre el más
pequeño `INSERT` o `UPDATE` usará una página de base de datos estándar de 8K y
requiere siempre este tamaño cuando escribe. El hecho de que solo una fracción es
usada después de cada cambio ayuda a reducir el volumen WAL escrito, pero solo una
vez que hemos escrito el usual `full_page_writes` de copia para cada página, lo que
terminará de cualquier manera como el volumen del procesamiento WAL actual.

## Detección de la Escala

El número de branches (sucursales) representa la escala, cada branch tiene 10 tellers
(cajeros) y 100.000 accounts (cuentas) en la base de datos. Este valor
es introducido cuando inicualizamos la base de datos `pgbench` y las tablas son creadas.
Abajo hay un ejemplo que muestra como crear un base de datos para `pgbench` y como
poblarla:

```bash
$ createdb pgbench

$ pgbench -i -s 10 pgbench
```

Esto inicializa (-i) las tablas para pgbench usando una scala de 10 (-s 10) dentro
de la base de datos llamada `pgbench`. No necesitamos usar el nombre por que es una
convención.

Cuando `pgbench` se ejecuta en modo regular (no inicializando), este primero revisa
si manualmente dijimos la escala de base de datos `-s` que usar; si es así, se usa
ese valor. Si no es así, se ejecutara al escala de las pruebas por defecto, `pgbench`
intentará detectar la escala usando la siguiente consulta:

```sql
SELECT count(*) FROM pgbench_branches;
```

NOTA: para nosotros la escala son los aspirantes (~300.000), pero para una UE la
escala parece ser las aulas.

## Definición del Script de Consulta

Como en la tabla con código de arriba, el código verdadero

se copilan in su código fuente, la única manera en que podemos verlos es revisando
la documentación `http://www.postgresql.org/docs/current/static/pgbench.html`.

El script de transacción por defecto que es llamado también transacción "tipo TPC-B"
cuando ejecutamos la prueba (y a la que nos referimos aquí como una transacción "TPC-B-like") presenta siete comandos por transacción. Dentro del programo
esto tiene el aspecto siguiente:

```sql
\set nbranches :scale
\set ntellers 10 * :scale
\set naccounts 100000 * :scale
\setrandom aid 1 :naccounts
\setrandom bid 1 :nbranches
\setramdom tid 1 :ntellers
\setramdom delta -5000 5000
BEGIN;
UPDATE pgbench_accounts SET abalance = abalance + :delta WHERE aid = :aid;
SELECT abalance FROM pgbench_accounts WHERE aid = :aid;
UPDATE pgbench_tellers SET tbalance = tbalance + :delta WHERE tid = :tid;
UPDATE pgbench_branches SET bbalance = bbalance + :delta WHERE bid = :bid;
INSERT INTO pgbench_history (tid, bid, aid, delta, mtime) VALUES (:tid, :bid, :aid, :delta, CURRENT_TIMESTAMP);
END;
```

Las primeras tres líneas computan cuantos branches, tellers y accounts tiene Esta
base de datos basado en `:scale`, que es un nombre de variable especial que coloca
la escala de la base de datos que se determina usando la lógica descrita en la sección
anterior.

Las siguientes cuatro líneas crean valores aleatorios que se usan para simular una
transacción bancaria donde alguien fue a un cajero específico de una agencia específica
y depositó o retiró alguna cantidad de dinero de su cuenta.

The presumption that one is using...

The main core here is five statements wrapped in a transaction block...

Cada una de las sentencias tiene un impacto práctico muy diferente:

* `UPDATE pgbench_accounts`: Siendo la tabla más grande esta es por mucho la sentencia
más probable de disparar I/O de disco.

* `SELECT abalance`: Como el `UPDATE` anterior ha dejado la información necesaria
para responder esta consulta en la caché, esta agrega muy poca carga de trabajo
para la prueba TPC-B-like.

* `UPDATE pgbench_tellers`: Como hay muchos menos cajeros que cuentas esta tabla
es pequeña y esta probablemente cacheada en RAM, pero aunque es pequeña tiende a
ser la fuente de problemas de bloqueo.

* `UPDATE pgbench_branches`: Siendo una tabla extremadamente pequeña todo su contenido
esta ya probablemente cacheado y esencialmente libre de acceso. Sin embargo, por que
es pequeña el bloqueo puede convertirse en un cuello de botella si usamos una escala
pequeña y muchos clientes. De acuerdo con la recomenación general siempre es mejor
asegurarse que la escala es mayor que los clientes para cualquier prueba que ejecutemos.
En al practica, este problema no es realmente lo que importa aunque se puede ver
en una de los ejemplos de abajo. Las I/O de disco sobre la tabla cuentas y la
velocidad del commmit será una limitación mayor que el bloqueo en la mayoría de los casos.

... pero ejecutar solo sentencias `SELECT` es muy útil para examinar el tamaño de
la cache en nuestro sistema operativo y para medir la velocidad máximo de CPU.

## Ejecutar pgbench Manualmente

Como un ejemplo simple de ejecución de una prueba podemos ejecutar una prueba de
solo lectura para un pequeño número de transacciones. Aquí esta una prueba rápida
para el servidor de prueba:

```bash
$ pgbench -S -c 4 -t 20000 pgbench
```

Esta es la simulación del escenario donde cuatro clientes de base de datos están
activos al mismo tiempo y continuamente consultando los datos. Cada cliente está
en ejecución hasta que se ejecuta el número de peticiones de las transacciones.

...
Usar más de un hilo (solo disponible desde PostgreSQL 9.0) muestra una mejora dramática
de la velocidad.

##

# Ficha Técnica

S.O:

## Escenario Genérico sin Llaves primarias

$ createdb pgbench
$ pgbench -i -s 10 pgbench

### Prueba A: _select only_

```bash
$ pgbench -S -c 4 -t 20000 pgbench
```

transaction type: <builtin: select only>
scaling factor: 10
query mode: simple
number of clients: 4
number of threads: 1
number of transactions per client: 20000
number of transactions actually processed: 80000/80000
latency average = 0.590 ms
tps = 6782.773045 (including connections establishing)
tps = 6784.675640 (excluding connections establishing)

### Prueba B: TPC-B

transaction type: <builtin: TPC-B (sort of)>
scaling factor: 10
query mode: simple
number of clients: 4
number of threads: 2
duration: 600 s
number of transactions actually processed: 72662
latency average = 33.034 ms
tps = 121.086642 (including connections establishing)
tps = 121.087346 (excluding connections establishing)


## Escenario Genérico con Llaves Primarias

$ createdb pgbenchfk
$ pgbench -i -s 10 --foreign-keys pgbenchfk

### Esceneario _select only_

$ pgbench -S -c 4 -t 20000 pgbenchfk

transaction type: <builtin: select only>
scaling factor: 10
query mode: simple
number of clients: 4
number of threads: 1
number of transactions per client: 20000
number of transactions actually processed: 80000/80000
latency average = 0.768 ms
tps = 5205.910348 (including connections establishing)
tps = 5207.362208 (excluding connections establishing)
