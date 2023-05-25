


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