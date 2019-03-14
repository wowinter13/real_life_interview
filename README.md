# Interview

***
## DB Questions

**What is AREL?**

Shortly: *A RELational algebra*

Fully:

*Arel is a Relational Algebra for Ruby. It simplifies the generation complex of SQL queries.

Now is bundled inside Rails Active Record.

Being used in constructing SQL queries. Every time you pass a hash to where, it goes through Arel eventually. Rails exposes this with a public API that we can hook into when we need to build a more complex query.

It’s all about providing frameworks with a way of building and representing SQL queries. It’s not the kind of library you would typically want to use directly (although you could as shown in a minute). Arel is meant to be the basic building block upon which frameworks build their own APIs that are more suitable for the end user.*

**What is N+1 query?**

*The N+1 query is a query, when we do a one more query for a child-record each time iterating throught parent-records.*

*The main problem is a reduced performance.*

**Basic SQL Clauses**
- HAVING
- GROUP

**Join types:**

**Why do we need a transaction when getting data from the database?**

**Что такое индекс, когда добавляют в базу и зачем они нужны**

*Индекс это обмен памяти на скорость, и базе данных вместо того чтобы использовать полнотекстный поиск достаточно пройтись по индексу, это будет значительно быстрее.*

*Индексы влияют на скорость апдейта и инсерта, потому что при каждом изменение таблицы нужно перестраивать индекс.*

**Как устроен btree и как функция защищает от коллизий**

***

## Theoretical questions

**Кому принадлежат методы объекта класса**

*Они принадлежат все равно классу, потому что хранятся в его структуре.*

**3 принципа ООП:**
- Как инкапсуляция работает в Ruby

**5 principles SOLID:**

*SOLID is an acronym for 5 basic OOP principles based on making the code more understandable, flexible and maintainable.*

- S(ingle resposibility)
- O(pen-closed)
- L(iskov substitution)
- I(nterface segregation)
- D(ependency inversion)

**Cвязность и что это означает**

*Классы должны как можно меньше знать друг о друге.*

**[Low-Level] How many times Ruby reads & updates the code before running?**

* God, save "Metaprogramming Ruby"

Shortly: *3 times.*

*Ruby code ->*

**Tokenize:** *reads the text chars and converts them into tokens ->(tokens)*

```ruby
10.times do |n|
# tINTEGER . tIDENTIFIER keyword_do | tIDENTIFIER | 
```

**Parse:** *using Bison (yet-another-compiler-compiler) parses tokens into statements called abstract syntax tree nodes (also includes a meta-data: line numbers, compiler intermediates) ->(AST nodes)*

*Actually Bison doesn't process tokens, it just creates a parsing code file `/parse.c` from a rules file `/parse.y` during the build process.*

*And after that using Grammar Rules described in `parse.c` we convert tokens into a complex internal data structure called AST.*

```Ruby
Program # 10.times { |n| puts n }
   |
 method
:add_block
   |
  call
  ---------------------
   |        |         |
integer    period  identifier
  10        .        times
```

**Compile:** *compiles AST nodes into low-level instructions->
YARV(yet-another-ruby-vm) Instructions*

```ruby
2+2
# [AST Node]                                [YARV]
# NODE_SCOPE (table: [none], args: [none]) | putself
# |--NODE_FCALL (method id: puts)          | <callinfo mid:puts
#     |--NODE_CALL (method id: +)          | <callinfo mid:+
#         |--NODE_LIT 2                    | putobject 2
#         |--NODE_LIT 2                    | putobject 2
```

**[Low-level] What is method dispatch?**

*In case of languages with a single inheritance (like Ruby), method dispatch is the way language looks for the method definition. If the method is defined on that class, that method is invoked. Else the language will lookup for this definition through class ancestors up to the unique supercall.*

```ruby
# read from the bottom
BasicObject # or finally here
|
Kernel # or here
|
Object # if was not overwrited, will check here
  |
  |----ExampleClass #lookup if method was overwrited

```

***

## Practical questions

**What is the difference between :include and :extend in Ruby?**
*:include is being used for instance methods (i for I) and :extend for class methods.*

**What is the difference between Proc and Lambda?**

**Типы данных в Ruby**
- Nil/True/False class
- Numeric (integer/float/complex)
- Time/Date
- String
- Array/Hash/Range
- Struct


**What is Mixin?**

**Multiple inheritance in Ruby**

**The most known/used metaprogramming example in Ruby**

*attr_accessor, attr_reader, attr_writer*

**What are unit tests? And when do we use them?**

**Где хранить бизнес логику(толстые модели/толстые контроллеры)**

*В сервисах или интеракторах/оперейшенах.*

**Basic Ruby/Rails patterns:**
- Interactor
- FormObject
- ServiceObject

**What is MVC**

## Interview-coding

**Варианты реализации факториала:**

- classic
  ```ruby
  def fact(n)
    return 1 if n.zero?

    (1..n).inject(:*)
  end
  ```
- recursion
  ```ruby
  def rec_fact(n)
    return 1 if n.zero?

    return n * rec_fact(n-1)
  end
  ```


**Some JS:**

What is the difference between `let`, `const`, `var` in JS.

Как реализован ООП в классическом JS.

In cookie-based session management, who sets the session token?