# Questions

***


## Common

**Why gRPC is better than our lovely REST? Why gRPC is a perfect choice for microservices? Do you know any useful features?**

gRPC is a perfect choice for microservices simply because of being lightweight and fast. Talking about the advantages:
1. Metadata. Instead of using HTTP requests. Metadata is much simplier and perfectly fits for internal communication.
2. Streaming (thanks to HTTP 2.0). gRPC provides all streaming types (client, server, bidirectional).
3. Interceptors. The way gRPC allows you to modify and change requests/responses right out of the box. (read: middlewares)
4. Load Balancing.
5. Call Cancellation. You can simply kill a gRPC call if you don't need a response anymore.

## Golang


## Ruby/RoR
### DB Questions

**What is AREL?**

Shortly: *A REL*ational algebra

Fully:

Arel is a Relational Algebra for Ruby. It simplifies the generation complex of SQL queries.

Now is bundled inside Rails Active Record.

Being used in constructing SQL queries. Every time you pass a hash to where, it goes through Arel eventually. Rails exposes this with a public API that we can hook into when we need to build a more complex query.

It’s all about providing frameworks with a way of building and representing SQL queries. It’s not the kind of library you would typically want to use directly (although you could as shown in a minute). Arel is meant to be the basic building block upon which frameworks build their own APIs that are more suitable for the end user.

**What is N+1 query?**

The N+1 query is a query, when we do a one more query for a child-record each time iterating throught parent-records.

The main problem is a reduced performance.


**Include/joins**

`include` - решает n+1, подгружая все записи из связанной таблицы. (`left outer join`)

`joins` - по дефолту осуществляет inner join записей, по Rails guide мы используем joins, если в последующем будут накладываться условия на связанные таблицы. (`inner join`)

**Basic SQL Clauses**
- HAVING
- GROUP

**Join types:**
- INNER
- OUTER
- LEFT RIGHT
- FULL

**Why do we need a transaction when getting data from the database?**

В случае состояния гонки это позволить сделать `LOCK` базы для получения ожидаемого результата.

Если более точно, то существует 4 уровня изоляции данных. По дефолту в PG используется `read commited`, который читает записи закомиченные до исполнения запроса. Транзакция позволяет исполнять запрос в рамках одного соединения и получать корректные данные на выходе.

**Что такое индекс, когда добавляют в базу и зачем они нужны**

Индекс это обмен памяти на скорость, и базе данных вместо того чтобы использовать полнотекстный поиск достаточно пройтись по индексу, это будет значительно быстрее.

Индексы влияют на скорость апдейта и инсерта, потому что при каждом изменение таблицы нужно перестраивать индекс.

**Как устроен btree и как функция защищает от коллизий**


**What is pgBouncer? When should we use it?**

Надстройка управляющая пулом соединений к Postgresql. Создает сеансы(сессии) для пользователей бд в рамках пула открытых соединений, что позволяет не открывать новое соединение для каждого из новых пользователей.

Использовать имеет смысл, когда база упирается в высокое количество клиентских соединений из-за чего транзакция занимает миллисекунды, но создание соединения секунды.

**Postgres replication (like: master-slave)**


**What is PGQ? Other queues in Postgres?**



**Базовые уровни изоляции транзакций**

- `Read Commited`
    Default reading mode. Гарантирует, что любые закомиченные данные будут прочитаны. Вызывает lock на запись в таблице -> ставит в очередь другие транзакции. Запрещает Dirty Read.
- `Read Uncommitted`
    Разрешает Dirty Read. Самый низкий уровень изоляции. Позволяет читать еще не закомиченные данные. Транзакции не изолированы друг от друга.
- `Repeatable Read`
    Транзакция вызывает lock на все записи на чтение и обновление в таблице, тк другие транзакции не имеют доступ к данным -> исключается аномалия `Non Repeatable read` (когда при двойном прочтении транзакция получает разный результат)
