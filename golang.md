# Cheat Sheet (Golang)

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
- [Golang](golang.md)
  - [Channels](#channels)
  - [Concurrency and Parallelism](#concurrency-and-parallelism)
- [Javascript](#javascript)
- [Network & DevOps](#network)

# <a id="golang"></a> Golang

### <a id="channels"></a> Channels

#### 1. What are channels used for?

#### 2. Buffered and Unbuffered channels. What are they good for?

#### 3. What happens if you write to closed channel? And what if you read from it?

#### 4. Runtime in Golang. What is the difference between threads and goroutines?


 Базовые типы данных (слайсы, массивы, хеш мапы)

 Можно ли создать map не через make


 Сложность чтения/записи и тд в мапе, хеширование, коллизия хешей


 5. Мьютексы что и зачем
6. Sync примитивы (я назвал только 5), но их больше – Мьютекс Вейтгрупп Канал CondVar и Once

Отличие горутин от тредов – размер, go scheduler, go runtine, механизм кражи горутин


11. Конструкция select – что зачем и почему, будет вопрос – а если два сигнала из двух каналов упали в один select какой из case сработает – ответ: неопределенно


13. Что сделает шедьюлер гоу если случится затык на net/IO запросе
14. Буфферизированные и небуферизированные каналы
15. Выполнится ли defer при SIGTERM, а при panic?
16. Чтение из закрытого канала


1. Почему го такой быстрый по сравнению с другими языками? 

2. Какие операции с каналами и в каких ситуациях вызовут панику?

3. Что делает функция make() изнутри?
4. Как работает планировщик горутин? 
5. В чём разница между планировщиком го и планировщиком операционной системы?

6. Как можно читать из канала?
7. Как работает сборщик мусора?
8. Написать семафор в голанге



What happens if you write to closed channel? And what if you read from it?

If you write to a closed channel in Go, it will result in a panic with the message "send on closed channel". This is because attempting to write to a closed channel is considered a runtime error.

Similarly, if you try to read from a closed channel in Go, you will not receive any data and the value that is received will be the zero value of the channel's element type. 