# Стандарт хранения дерева для языка программирования AVVP... и еще много буковок. 
## Введение 
Дерево языка должно хранится в едином формате, чтобы можно было транслировать один язык в другой, так что время напрячь булки и написать лучший стандарт(стандарт говно!!!) в мире.

## Общие положения про структуру дерева

Дерево бинарное, ёпта.

### Statement 
Все выражения по типу присваивания, тел функций и т.д., должны быть левым поддеревом специального узла **statement** 
Например, если у нас есть выражение по типу:
~~~
var x = 228;
~~~
В структуре дерева оно должно быть отражено подобным образом:
~~~
{statement}
  -Left -> {variable}
  -Right -> {statement}
~~~

Корнем дерева тоже является **{statement}**, от которого отходят дочерние **{statement}** и узлы **{func}**
Более того, **прародителем** всех областей видимости, например тел функции должен быть именно {ST}.

Запись в текстовом файле для хранения дерева: **ST**.

### Function

О, великие функции, да подорвёт пукан наш их синтаксис.
Для начала нужно написать реализацию функции, узел {FUNC} является объявлением того, что сейчас пойдет объявление функции. {FUNC} должен быть **левым ребенком** узла {ST}.
- Левый ребенок узла {FUNC} - имя функции, узел {"название функции"}, о нем поговорим далее. 
- Правый ребенок узла - тело функции, то есть {ST}, так как в каждой области видимости он должен быть первым.

Входные параметры, имя функции и т.д. будут относиться к левому поддереву {FUNC}, то есть {"имя функции"}

### Function -> Имя функции

Начинается фарш,

## Запись дерева на диск

## Владян лох
