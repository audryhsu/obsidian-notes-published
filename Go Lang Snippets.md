---
creation date: 2022-03-22 08:39
aliases: 
tags: 🖥️
---

#### [Go Lang Snippets](Go%20Lang%20Snippets.md)

###### Initialized If
```go
if err := process(); err != nil {
	return err
}
```

###### Passing pointers of Structs as function arguments
```go
func main() {
  goku := Saiyan{"Goku", 9000}
  Super(&goku) // pass the pointer
  fmt.Println(goku.Power) // 19000! 
}

// expects a pointer
func Super(s *Saiyan) {
  s.Power += 10000
}
```

###### Struct Constructors
We can use the `new` to create a new struct instance, or use a factory function; these are the same.
```go
type Saiyan struct {
	name  string
	power int
}
func main() {
	goku := new(Saiyan) 
	goku.name = "goku"
	goku.power = 9001
	
	// same as:
	goku := &Saiyan {
		name: "goku"
		power: 90001
	}
}
```

###### Strings and Byte Arrays
```go
func main() {
	stra := "the spice must flow"
	byts := []byte(stra) // [116 104 101 32 115 112 105 99 101 32 109 117 115 116 32 102 108 111 119]
	strb := string(byts) //the spice must flow

	fmt.Println(byts)
	fmt.Println(strb)
}
```

###### Jedi, Saiyan, Person, Fighters
```go
type Person struct {
	Name string
}

type Saiyan struct {
	*Person
	Power int
	Hair  string
}
type Jedi struct {
	*Person
	Power  int
	Knight bool
}

func (p *Person) Greet() {
	fmt.Printf("My name is %s\n", p.Name)
}

func (j *Jedi) LevelUp() {
	j.Power *= 2
	j.Knight = true
	fmt.Printf("%v power increased to %v! I am a %t Jedi Knight\n", j.Name, j.Power, j.Knight)
}

func (s *Saiyan) LevelUp() {
	s.Power += 50
	s.Hair = "gold"
	fmt.Printf("%v power increased to %v! Hair is now %v\n", s.Name, s.Power, s.Hair)
}

type Fighters interface {
	LevelUp()
}

func main() {
	anikin := &Jedi{
		Person: &Person{"Anikin Skywalker"},
		Power:  80,
		Knight: false}
	goku := &Saiyan{
		Person: &Person{"Goku"},
		Power:  1000,
		Hair:   "black"}

	anikin.Greet() // My name is Anikin Skywalker
	goku.Greet() // My name is Goku

	heros := []Fighters{anikin, goku}

	for _, hero := range heros {
		hero.LevelUp()
	}

}
```

###### Shapes Interfaces
Shapes Interface
```go
// interfaces let us create data structures of different struct types that share properties
type shape interface {
	area() float64
}

type circle struct {
	radius float64
}

type rect struct {
	width  float64
	height float64
}

func (r rect) area() float64 {
	return r.width * r.height
}

func (c circle) area() float64 {
	return c.radius * c.radius
}

// Because circle and rect both have a shared area method, they can belong to the shape interface
func main() {
	c1 := circle{4.5}
	r1 := rect{5, 7}

	// Now we can create a slice containing Shape types 
	shapes := []shape{c1, r1}
	
	// loop through shapes slice and print area
	for _, shape := range shapes {
		fmt.Println(shape.area())
	}
}
```