- `Serializable`
    Доступ, используемый в программах для создания дампов БД. Максимальный уровень изоляции транзакций. Граф сериализации выполняется последовательно -> становятся недопустимы любые аномалии данных.

***

### Theoretical questions

**Кому принадлежат методы объекта класса**

Они принадлежат все равно классу, потому что хранятся в его структуре.

**3 принципа ООП:**


**Как инкапсуляция работает в Ruby**

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

### Practical questions

**method_missing: what is it?**

Метод класса `BasicObject`, который работает как begin/rescue для вызовов методов и перехватывает несуществующие. Принимает 3 аргумента: название метода, массив аргументов и блок.

Так же в случае патчинга `method_missing` нужно не забывать патчить `respond_to_missing?` - иначе может возникнуть ситуация, когда метод отсутствующий метод обрабатывается, но библиотеки использующие `respond_to_missing?` будут продолжать воспринимать его как отсутствующий.


**delete_all vs destroy_all**

`delete_all` выполняется одним запросом (`SQL DELETE`) и уничтожает все записи. Игнорирует коллбеки.

`destroy_all` инстанцирует каждую запись и удаляет последовательно в соответствии с условиями запроса. 

**What is the difference between :include and :extend in Ruby?**
*:include is being used for instance methods (i for I) and :extend for class methods.*

**What is the difference between Proc and Lambda?**

**Ruby data types**
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

**Where should we store the business logic?(fat models/fat controllers)**

*Better to use some basic patterns like: interactors, services or operations, and to store it here.*

**Basic Ruby/Rails patterns:**
- Interactor
- FormObject
- ServiceObject

**What is MVC**

## Server-side questions

**Чем отличается puma от unicorn?**

**Fork/Thread. Что это и в чем разница?**

`Поток` создается в рамках одного процесса (параллелизм). `Форк` это создание новой отдельной копии процесса.

Потоки расходуют меньше памяти тк не создается новый инстанс задачи.

Но форки процесса наследуют ресурсы родителя, в том числе и коннекшены к базе данных.
Поэтому внутри каждого форка нужно заново переоткрывать соединение к БД.

Используя `MRI` лучше форкать, `jruby` - создавать треды, тк MRI запускает только 1 поток в моменте.

**Зачем нужен NGINX?**

Nginx - веб-сервер. Puma или Unicorn - application-сервер.

В NGINX'е есть базовая защита от DDOS'а.
Он лучше обрабатывает multipart-загрузку файлов.

Служит для обработки статики и ассетов.

Редирект.

**Как работает loadbalancer**

## Patterns

**Form object**

Не паттерн, а подход к обработке данных (по сути это замена permitted_params).

Получаем возможность препроцессить данные, валидировать их (dry-struct, dry-types, dry-validations).

**Decorator**

Декорирует только один объект (добавляет некоторую функциональность) (draper)

**Presenter**

Тоже декоратор, но уже максимально приближенный к вьюхам.

**Policies**

Не назвал бы паттерном, просто подход к делегированию информации и доступов (pundit).

**Serializers**

Способ нормализовать данные и стандартизировать API. (json serializer)

**Interactor, Service object**

Инкапсулирует часть бизнес логики (Command паттерн) (dry-transaction (чейнит шагами) -> Interactor::Organizer + Interactor).

**The difference between Presenter & Decorator**

На каждом проекте по разному делают в итоге. Но однозначно говоря - декоратор добавляет функциональность объекта. А презентер это сервис который подготавливает информацию к отображению (например обработка коллекции из сущностей с информацией из связей). Декоратор - это общее решение: вроде универсального форматирования. А презентер используется для какого-то узкого назначения.


## Interview-coding

**Implementations of :factorial function:**

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

**Given an integer `c`, return `true` if there exist 2 integers, a and b, such that `a * a + b * b =c`**

## Some JS

**What is the difference between `let`, `const`, `var` in JS.**

**How OOP is implemented in vanilla JS.**

**In cookie-based session management, who sets the session token?**