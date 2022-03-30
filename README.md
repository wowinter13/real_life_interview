# Technical Interview Cheat Sheet

A collection of questions from different technical interviews.

***

## Contributing
This is an open source, community project, and I am grateful for all the help I can get. If you find a mistake make a PR and please have a source so I can confirm the correction. If you have any suggestions feel free to open an issue.

***

# Table of Content
- [Common](#common)
  - [Algorithms](#algorithms)
  - [Concurrency and Parallelism](#concurrency-and-parallelism)
  - [System Design](#system-design)
- [Databases](#databases)
  - [Common](#common-databases)
  - [Postgresql](#postgresql)
  - [NoSQL](#nosql-databases)
- [Ruby on Rails](#ror)
  - [Ruby](#ruby)
  - [Rails](#rails)
  - [Metaprogramming](#metaprogramming)
  - [Concurrency and Parallelism](#rb-concurrency-and-parallelism)
  - [Ancient Magic](#ancient-magic)
- [Golang](#golang)
  - [Channels](#channels)
  - [Concurrency and Parallelism](#concurrency-and-parallelism)
- [Javascript](#javascript)

***

# <a id="common"></a> Common
### <a id="algorithms"></a> Algorithms

#### 1. Reverse a linked list

#### 2. Quick Sort

#### 3. Bubble Sort

### <a id="concurrency-and-parallelism"></a> Concurrency and Parallelism

#### 1. Threads vs Processes

<img width="560" height="544" src="https://raw.githubusercontent.com/wowinter13/real_life_interview/master/thread-vs-process.png">

#### 2. Data races

A data race is a situation that occurs when multiple threads are running the same program and 2 or more threads get access to the same shared variable, at the same time. Cases like this can be prevented using locks, so the access to the variable will be locked until one of the threads completes its operation.

#### 3. Deadlocks

This is a situation where two or more threads are blocked permamently because they are waiting for each other.

***


### <a id="system-design"></a> System Design

**Распределенные системы**

1. Replication
хранение копий одних и тех же данных на нескольких машинах, i.e. дубликация

Решает проблему географии (когда пользователи разбросаны по миру), проблему отказа одной из систем (высокая доступность) и простое горизонтальное масштабирование (обработка большего количества запросов).

Сложность репликации – изменение данных на всех узлах.

По типам разделяют репликацию с одним ведущим узлом (single leader), с несколькими ведущими узлами (multi-leader) и без ведущего узла (leaderless).

Те узлы, которые хранят копии называются реплики.

Базовый вариант реализации master/slave (leader-based replication). Лидер узла отвечает за запись данных в свое локальное хранилище, а затем используя журнал репликации (replication log) рассылает все обновления в ведомые узлы (followers). Данная реализация доступна во всех современных БД и некоторых брокерах сообщений.

По типам выполнения разделяют синхронную, асинхронную и полусинхронную репликацию.

Синхронная репликация обеспечивает гарантии актуальных данных между узлами, но в некоторых случаях операция записи может занимать долгое время. Проблема может возникнуть, если follower реплика не отвечает и произойдет rollback на ведущей реплике.

Чаще используют semi-синхронную репликацию, в данном случае синхронное обновление происходит только на одном ведущем и ведомом узле.

В случае отказа ведомого узла, проблема не возникает – ведущий узел хранит лог изменений и воспроизведет его при рестарте. В случае отказа ведущего узла, можно временно можно назначить ведомый узел в качестве ведущего.


Репликацию с несколькими ведущими узлами используют в развертывании систем, расположенных в нескольких ЦОДах.
В postgres это BDR (bi-directional replication). Чаще всего конфликты между асинхронными записями решаются алгоритмом LWW (last write wins).

Без ведущего узла – экспериментальный вариант, используется в Dynamo-подобных системах. Запросы падают сразу на все узлы.

2. Партицирование (шардирование, секционирование)

Секционирование это разбиение базы на отдельные кусочки. Снова же основная цель партицирования – масштабируемость системы, применяется когда набор данных настолько велик, что репликации становится недостаточно. Основная идея заключается в том, что при партицировании не используется разделение ресурсов.

Цель секционирования — равномерно распределить по узлам данные и загрузку по запросам. В случае если нагрузка распределена неравномерно, секционирование называют ассиметричным (skewed), а секцию с максимальной нагрузкой называют hot spot. Партицирование может быть как и автоматическим (по хеш-ключу), так и ручным. Ручное устанавливается по каким-либо диапазонным значениям. Для распределения запросов используется балансировщик нагрузок.

Индексы также требуется секционировать – локальные в рамках секции и глобальные между секциями.

**В случае postgresql часто используется связка partman/pg_cron для автоматического партицирования данных в какой-то период. (PARTITION BY)**



# <a id="databases"></a> Databases

### <a id="common-databases"></a> Common


**Types of indexes**

1. Hash-indexes (key-value)

Used for columns containing unique data. 

A hash index stores keys by dividing them into smaller buckets, where each bucket is given an integer ID-number to retrieve it quickly when searching for a key’s location in the hash table. The buckets are stored sequentially on a disk. Fast read, cheap storage. But it is impossible to store duplicate keys within a bucket.

2. LSM-tree (Log-Structured Merge-Tree)

3. B-trees (balanced tree)

The most popular index type.

Balanced tree is balanced because compared to hash or log-strucured indexes, B-trees always split keys by dividing them into chunks (page) with a fixed size (about 4Kb). Each page has a pointer (link) on the disk, and can be a reference to another one page. The links count for child elements called is called branching factor (for the sake of simplicity, depth).

**ACID**

ACID – atomacity, consistency, isolation, durability (атомарность, консистентность, изоляция, сохраняемость).

**А**томарность – прерываемость. Возможность прервать транзакцию при ошибке. Принцип "Все или ничего".

**C**onsistency – гарантия целостности данных. Можно сказать что если транзакция начинается при допустимом состоянии базы данных, то можно быть уверенным, что и после состояние БД будет соответствовать изначальным инвариантам (изначальным утверждениям).

**I**solation – гарантирует, что конкурентные операции изолированы друг от друга. 

**D**urability – гарантирует, что данные не будут потеряны после успешно зафиксированных операций даже в случае сбоя самой БД.

### <a id="postgresql"></a> Postgresql

#### 1. Do you know what is PGQ? Other queues in Postgres?


#### 2. Postgres. Indexes.

По типам индексов, их там в PG штук 5, но я лично сталкивался в миграциях только с btree и ginом.
btree это дефолтный индекс – balanced дерево. Получается работает как придавленное нормальное распределение, то есть, у нас очень ветвистое дерево, весь кайф в балансе, то есть глубина на всех участках одинаковая. Собственно по понятным причинам подходит для сортируемых данных.
gin - это базовый обратный индекc, там концепция обратная – мы храним не значения по индексам, а индексы по значениям. 
Еще там есть Brin, как Сергей Брин, но индекс, Gist, хеш-индексы, но детали не изучал ибо не использовал осознанно.


### <a id="nosql-databases"></a> NoSQL

#### 1. What are types of NoSQL databases?

graph stores, key-value stores, document stores, column stores.


***

# <a id="ror"></a> Ruby on Rails

### <a id="ruby"></a> Ruby

#### Memory Allocation. For instance, on arrays. Why does it works efficiently while Ruby is dynamically typed?

Ruby allocates memory for an array, but it does not take into account the final string length. And when we add new data, then the entire memory object is transferred. Ruby allocates a cell with a larger size and trasfers the entire array to this cell. How to solve the memory allocation problem? Well, in fact, it is enough to set the initial length of the array.

Why does it works efficiently? Thanks to the mechanism of special links called pointers. We only store references to the object.

### DB Questions

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

Транзакции обеспечивают гарантии функциональной безопасности. Упрощают обработку ошибок конкурентного доступа. 
В случае состояния гонки это позволить сделать `LOCK` базы для получения ожидаемого результата.

Если более точно, то существует 4 уровня изоляции данных. По дефолту в PG используется `read commited`, который читает записи закомиченные до исполнения запроса. Транзакция позволяет исполнять запрос в рамках одного соединения и получать корректные данные на выходе.

**Что такое индекс, когда добавляют в базу и зачем они нужны**

Индекс — дополнительная структура, производная от основных данных.

Индекс это обмен памяти на скорость, и базе данных вместо того чтобы использовать полнотекстный поиск достаточно пройтись по индексу, это будет значительно быстрее.

Индексы влияют на скорость апдейта и инсерта, потому что при каждом изменение таблицы нужно перестраивать индекс.

**Как устроен btree и как функция защищает от коллизий**


**What is pgBouncer? When should we use it?**

Надстройка управляющая пулом соединений к Postgresql. Создает сеансы(сессии) для пользователей бд в рамках пула открытых соединений, что позволяет не открывать новое соединение для каждого из новых пользователей.

Использовать имеет смысл, когда база упирается в высокое количество клиентских соединений из-за чего транзакция занимает миллисекунды, но создание соединения секунды.

**Postgres replication (like: master-slave)**


**Базовые уровни изоляции транзакций**

- `Read Commited`
    Default reading mode. Гарантирует, что любые закомиченные данные будут прочитаны. Вызывает lock на запись в таблице -> ставит в очередь другие транзакции. Запрещает Dirty Read. Обычно реализуется через блокировку  транзакцией записи до модификации значения.
- `Read Uncommitted`
    Разрешает Dirty Read. Самый низкий уровень изоляции. Позволяет читать еще не закомиченные данные. Транзакции не изолированы друг от друга.
- `Repeatable Read`
    Транзакция вызывает lock на все записи на чтение и обновление в таблице, тк другие транзакции не имеют доступ к данным -> исключается аномалия `Non Repeatable read` (когда при двойном прочтении транзакция получает разный результат)
- `Serializable`
    Доступ, используемый в программах для создания дампов БД. Максимальный уровень изоляции транзакций. Граф сериализации выполняется последовательно -> становятся недопустимы любые аномалии данных. Последовательное исполнение транзакций. 

***

### Theoretical questions

**Кому принадлежат методы объекта класса**

Они принадлежат все равно классу, потому что хранятся в его структуре.

**3 принципа ООП:**


**How incapsulation works in Ruby?**

**5 principles SOLID:**

*SOLID is an acronym for 5 basic OOP principles based on making the code more understandable, flexible and maintainable.*

- **S(ingle resposibility)**
  One class should have only one responsibility.
- **O(pen-closed)**
  Class should be open for extension, but closed for modification.
- **L(iskov substitution)**
  All subtype class instances should not break the behavior of the original ancestor class, so they can be substituted.
- **I(nterface segregation)**
  Many interfaces of special usage (client-specific) are better than one universal and abstract interface.
- **D(ependency inversion)**
  Abstractions should not depend on details. Details should depend on abstractions.

**Cвязность и что это означает**

*Классы должны как можно меньше знать друг о друге.*


***

### Practical questions

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


### <a id="metaprogramming"></a> Metaprogramming

#### What do you know about code generators? Kernel.eval, class_eval, instance_eval

`eval` is ideal for adding methods to a class dynamically. This functionality becomes very useful when we need to create gems.
For instance, `attr_accessor` is the most famous example of code generation:

```ruby
def attr_accessor(accessor_name)
  self.class_eval %{
    def #{accessor_name}
      @#{accessor_name}
    end
    def #{accessor_name}=(value)
      @#{accessor_name} = value
    end
  }
end
```


#### What is Ruby's :method_missing?

:method_missing is defined in `BasicObject`.

It allows us to:
  1. Handle errors
  2. Delegate methods
  3. Build DSLs

When an object calls a method, Ruby traverses a method lookup path through object's ancestors.

To pass control to the original `method_missing` we may use `super`. Also, it's a good practice always to override `respond_to_missing?` method.

**First**. It allows us to handle errors in a friendlier way.

```ruby
class CustomClass

  def method_missing(method_name, *args)
    message = "Custom message"
    raise NoMethodError, message
  end
end
```

**Second**. It allows us to call or/and delegate methods.

```ruby
class Number
  attr_accessor :value

  def initialize
    @value = 0
  end

  def method_missing(method_name, *args, &block)
    if method_name ~= /add_(.*)/
      public_send("#{Regexp.last_match(1)}=", *args)
    else
      super
    end
  end

  def respond_to_missing(method_name, include_private = false)
   method_name ~= /add_(.*)/ || super
  end
end

num = Number.new
num.add_value(1) # increment
num # => 1
num.respond_to?(:add_value) # => true
```


**Third**. It allows us to create DSLs (*domain-specific language*). Check any of the most popular gems: Rack, XML, Rails Routing, Sinatra, factory_bot, interactors. In all of them you would be able to find some footprints of `method_missing` and other metaprogramming tweaks.

### <a id="rb-concurrency-and-parallelism"></a> Concurrency and Parallelism

#### GIL. Threads.

GIL (global interpretation lock) is a thread synchronization mechanism used in Ruby that helps to avoid data conflicts when more than 1 thread is accessing the same area of memory.

And threads are used to parellelize computations. Threads are placed in a special context and can share the same memory.

### <a id="ancient-magic"></a> Ancient Magic (Ruby under a Microscope)


#### How many times Ruby reads & updates the code before running?

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


**Ruby Garbage Collector**
GC collects all unused objects and frees memory of them. Basically, GC will delete object, if there are no more calls to it in the program.


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

***

# <a id="golang"></a> Golang

### <a id="channels"></a> Channels

#### 1. What are channels used for?

#### 2. Buffered and Unbuffered channels. What are they good for?

#### 3. What happens if you write to closed channel? And what if you read from it?



### <a id="concurrency-and-parallelism"></a> Concurrency and Parallelism

#### 1. Mutex & RWMutex. What is the difference between them?


***
# <a id="javascript"></a> Javascript

#### What is the difference between `let`, `const` and `var` in JS.

#### How does OOP implemented in vanilla JS?

#### In cookie-based session management, who sets the session token?





// to sort
#### Why gRPC is better than our lovely REST? Why gRPC is a perfect choice for microservices? Do you know any useful features?

gRPC is a perfect choice for microservices simply because of being lightweight and fast. Talking about the advantages:
1. Metadata. Instead of using HTTP requests. Metadata is much simplier and perfectly fits for internal communication.
2. Streaming (thanks to HTTP 2.0). gRPC provides all streaming types (client, server, bidirectional).
3. Interceptors. The way gRPC allows you to modify and change requests/responses right out of the box. (read: middlewares)
4. Load Balancing.
5. Call Cancellation. You can simply kill a gRPC call if you don't need a response anymore.