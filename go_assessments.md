## Golang Assessments

### Table of Content
  - [Channels](#channels)
	- [Algos](#algos)


### <a id="channels"></a> Channels

**Buffered channels**

```go
func main() {
    c := make(chan int, 1000)
    for i := 0; i < 100; i++ { // 100
       go foo(c) // c <- r
    }
    
    sum := 0
    for r := range c {
        sum += r
    }

    fmt.Println(sum)
}

func foo(c chan int) {
    r := rand.Int() // (1..100)
    for i := 0; i < r; i++ {
        c <- r
    }
}
// We need to close channels.

func main() {
	c := make(chan int, 1000)
	var wg sync.WaitGroup

	for i := 0; i < 100; i++ {
		wg.Add(1)
		go foo(c, &wg)
	}

	go func() {
		wg.Wait()
		close(c)
	}()

	sum := 0
	for r := range c {
		sum += r
	}

	fmt.Println(sum)
}

func foo(c chan<- int, wg *sync.WaitGroup) {
	defer wg.Done()
	r := rand.Intn(100) + 1
	for i := 0; i < r; i++ {
		c <- r
	}
}

```


### <a id="algos"></a> Algos

Find duplicates in slice

```go

func countOccurrences(slice []int) map[int]int {
	countMap := make(map[int]int)
	for _, entry := range slice {
		countMap[entry] += 1
	}
	return countMap
}

// OR

func findDuplicates(slice []int) []int {
	duplicates := []int{}
	keys := make(map[int]bool)

	for _, entry := range slice {
		if _, value := keys[entry]; !value {
			keys[entry] = true
		} else {
			duplicates = append(duplicates, entry)
		}
	}
	return duplicates
}

```

