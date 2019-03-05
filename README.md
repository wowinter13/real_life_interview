# Interview

## DB Questions

**What is AREL?**

Shortly: *A RELational algebra*

Description:
*Arel is a Relational Algebra for Ruby. It simplifies the generation complex of SQL queries.
Now is bundled inside Rails Active Record.
for use in constructing SQL queries. Every time you pass a hash to where, it goes through Arel eventually. Rails exposes this with a public API that we can hook into when we need to build a more complex query.
It’s all about providing frameworks with a way of building and representing SQL queries. It’s not the kind of library you would typically want to use directly (although you could as shown in a minute). Arel is meant to be the basic building block upon which frameworks build their own APIs that are more suitable for the end user.*

**What is N+1 query?**

**Базовые SQL Операторы**
- HAVING
- GROUP

**Join types:**

**Why do we need transaction when getting data from database?**

**Что такое индекс, когда добавляют в базу и зачем они нужны**

*Индекс это обмен памяти на скорость, и базе данных вместо того чтобы использовать полнотекстный поиск достаточно пройтись по индексу, это будет значительно быстрее.*

*Индексы влияют на скорость апдейта и инсерта, потому что при каждом изменение таблицы нужно перестраивать индекс.*

**Как устроен btree и как функция защищает от коллизий**

## Theoretical questions

**Кому принадлежат методы объекта класса**

*Они принадлежат все равно классу, потому что хранятся в его структуре.*

**3 принципа ООП:**
- Как инкапсуляция работает в Ruby

**5 принципов SOLID:**
- S
- O
- L
- I
- D

**Cвязность и что это означает**

*Классы должны как можно меньше знать друг о друге.*

## Practical questions

**What is the difference between :include and :extend in Ruby?**

**What is the difference between Proc and lambda?**

**Типы данных в Ruby**
- Nil/True/False class
- Numeric (integer/float/complex)
- Time/Date
- String
- Array/Hash/Range
- Struct


**What is Mixin?**

**Множественное наследование в Ruby**

**Самый известный пример метапрограммирования в Ruby**

*attr_accessor, attr_reader, attr_writer*

**Что такое unit тесты**

**Где хранить бизнес логику(толстые модели/толстые контроллеры)**

*В сервисах или интеракторах/оперейшенах.*

**Базовые Ruby/Rails паттерны:**
- Interactor
- FormObject
- ServiceObject

**Что такое MVC**

## Interview-coding

**Варианты реализации факториала:**

- классический
  ```ruby
  def fact(n)
    return 1 if n.zero?

    (1..n).inject(:*)
  end
  ```
- рекурсивный
  ```ruby
  def rec_fact(n)
    return 1 if n.zero?

    return n * rec_fact(n-1)
  end
  ```


**Some JS:**

What is the difference between `let`, `const`, `var` in JS.

Как реализован ООП в классическом JS.