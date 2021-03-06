# Maps

### Initialization

In Python, a collection of `(key, value)` pairs with unique keys is known as a dictionary. It is a built-in type called [`dict`](https://docs.python.org/3/library/stdtypes.html?highlight=dict#dict) and is declared either of the following ways (although the first is far more prominent):

```python
aliases = {}
aliases = dict()
```

This same structure exists within Go as a [`map`](https://golang.org/ref/spec#Map_types). But since Go is a typed language, maps have types for both keys and values:

```go
var aliases map[string]string
```

However, attempting to insert into this variable will cause a runtime panic:

```go
aliases["Batman"] = "Bruce Wayne"
```
```
panic: assignment to entry in nil map
```

Before use, it must be "made" with the built-in function `make`:

```go
aliases = make(map[string]string)
```

This declare and make syntax is clunky. Map initialization is better done with [short variable declarations](https://golang.org/ref/spec#Short_variable_declarations) - if possible - by using either of the following:

```go
aliases := map[string]string{}
aliases := make(map[string]string)
```

Since type is inferred in the short form, it is impossible to use an un-made map. Any syntax mistakes made during declaration become compile-time errors:

```go
aliases := map[string]string
```
```
syntax error: unexpected semicolon or newline, expecting {
```

Using the first short format also allows the declaration of initial values, much as like python:

```go
aliases := map[string]string{
    "Superman": "Clark Kent",
}
```

Declaring duplicate keys during initialization is a compile-time error:

```go
aliases := map[string]string{
    "Green Lantern": "John Stewart",
    "Green Lantern": "Hal Jordan",
}
```
```
duplicate key "Green Lantern" in map literal
```

In contrast, Python allows duplicate keys during initialization, and will use the last given duplicate as the key's value:

```python
aliases = {
    "Green Lantern": "John Stewart",
    "Green Lantern": "Hal Jordan",
}
```
```python
{'Green Lantern': 'Hal Jordan'}
```

Although Python dictionaries are untyped - not all types are valid keys. Only hashable Python types can be used as keys, which excludes `list`, `set`, and other dictionaries. Using these types as keys will generate a runtime `TypeError` exception:

```python
aliases[dict()] = 'value'
```
```
TypeError: unhashable type: 'dict'
```

There are also illegal key types in Go, but using them will generate a compile-time error:

```go
var nested map[map[string]string]string
```
```
invalid map key type map[string]string
```

In Python, [`frozenset`](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset), [`tuple`](https://docs.python.org/3/library/stdtypes.html#tuples), and custom classes can be used to provided multi-dimensional keys. In Go, this can also done with using custom `struct` types or using [arrays](https://golang.org/ref/spec#Array_types) - which are hashable - as opposed to [slices](https://golang.org/ref/spec#Slice_types) - which are not.

```go
matrix := map[[2]int]string{
    [2]int{1, 2}: "Battleship",
}
```

```go
matrix := map[[]int]string{
    []int{1, 2}: "Battleship",
}
```
```
invalid map key type []int
```

*TODO*

- Python can build dictionaries from iterables - Go equivalent?
- Caveats of interface{} as a key in Go - especially vis-à-vis type equality

### Usage

Both Python and Go maps are mutable in the sense that keys can be overwritten post-initialization.

For maps, the built-in length function `len` in Go is equivalent to that in Python:

```go
len(aliases)
```

To delete, Python uses the `del` keyword, whereas Go uses another built-in function called `delete`:

```go
delete(aliases, "Green Lantern")
```

Existence is checked in Python using the `in` operator:

```python
print("Green Arrow" in aliases) # False
```

There is no existence function, operator, or method in Go. Checking existence in Go is further complicated by non-existent keys returning the zero values of their value types:

```go
ages := map[string]int{}
fmt.Println(ages["Bill"]) // 0
```

Existence must be checked by using the two variable assignment syntax:

```go
ages := map[string]int{}
age, exists := ages["Bill"]
fmt.Println(age, exists) // 0, false
```

If the first variable is irrelevant, it can be ignored using the [blank identifier `_`](https://golang.org/ref/spec#Blank_identifier):

```go
ages := map[string]int{}
_, exists := ages["Bill"]
fmt.Println(exists) // false
```

The benefit of Go maps returning zero values for non-existent keys is that many values can be modified without initialization, for example:

```go
villians := map[string][]string{}
villians["Batman"] = append(villians["Batman"], "The Joker", "Ra's al Ghul")
```

The equivalent behavior in Python would require the usage of the `collections` module's [`defaultdict`](https://docs.python.org/3/library/collections.html#collections.defaultdict):

```python
from collections import defaultdict
villians = defaultdict(list)
villians["Batman"].extend(["The Joker", "Ra's al Ghul"])
```

### Iteration

Iterating over a dictionary in Python returns the key as the single iterable variable.

```python
for key in aliases:
    print(key)
```

Using `range` in Go will also return the key:

```go
for key := range aliases {
    fmt.Println(key)
}
```

But iteration over key - value pairs is as simple as providing another destination variable:

```go
for key, value := range aliases {
    fmt.Println(key, value)
}
```

Wheres Python requires usage of the [`items`](https://docs.python.org/3/library/stdtypes.html#dict.items) method:

```python
for key, value in aliases.items():
    print(key, value)
```

In the CPython implementation of Python, dictionary iteration can vary, but will remain consistent as long as the dictionary is not modified.<sup>[link](https://docs.python.org/2/library/stdtypes.html#dict.items)</sup> 

Go does not guarantee iteration order, even between back-to-back iterations.<sup>[link](http://golang.org/ref/spec#RangeClause)</sup>


### Methods

Python provides a number of methods for dictionaries, including the `keys` and `values` methods that return a dictionary's keys and values, respectively, as lists.

Go maps have no built-in methods, though the `keys` and `values` methods can be replicated by attaching them to custom types:

```go
type Counter map[string]int

func (c Counter) Keys() (keys []string) {
    for key := range c {
        keys = append(keys, key)
    }
    return
}
```

However, since Go lacks generics, these methods must be declared for each custom type.

From [Golang for Pythonistas](https://github.com/aodin/golang-for-pythonistas)

Happy Hacking!

aodin, 2015
