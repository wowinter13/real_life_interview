## Ruby

***

### Table of Content
  - [Base](#base)
  - [Algos and Data Structures](#algos-and-data-structures)
  - [Rails specific questions](#rails-specific)
  - [Concurrency and Parallelism](#rb-concurrency-and-parallelism)
  - [Deep theory](#deep-theory)

***

### <a id="base"></a> Base

**What is the difference between :include, :extend and :prepend in Ruby?**

`include` is being used for instance methods.

`extend` – for class methods. Going above the class in a method dispatch tree. (Class -> Extend -> ... -> Kernel -> BasicObject)

`prepend` – for class methods. Going below the class in a method dispatch tree. (Prepend -> Class -> ... -> Kernel -> BasicObject)

***

### <a id="algos-and-data-structures"></a> Algos and Data Structures

**Memory Allocation. For instance, on arrays. Why does it works efficiently while Ruby is dynamically typed?**

Ruby allocates memory for an array, but it does not take into account the final string length. And when we add new data, then the entire memory object is transferred. Ruby allocates a cell with a larger size and trasfers the entire array to this cell.
**How to solve the memory allocation problem?**
Well, in fact, it is enough to set the initial length of the array.

**Why does it works efficiently?**
Thanks to the mechanism of special links called pointers. We only store references to the object.


**Ruby data types**
- Nil/True/False class
- Numeric (integer/float/complex)
- Time/Date
- String
- Array/Hash/Range
- Struct


***


### <a id="rails-specific"></a> Rails specific questions

**.include & .joins**

`include` - This method is used for eager loading of associations. Used to solve N+1 situations. (`left outer join`)

`joins` - by default performs `inner join` of records. According to Rails guide we  should use joins, if any queries were made on related records.

**delete_all vs destroy_all**

`delete_all` executes as a single query (`SQL DELETE`) и will destroy all records. Ignores callbacks.

`destroy_all` instantizes every record and deletes them in a sequence according to the query.

***

### <a id="rb-concurrency-and-parallelism"></a> Concurrency and Parallelism


***

### <a id="deep-theory"></a> Deep theory

**What do you know about code generators? Kernel.eval, class_eval, instance_eval**

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


**What is method dispatch?**

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