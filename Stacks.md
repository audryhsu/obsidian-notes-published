---
creation date: 2022-03-31 08:18
aliases: 
tags: 🖥️
---

###### Description [Stacks](Stacks.md)


###### Implementation
```go
type Stack struct {
	Data []int
}

func (s *Stack) push(n int) {
	s.Data = append(s.Data, n)
}

func (s *Stack) pop() int{
	last := s.Data[len(s.Data)-1]
	s.Data = s.Data[:len(s.Data)-1]
	return last
}

func (s *Stack) read() int{
	fmt.Println(s.Data[len(s.Data)-1])
	return s.Data[len(s.Data)-1]
}

func main() {
	stack := new(Stack)
	stack.Data = []int{0}
	stack.push(5)
	fmt.Println(stack.Data)
	stack.pop()
	fmt.Println(stack.Data)
	stack.read()
}
```

