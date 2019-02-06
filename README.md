# Questions
## What is the difference between include and extend in Ruby?

## Why do we need transaction when getting data from database?

## What is AREL?
Shortly: A RELational algebra

Description: 
Arel is a Relational Algebra for Ruby. It simplifies the generation complex of SQL queries.
*Now is bundled inside Rails Active Record.
for use in constructing SQL queries. Every time you pass a hash to where, it goes through Arel eventually. Rails exposes this with a public API that we can hook into when we need to build a more complex query.
It’s all about providing frameworks with a way of building and representing SQL queries. It’s not the kind of library you would typically want to use directly (although you could as shown in a minute). Arel is meant to be the basic building block upon which frameworks build their own APIs that are more suitable for the end user.
## What is Mixin?

## 

# Interview-coding




3 принципа ООП
как инкапсуляция работает в руби
Кому принадлежат методы объекта(ответ что они принадлежат все равно классу, видимо потому что в кишках руби они хранятся в структуре класса)
SOLID как расшифровываются буквы
Слышал ли ты про связность и что это означает(просто сказал что классы должны как можно меньше знать друг о друге)
Что такое unit тесты
Где хранить бизнес логику(толстые модели/толстые контроллеры, я сказал ни там ни там, в сервисах или интеракторах/оперейшенах)
Что такое MVC
когда добавляют индексы в базу и зачем они нужны
Как устроен btree и как функция защищает от коллизий


Антон, [21 авг. 2018 г., 12:24:49]:
ну что индекс это обмен памяти на скорость, и базе данных вместо того чтобы использовать полнотекстный поиск достаточно пройтись по индексу, это будет значительно быстрее

что индексы влияют на скорость апдейта и инсерта, потому что при каждом изменение таблицы нужно перестраивать индекс