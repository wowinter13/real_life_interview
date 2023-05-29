### Ruby assessments

**Implement a :factorial function:**

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

**Implement :each_with_index**

```ruby
class Enumerable
  def each_with_index
    index = 0
    array.each do |item|
      yield(item, index)
      index += 1
    end
  end
end
```