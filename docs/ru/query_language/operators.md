# Операторы {#operatory}

Все операторы преобразуются в соответствующие функции на этапе парсинга запроса, с учётом их приоритетов и ассоциативности.
Далее будут перечислены группы операторов в порядке их приоритета (чем выше, тем раньше оператор связывается со своими аргументами).

## Операторы доступа {#operatory-dostupa}

`a[N]` - доступ к элементу массива, функция `arrayElement(a, N)`.

`a.N` - доступ к элементу кортежа, функция `tupleElement(a, N)`.

## Оператор числового отрицания {#operator-chislovogo-otritsaniia}

`-a` - функция `negate(a)`.

## Операторы умножения и деления {#operatory-umnozheniia-i-deleniia}

`a * b` - функция `multiply(a, b)`

`a / b` - функция `divide(a, b)`

`a % b` - функция `modulo(a, b)`

## Операторы сложения и вычитания {#operatory-slozheniia-i-vychitaniia}

`a + b` - функция `plus(a, b)`

`a - b` - функция `minus(a, b)`

## Операторы сравнения {#operatory-sravneniia}

`a = b` - функция `equals(a, b)`

`a == b` - функция `equals(a, b)`

`a != b` - функция `notEquals(a, b)`

`a <> b` - функция `notEquals(a, b)`

`a <= b` - функция `lessOrEquals(a, b)`

`a >= b` - функция `greaterOrEquals(a, b)`

`a < b` - функция `less(a, b)`

`a > b` - функция `greater(a, b)`

`a LIKE s` - функция `like(a, b)`

`a NOT LIKE s` - функция `notLike(a, b)`

`a BETWEEN b AND c` - равнозначно `a >= b AND a <= c`

`a NOT BETWEEN b AND c` - равнозначно `a < b OR a > c`

## Операторы для работы с множествами {#operatory-dlia-raboty-s-mnozhestvami}

