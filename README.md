# Введение 
Описание стандарта дерева языка для межязыковой трансляции кода. Для общности в примерах будет использоватся синтаксис си, а базовым типом будет int. Наличие пробела обозначае, как минимум один пробельный символ. Все символы между '<', '>' считаются незначащими (комментарием). Стандарт составлялся по принципу переиспользования существующих типов узлов и простоты реализации reader'а и writer'а.

# Общие положения про структуру дерева
Дерево бинарное.
Узлы делятся на терминальные(листья) и обычные. У терминальных нет наследников. У обычных обязательно два наследника(в случае если один из них нулевой, то этто правый).
Корень дерева должен быть STATEMENT(см. ниже).
В дереве обязательно должна быть функция с названием main(см. ниже).

# Dictionary

## Number

Обозначается { value }

Где value - числовое значение в любом формате, который может вывести %lg.

Является терминальным узлом (листом).

Пример:

~~~
 ... 3 ...
~~~
Может быть представлен как:
 ~~~
  ... { 3 } ...
  or
  ... { 3.00 } ...
  or
  ... { 3e+1 } ...
  e.t.d
 ~~~

## Name

Обозначается { "name" }

Где name - символьное значение.
  Обязательный для поддержки латинский алфавит, символ нижнего подчеркивания и цифровые символы(не могут быть первым символом имени).
  
  Синтаксис: [a-zA-Z_][a-zA-Z_0-9]*
  
  Обязательный для запрета алфавит: [](){}<>.,-+*/?\^%!&$ и лубой символ для которого вызов isctrl() возвращает true.
  
  Все остальные символы поддерживаются по усмотрению пользователя.
  
  (Раздел в разработке, поэтому все кроме обязательного алфавита игнорируйте).

Является терминальным узлом (листом).

Пример:

~~~
 ... name ...
~~~
Может быть представлен как:
 ~~~
  ... { "name" } ...
 ~~~
    
## Statement 

Обозначается { ST {RIGHT} {LEFT} }

RIGHT - текущая инструкция (иструкция-выражение, инструкция выбора, иструкция цикла, иструкция прыжка, и т.д.).

LEFT - следующий statement или nullptr в случае последней инструкции.

Пример:

~~~
{
  int x = 228;
  
  ...
    
  x = 9;
}
~~~
В структуре дерева оно должно быть отражено подобным образом:
~~~
{ ST 
     { VAR { "x" } { 228 } }
     { ST 
          {...} 
          { ... 
               { ST 
                    { EQ { "x" } { 9 } }
                    { NIL } 
               }
          }
     } 
}
~~~

Является корнем дерева.

## Function

Обозначается { FUNC { FUNCT_NAME } { BODY } }
  
  FUNCT_NAME - имя функции(см. далее).
  
  BODY - тело функции, (обязательно) хотя бы один statement.
  
Является обпределением функции. Функция main всегда должна быть представлена.

Пример функции будет представлен после следущего раздела.
  
## Function name

Обозначается { "name" {RIGHT} {LEFT} }

Где name - символьное значение.
  Обязательный для поддержки латинский алфавит, символ нижнего подчеркивания и цифровые символы(не могут быть первым символом имени).
  
  Синтаксис: [a-zA-Z_][a-zA-Z_0-9]*
  
  Обязательный для запрета алфавит: [](){}<>.,-+*/?\^%!&$ и лубой символ для которого вызов isctrl() возвращает true.
  
  Все остальные символы поддерживаются по усмотрению пользователя.
  
  (Раздел в разработке, поэтому все кроме обязательного алфавита игнорируйте).
  
RIGHT - формальные аргументы функции в лице { PARAM }(см. далее) или их отсутствие в лице { NIL }

LEFT - возвращаемое значение функции ({ Void } или { Type })(см. далее).

Пример:
  
~~~
  ...
int funct1()
  {
    ...
  }
  ...
int funct2(int x, int y)
  {
    ...
  }
  ...
void funct3()
  {
    ...
  }
  ...
void funct4(int z, int w)
  {
    ...
  }
  ...
