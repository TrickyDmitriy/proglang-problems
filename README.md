# Языки программирования

Задания для курса по языкам программирования в БГУ и БГУИР (Минск).
Пожалуйста, не выкладывайте решения в открытый доступ.

## Программируем на OCaml

Установщик для Windows: https://fdopen.github.io/opam-repository-mingw/installation/

Интерактивные скетчи: [sketch.sh]
Когда создаёте скетчи, обратите внимание, что по умолчанию стоит другой синтаксис (от ReasonML). Переключайте сразу RE на ML.

Компиляция и запуск кода на OCaml.
Если код находится в файле code.ml, а тесты - в tests.ml, скомпилировать и запустить можно так:

```
ocamlopt -o program code.ml tests.ml
```

У вас получится исполняемый файл program.exe. Обратите внимание, что порядок файлов в сборке важен. Чтобы код из основного файла был доступен в файле tests, добавьте в начале файла с тестами

```
open Code;;
```

 Подробнее про компиляцию, запуск и модули можно почитать здесь:

https://ocaml.org/learn/tutorials/compiling_ocaml_projects.html