*Смотрите раздел [Операторы IN](select.md#select-in-operators).*

`a IN ...` - функция `in(a, b)`

`a NOT IN ...` - функция `notIn(a, b)`

`a GLOBAL IN ...` - функция `globalIn(a, b)`

`a GLOBAL NOT IN ...` - функция `globalNotIn(a, b)`

## Оператор для работы с датами и временем {#operators-datetime}

### EXTRACT {#extract}

``` sql
EXTRACT(part FROM date);
```

Позволяет извлечь отдельные части из переданной даты. Например, можно получить месяц из даты, или минуты из времени.

В параметре `part` указывается, какой фрагмент даты нужно получить. Доступные значения:

-   `DAY` — День. Возможные значения: 1–31.
-   `MONTH` — Номер месяца. Возможные значения: 1–12.
-   `YEAR` — Год.
-   `SECOND` — Секунда. Возможные значения: 0–59.
-   `MINUTE` — Минута. Возможные значения: 0–59.
-   `HOUR` — Час. Возможные значения: 0–23.

Эти значения могут быть указаны также в нижнем регистре (`day`, `month`).

В параметре `date` указывается исходная дата. Поддерживаются типы [Date](../data_types/date.md) и [DateTime](../data_types/datetime.md).

Примеры:

``` sql
SELECT EXTRACT(DAY FROM toDate('2017-06-15'));
SELECT EXTRACT(MONTH FROM toDate('2017-06-15'));
SELECT EXTRACT(YEAR FROM toDate('2017-06-15'));
```

В следующем примере создадим таблицу и добавим в неё значение с типом `DateTime`.

``` sql
CREATE TABLE test.Orders
(
    OrderId UInt64,
    OrderName String,
    OrderDate DateTime
)
ENGINE = Log;
```

``` sql
INSERT INTO test.Orders VALUES (1, 'Jarlsberg Cheese', toDateTime('2008-10-11 13:23:44'));
```

``` sql
SELECT
    toYear(OrderDate) AS OrderYear,
    toMonth(OrderDate) AS OrderMonth,
    toDayOfMonth(OrderDate) AS OrderDay,
    toHour(OrderDate) AS OrderHour,
    toMinute(OrderDate) AS OrderMinute,
    toSecond(OrderDate) AS OrderSecond
FROM test.Orders;
```

``` text
┌─OrderYear─┬─OrderMonth─┬─OrderDay─┬─OrderHour─┬─OrderMinute─┬─OrderSecond─┐
│      2008 │         10 │       11 │        13 │          23 │          44 │
└───────────┴────────────┴──────────┴───────────┴─────────────┴─────────────┘
```

Больше примеров приведено в [тестах](https://github.com/ClickHouse/ClickHouse/blob/master/tests/queries/0_stateless/00619_extract.sql).

### INTERVAL {#operator-interval}

Создаёт значение типа [Interval](../data_types/special_data_types/interval.md) которое должно использоваться в арифметических операциях со значениями типов [Date](../data_types/date.md) и [DateTime](../data_types/datetime.md).

Типы интервалов:
- `SECOND`
- `MINUTE`
- `HOUR`
- `DAY`
- `WEEK`
- `MONTH`
- `QUARTER`
- `YEAR`

!!! warning "Внимание"
    Интервалы различных типов нельзя объединять. Нельзя использовать выражения вида `INTERVAL 4 DAY 1 HOUR`. Вместо этого интервалы можно выразить в единицах меньших или равных наименьшей единице интервала, Например, `INTERVAL 25 HOUR`. Также можно выполнять последовательные операции как показано в примере ниже.

Пример:

``` sql
SELECT now() AS current_date_time, current_date_time + INTERVAL 4 DAY + INTERVAL 3 HOUR
```

``` text
┌───current_date_time─┬─plus(plus(now(), toIntervalDay(4)), toIntervalHour(3))─┐
│ 2019-10-23 11:16:28 │                                    2019-10-27 14:16:28 │
└─────────────────────┴────────────────────────────────────────────────────────┘
```

**Смотрите также**

-   Тип данных [Interval](../data_types/special_data_types/interval.md)
-   Функции преобразования типов [toInterval](functions/type_conversion_functions.md#function-tointerval)

## Оператор логического отрицания {#operator-logicheskogo-otritsaniia}

`NOT a` - функция `not(a)`

## Оператор логического ‘И’ {#operator-logicheskogo-i}

`a AND b` - функция `and(a, b)`

## Оператор логического ‘ИЛИ’ {#operator-logicheskogo-ili}

`a OR b` - функция `or(a, b)`

## Условный оператор {#uslovnyi-operator}

`a ? b : c` - функция `if(a, b, c)`

Примечание:

Условный оператор сначала вычисляет значения b и c, затем проверяет выполнение условия a, и только после этого возвращает соответствующее значение. Если в качестве b или с выступает функция [arrayJoin()](functions/array_join.md#functions_arrayjoin), то размножение каждой строки произойдет вне зависимости от условия а.

## Условное выражение {#operator_case}

``` sql
CASE [x]
    WHEN a THEN b
    [WHEN ... THEN ...]
    [ELSE c]
END
```

В случае указания `x` - функция `transform(x, [a, ...], [b, ...], c)`. Иначе — `multiIf(a, b, ..., c)`.
При отсутствии секции `ELSE c`, значением по умолчанию будет `NULL`.

!!! note "Примечание"
    Функция `transform` не умеет работать с `NULL`.

## Оператор склеивания строк {#operator-skleivaniia-strok}

`s1 || s2` - функция `concat(s1, s2)`

## Оператор создания лямбда-выражения {#operator-sozdaniia-liambda-vyrazheniia}

`x -> expr` - функция `lambda(x, expr)`

Следующие операторы не имеют приоритета, так как представляют собой скобки:

## Оператор создания массива {#operator-sozdaniia-massiva}

`[x1, ...]` - функция `array(x1, ...)`

## Оператор создания кортежа {#operator-sozdaniia-kortezha}

`(x1, x2, ...)` - функция `tuple(x2, x2, ...)`

## Ассоциативность {#assotsiativnost}

Все бинарные операторы имеют левую ассоциативность. Например, `1 + 2 + 3` преобразуется в `plus(plus(1, 2), 3)`.
Иногда это работает не так, как ожидается. Например, `SELECT 4 > 3 > 2` выдаст 0.

Для эффективности, реализованы функции `and` и `or`, принимающие произвольное количество аргументов. Соответствующие цепочки операторов `AND` и `OR`, преобразуются в один вызов этих функций.

## Проверка на `NULL` {#proverka-na-null}

ClickHouse поддерживает операторы `IS NULL` и `IS NOT NULL`.

### IS NULL {#operator-is-null}

-   Для значений типа [Nullable](../data_types/nullable.md) оператор `IS NULL` возвращает:
    -   `1`, если значение — `NULL`.
    -   `0` в обратном случае.
-   Для прочих значений оператор `IS NULL` всегда возвращает `0`.

<!-- -->

``` sql
SELECT x+100 FROM t_null WHERE y IS NULL
```

``` text
┌─plus(x, 100)─┐
│          101 │
└──────────────┘
```

### IS NOT NULL {#is-not-null}

-   Для значений типа [Nullable](../data_types/nullable.md) оператор `IS NOT NULL` возвращает:
    -   `0`, если значение — `NULL`.
    -   `1`, в обратном случае.
-   Для прочих значений оператор `IS NOT NULL` всегда возвращает `1`.

<!-- -->

``` sql
SELECT * FROM t_null WHERE y IS NOT NULL
```

``` text
┌─x─┬─y─┐
│ 2 │ 3 │
└───┴───┘
```

[Оригинальная статья](https://clickhouse.tech/docs/ru/query_language/operators/) <!--hide-->