~~~
Может быть представлен как:
 ~~~
  ... 
  { FUNC 
      { "funct1" { NIL } { Type } }
      { ST ... }
  }
  ...
  { FUNC
      { "funct2" { PARAM
                     { VAR { "x" } { NIL } }
                     { PARAM 
                         { VAR { "y" } { NIL } }
                         { NIL }
                     }
                 }
                 { Type }
      { ST ... }
  }
  ...
  { FUNC 
      { "funct3" { NIL } { Void } }
      { ST ... }
  }
  ...
  { FUNC
      { "funct4" { PARAM
                     { VAR { "z" } { NIL } }
                     { PARAM 
                         { VAR { "w" } { NIL } }
                         { NIL }
                     }
                 }
                 { Void }
      { ST ... }
  }
  ...
 ~~~

## Call
  
Обозначается {  CALL { FUNCT_NAME } { NIL } }
  
  FUNCT_NAME - имя функции, но вместо узлов { VAR } выражение(см. далее), а возвращаемоезначение заменнено { NIL }.

Пример:
  
~~~
  ...
 ... funct1() ...
  ...
... funct2(x, 5) ...
  ...
... funct3() ...
  ...
... funct4(1+3, x*y) ...
  ...
~~~
Может быть представлен как:
 ~~~
  ... 
  { CALL 
      { "funct1" { NIL } { NIL } }
      { NIL }
  }
  ...
  { FUNC
      { "funct2" { PARAM
                     { "x" }
                     { PARAM 
                         { 5 }
                         { NIL }
                     }
                 }
                 { NIL }
      { NIL }
  }
  ...
  { FUNC 
      { "funct3" { NIL } { NIL } }
      { NIL }
  }
  ...
  { FUNC
      { "funct4" { PARAM
                     { ADD { 1 } { 3 } }
                     { PARAM 
                         { MUL { "x" } { "y" } }
                         { NIL }
                     }
                 }
                 { NIL }
      { NIL }
  }
  ...
 ~~~
    
## Return

Обозначается {  RET { EXPRESSION } { NIL } }

Где EXPRESSION - выражение или { NIL }
  
Пример:
  
~~~
  ...
 ... return; ...
  ...
... return 5; ...
  ...
... return(1+x*y); ...
  ...
~~~
Может быть представлен как:
~~~
  ... 
  { RET 
      { NIL }
      { NIL }
  }
  ...
  { RET
      { 5 }
      { NIL }
  }
  ...
  { RET
      { ADD
          { 1 }
          { MUL
             { "x" }
             { "y" }
          }
      }
      { NIL }
  }
  ...
 ~~~
    
## Input
  
Обозначается {  IN { PARAM } { NIL } }

У ввода должен присутствовать как минимум один фактический аргумент.
  
Пример:
  
~~~
  ...
... scanf("...", &a); ...
  ...
... scanf("...", &a, &b, &c); ...
  ...
~~~
Может быть представлен как:
~~~
  ... 
  { IN 
      { PARAM
          { "a" }
          { NIL }
      }
      { NIL }
  }
  ...
  { IN 
      { PARAM
          { "a" }
          { PARAM 
              { "b" }
              { PARAM 
                  { "c" }
                  { NIL }
              }
          }
      }
      { NIL }
  }
  ...
 ~~~  
  
## Output
  
Обозначается { OUT { PARAM } { NIL } }

У вывода должен присутствовать как минимум один фактический аргумент.
  
Пример:
  
~~~
  ...
... printf("...", a); ...
  ...
... printf("...", a, 1+5, c/d); ...
  ...
~~~
Может быть представлен как:
~~~
  ... 
  { OUT 
      { PARAM
          { "a" }
          { NIL }
      }
      { NIL }
  }
  ...
  { OUT 
      { PARAM
          { "a" }
          { PARAM 
              { ADD
                 { 1 }
                 { 5 }
              }
              { PARAM 
                  { DIV
                      { "c" }
                      { "d" }
                  }
                  { NIL }
              }
          }
      }
      { NIL }
  }
  ...
 ~~~  
  
## While

Обозначается { WHILE { CONDITIONAL } { BODY } }
  
Где CONDITIONAL - выражение, которое является условием цикла, а BODY - тело цикла, представленое как statement.

У цикла должена присутствовать как минимум одина инструкция в теле.
  
Пример:
  
~~~
  ...
while (x - 10)
  x = x + 1;  
  ...

while (x - 10)
  {
    ...
  }
  ...
~~~
Может быть представлен как:
~~~
  ... 
  { WHILE 
      { SUB
         { "x" }
         { 10 }
      }
      { ST
         { EQ
            { "x" }
            { ADD 
                { "x" }
                { 1 }
            }
         }
         { NIL }
      }
  }
  ...
  { WHILE 
      { SUB
         { "x" }
         { 10 }
      }
      { ST
         { ... }
         { ST ... }
      }
  }
  ...
 ~~~  
  
## If
  
Обозначается { IF { CONDITIONAL } { LEFT } }
  
Где CONDITIONAL - выражение, которое является условием условной конструкции, 

  а LEFT - или тело конструкции (в случае if) или  { ELSE { _RIGHT } { _LEFT } } (в случае if/else),
  
  где _RIGHT - тело истиной ветки, а _LEFT - тело ложной ветки.

У условной конструкции должена присутствовать как минимум одина инструкция в теле (и у if, и у else).

Конструкция if/elif/else (и ее разновидности не поддерживаются) (транслируйте в if {} else { if {} else {} }). 

Тела веток должны быть представлены как statement.
  
Пример:
  
~~~
...
if (expr)
  x = 5;
...
if (expr)
  {
    ...
  }
...  
if (expr)
  ...
else
  ...
...
~~~
Может быть представлен как:
~~~
  ... 
  { IF 
      { expr }
      { ST
         { EQ
            { "x" }
            { 5 }
         }
         { NIL }
      }
  }
  ...
  { IF 
      { expr }
      { ST ... }
  }
  ...
  { IF 
      { expr }
      { ELSE
          { ST ... } <expr == true>
          { ST ... } <expr == false>
      }
  }
  ...
 ~~~    
  
## Var

Обозначается { VAR { "name" } { VAL } }
  
Где  VAL - либо { NIL }(в случае отсутствия иницилизатора), либо { <value> }.   
  
Поддержка { NIL } в качестве { VAL } не является желательной и обязательной.

Пример:
  
~~~
...
int a;
...
int b = 10;
...
~~~
Может быть представлен как:
~~~
  ... 
  { VAR 
      { "a" }
      { NIL }
  }
  ...
  { VAR
      { "b" }
      { 10 }
  }
  ...
 ~~~   
  
## Math operators
### ADD
  
Недопускается унарная версия оператора.
  
Пример:
  
~~~
... expr1 + expr2 ...  
~~~
Может быть представлен как:
~~~
  ... 
  { ADD 
      { expr1 }
      { expr2 }
  }
  ...
 ~~~   
  
### SUB
  
Недопускается унарная версия оператора.
  
Пример:
  
~~~
... expr1 - expr2 ...  
~~~
Может быть представлен как:
~~~
  ... 
  { SUB 
      { expr1 }
      { expr2 }
  }
  ...
 ~~~   

### MUL
  
Пример:
  
~~~
... expr1 * expr2 ...  
~~~
Может быть представлен как:
~~~
  ... 
  { MUL
      { expr1 }
      { expr2 }
  }
  ...
 ~~~   

### DIV
  
Пример:
  
~~~
... expr1 / expr2 ...  
~~~
Может быть представлен как:
~~~
  ... 
  { DIV
      { expr1 }
      { expr2 }
  }
  ...
 ~~~   
  
### POW 
  
Пример:
  
~~~
... pow(expr1, expr2) ...  
~~~
Может быть представлен как:
~~~
  ... 
  { POW
      { expr1 }
      { expr2 }
  }
  ...
 ~~~   

### SIN 
  
Пример:
  
~~~
... sin(expr) ...  
~~~
Может быть представлен как:
~~~
  ... 
  { SIN
      { expr }
      { NIL }
  }
  ...
 ~~~   

### COS 
  
Пример:
  
~~~
... cos(expr) ...  
~~~
Может быть представлен как:
~~~
  ... 
  { COS
      { expr }
      { NIL }
  }
  ...
 ~~~   
  
## Optional operators
- IS_EE: ==
- IS_GE: >=
- IS_BE: <=
- IS_GT: >
- IS_BT: <
- IS_NE: !=
- SQRT : sqrt()
- OR   : || 
- AND  : && 
            
## Краткая версия
- ST
- IF
- ELSE
- NIL
- VAR
- WHILE
- FUNC
- RET
- CALL
- PARAM
- EQ
- VOID
- TYPE            
- "name"
- value
 
            
## Optional nodes
  
Обозначается $ id { NODE } $
  
Где id индификатор(любой набор символов, не соддержащий пробеллов) данного доплнения, а NODE - сам опциональный узел.  
            
Перед id нет пробела.            
  
Поддержка не является обязательной.

Пример:
  
~~~
...
int a[10];
...
~~~
Может быть представлен как:
~~~
  ... 
  $db::array { ARR 
                { "a" }
                { 10 }
              }
  $
  ...
 ~~~   
 
Стоит рассмотреть при чтении ситуацию вложеных опциональных узлов:
  
~~~
$id1 { NODE1  
        $id2 { NODE2 } $ 
             { NIL } 
$
~~~
 
 
 ## Дополнительно
 
Стандарт. Говно...
