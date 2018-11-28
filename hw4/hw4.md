# Язык A

Язык:​ **Racket**

Максимальное количество баллов: **19**

*В этой работе нужно реализовать вычислитель игрушечного языка A (A-Lang), а также решить на нём несколько несложных задач. Код на A-Lang нужно писать в терминах AST на Racket (для этого уже определён набор struct). Racket можно (и иногда нужно) ипользовать как "макросистему" поверх A-lang - писать Racket функции, которые возвращают A-lang выражения, чтобы упростить себе работу. Изменяемые данные и выражения вроде set!, set-mcar! использовать нельзя. Автороское решение занимает примерно 140 строк*

## Описание языка A-lang

В языке A-lang допустимы следующие выражения:

### Константы и значения

Все значения вычисляются сами в себя (кроме замыкания - его можно получить, только вычислив определение функции или обращение к переменной)

- `(aint value)` - целочисленное значение;
- `(abool value)` - логическое значение;
- `(anil)` - пустое значение (для обозначения пустого списка);
- `(aclosure fun env)` - значение, в которое вычисляется определение функции (напрямую писать в абстрактном синтаксисе нельзя). Хранит функцию и окружение на момент её определения.

### Тестирующие выражения

Используются для проверки условий, вычисляются в логическое значение.

- `(aless e1 e2)` - если A-Lang выражения `e1` и `e2` оба вычисляются в целочисленные значения `v1` и `v2`, то результатом всего выражения `(aless e1 e2)` будет `(abool #t)`, если `v1 < v2`, и `(abool #f)` в противном случае. Если какое-то из значений `v1` или `v2` не целочисленное, то получаем ошибку.
- `(isanil e)` - если A-Lang выражение `e` вычисляется в `(anil)`, то результатом будет `(abool #t)`, иначе - `(abool #f)`.

### Арифметика

- `(asub e1 e2)` - если A-Lang выражения `e1` и `e2` вычисляются в целочисленные значения `v1` и `v2` соответственно, то результатом будет целочисленное значение, равное разности `v1` и `v2`, иначе ошибка.

### Переменные и окружение

- `(avar id)` - происходит поиск строки `id` в текущем окружении (функция `lookup-env` уже реализована). Если в окружении найдено значение, соответствующее строке `id`, оно и будет результатом, иначе ошибка.
- `(alet id e1 e2)` - A-Lang выражение `e1` вычисляется в `v` в текущем окружении, затем A-Lang выражение `e2` вычисляется в текущем окружении, дополненном парой `(id . v)`.

### Работа с потоком управления

- `(aif e1 e2 e3)` - условное выражение, работает так же как в Racket: сперва A-Lang выражение `e1` вычисляется в значение `v1`. Если `v1` - логическое значение, то в зависимости от его истинности вычисляется либо `e2`, либо `e3`. Если `v1` не является логическим значением, получаем ошибку.
- `(afun id param body)` - определение функции, где `id` - имя функции либо `#f` для безымянных функций, `param` - имя единственноего параметра, а `body` - это A-lang выражение, содержащее тело функции. Вычисляется в `(aclosure fun env)`, где `fun` - исходное выражение `(afun id param body)`, а `env` - текущее окружение. Тело функции **не вычисляется**.
- `(acall e1 e2)` - вызов функции. A-Lang выражения `e1` и `e2` вычисляются в текущем окружении, получаются значения `v1` и `v2` соответственно. Если `v1` - `(aclosure fun env)`, то тело функции `fun` вычисляется в окружении `env` из замыкания, дополненном парами `(param-id . v2)`, `(fun-id . v1)` (если функция из замыкания не безымянная). Если `v1` - не замыкание, получаем ошибку.

### Пользовательские структуры данных - пары

- `(apair e1 e2)` - A-Lang выражения `e1`, `e2` вычисляются в текущем окружении в `v1` и `v2`, затем конструируется пара `(apair v1 v2)`.
- `(afst e)` - выражение A-Lang `e` вычисляется в текущем окружении в значение `v`. Если `v` - пара `(apair v1 v2)`, то результатом будет `v1`, иначе получаем ошибку.
- `(asnd e)` - A-Lang выражение `e` вычисляется в текущем окружении в значение `v`. Если `v` - пара `(apair v1 v2)`, то результатом будет `v2`, иначе получаем ошибку.

## Задачи

1. Реализация вычислителя A-Lang. Абстрактный синтаксис и ветки для некоторых выражений уже реализованы. Допишите рекурсивную функцию `eval-under-env`, реализовав ветки для оставшихся типов выражений A-Lang согласно описанию. Считайте, что AST на входе вычислителя синтаксически корректны, то есть не бывает деревьев вроде `(aint (aint 3))` или `(asub 43 42)`, но могут быть ошибки типов, например `(afst (aint 3))` или `(acall (aint 3) (afun #f "x" (avar "x")))` - в этом случае выдавайте сообщения об ошибке.
    1. (1) Реализуйте вычисление констант и значений
    2. (2) Реализуйте вычисление тестирующих выражений
    3. (2) Реализуйте работу с переменными и окружением
    4. (5) Реализуйте работу с потоком управления
    5. (2) Реализуйте работу с парами.
2. Программирование на A-Lang. Будьте внимательны, чтобы не запутаться, где язык-объект (A-lang), а где язык реализации (Racket), который мы будем использовать как язык для макроподстановок.
    1. (2)
        - Напишите Racket функцию `a-add`, которая принимает два A-Lang выражения `x` и `y`, и возвращает A-Lang выражение, которое после вычисления вернёт сумму результатов вычисления `x` и `y`. Сама функция `a-add` это выражение **не вычисляет**.
        - Напишите Racket функцию `a-eq`, которая принимает два A-Lang выражения `x` и `y`, и возвращает A-Lang выражение, которое вычисляется в `(abool #t)`, если значения выражений `x` и `y` равны, и `(abool #f)` в противном случае. Считайте, что внутри выражений `x` и `y` не встречаются переменные с именами `"_x"` и `"_y"`. При вычислении выражения, которое возвращает `a-eq`, его подвыражения `x` и `y` должны вычисляться **ровно один раз**. Считайте, что `x` и `y` вычисляются в целочисленные значения `(aint v)`.
    2. (2) Напишите Racket функцию `a-mul`, которая принимает два A-Lang выражения `x` и `y` и возвращает A-Lang выражение, которое вычисляется в произведение результатов вычисления `x` и `y`, считая, что подвыражения `x` и `y` вычисляются в целочисленные значения.
    3. (1) Напишите A-Lang функцию `sum`, которая принимает список целочисленных значений `lst` (сконструированный из вложенных пар, оканчивающийся на `(anil)`) и возвращает целочисленное значение, содержащее сумму всех значений списка. Например, при вызове со списком `(apair (aint 1) (apair (aint 2) (anil)))` в качестве аргумента функция `sum` должна вернуть `(aint 3)`, при вызове с `(anil)` - `(aint 0)`. Для удобства тестирования сохраните полученное выражение в Racket переменную `a-sum`.
    4. (2)
        - Напишите A-Lang функцию `map`, которая принимает в качестве аргументов A-Lang функцию `f` и список `lst` (помним про каррирование), и возвращает список, полученный из элементов `lst` применением к ним функции `f`. Для удобства сохраните полученное A-Lang выражение в Racket переменную с именем `a-map`
        - С помощью функции `map` реализуйте A-Lang функцию `squares`, которая принимает список целочисленных A-Lang значений `lst` и возвращает список, полученный из квадратов соответствующих значений.