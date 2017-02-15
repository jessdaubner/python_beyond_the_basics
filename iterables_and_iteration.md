## Iterables and Iteration

### Multi-Input Comprehensions
* Comprehensions are a short-hand syntax for creating collections and iterable objects of various types
* For example a list comprehension creates a new list object from an existing sequence and comprehension syntaxes for creating dictionaries, sets and generators exist as well 
```
l = [i * 2 for i in range(10)]
d = {i: i*2 for i in range(10)}
s = {i for i in range(10)}
g = (i for i in range(10))

```
* Comprehensions can use multiple input sequences and multiple if-clauses
* Read multi-input comprehensions as a set of nested for loops where the later `for` clauses are nested inside the earlier `for` clauses
* Result expression of the comprehension is executed inside the innermost or last `for` loop
```
[(x,y) for x in range(5) for y in range(3)]

points = []
for x in range(5):
    for y in range(5):
        points.append((x,y))
```
#### Benefits of Comprehensions
* Obvious benefit is that the list variable does not need to be created initially and then repeatedly append elements to it (i.e., container is populated "automatically")
* Allows Python to optimize creation 
* More readable
```
values = [x / (x-y)
          for x in range(100)
	  if x > 50
	  for y in range(100)
	  if x - y != 0]
[(x,y) for x in range(10) for y in range(x)]
```
* Later clauses can refer to variables bound in earlier clauses

### Nested Comprehensions
* Comprehensions can be *nested* inside other comprehensions
* Comprehensions can form the output expression of a comprehension (e.g., each element of the collection produced by a comprehension can itself be a comprehension) 
```
vals = [[y * 3 for y in range(x)] for x in range(10)]

outer = []
for x in range(10):
    inner = []
    for y in range(x):
	inner.append(y*3)
    outer.append(inner)
```
* All comprehensions nest in the same way
```
{x * y for x in range(10) for y in range(x)}

((x,y) for x in range(10) for y in range(x))
```

### The `map()` function
#### Iteration and Iterables + Building-Block Functions
* Python's concept of iteration and iterables is a sequence of elements that can be accessed one at a time in order
* This high level of abstraction allows us to develop tools that work on iterables at an equally high level
* Python provides a number of functions that serve as building-blocks for combining and working with iterables in sophisticated ways
* Many of these ideas were originally developed in the functional programming community, so people refer to the use of these techniques as *functional-style* Python

* `map()` is one of the most widely recognized functional programming tools in Python
* `map()` applies a new function to every element in a sequence, producing a new sequence; that is, we map a function over a sequence to produce a new sequence
* `map()` performs lazy evaluation; it doesn't produce any output until it's needed 
* In other words, `map()` will not call its function or access any elements from its input sequence until they're actually needed; the map object returned by map is itself an iterator object and only by iterating over it can you start to produce output
* `next()` iterates over the next object
* usually a higher-level method is used to iterate over the map object, such as the list constructor `list()` or a `for-loop`
* main idea: lazy evaluation requires iterating over the return object of `map` in order to access the values of the new sequence; until the map object is iterated over, the sequence is not evaluated 
```
# gets unicode endpoint for each character
list(map(ord, 'The quick brown fox'))
for o in map(ord, 'The quick brown fox'):
    print o
```

### Multiple Input Sequences
* `map()` can accept *any number* of input sequences
* The number of input sequences must *match* the number of function arguments
```
sizes = ['small', 'medium', 'large']
colors = ['lavender', 'teal', 'burnt orange']
animals = ['koala', 'platypus', salamander']
def combine(size, color, animal):
    "{} {} {}".format(size, color, animal)
list(map(combine, sizes, colors, animals))
```
* If the sequences are not all of the same size, `map()` will terminate when a sequence is terminated

### `map()` versus Comprehensions
* `map()` provides some of the same functionlity as comprehensions
* In cases where both will work, there is no clear choice which is better. Neither approach is necessarily faster.
```
[str(i) for i in range(5)]
list(map(str, range(5)))
```
### The `filter()` function
* `filter()` applies a function to each element in a sequence, constructing a new sequence with the elements for which the function returns `True`
* similar to `map()`, `filter()` is evaluated lazily and evaluates each element in a sequence
* unlike `map()`, `filter()` only excepts a single input sequence thus the function supplied can also only take one argument
* `filter()` returns an iterable object of type filter

```
filter(is_odd, [1,2,3,4,5])
list(filter(lambda x: x > 0, [0, 1, -5, 2, -2]))
```
* Passing `None` as the *first argument* to `filter()` will remove elements that evaluate to `False`
* Recall that `0`, `''`, `[]`, and `False` are all "falsey"
* In *Python 2* `map()` and `filter()` functions are *eagerly* evaluated and return `list` objects

### The `functools.reduce()` function
* `functools.reduce()` repeatedly applies a function to the elements of a sequence, reducing them to a single value
* that is, it repeatedly applies a function of two arguments to an interim accumulator value and each value of the series in turn updating or accumulating the interim value at each step with the result of the called function, ultimately the final value of the accumulator is returned thereby reducing the value to a single value
* `reduce` is the same as `fold` in functional-programming or `std::accumulate()` in C++ STL
* the canonical example of `reduce` is the summation of a sequence
```
from functool import reduce
import operator
reduce(operator.add, [1, 2, 3, 4, 5])
```
* The standard library `operator` module contains function equivalents of infix operators: `a + b` is equivalent to `operator.add(a,b)`
* Iterim result is passed as the first argument to the reducing function and the next value in the input sequence is passed as the second
* Sequence passed to `reduce` cannot be empty
* Sequence of one element returns the element without calling the reducing function
* Optional inital value is conceptually just added to the start of the input sequence; serves as the first accumulator value for the function; useful when you cannot be sure that your sequence will have any values
* Use intial values of 0 for summation and 1 for products

### Combining `map()` and `reduce()`
* Python's map and reduce are the same as the terms in the popular map-reduce algorithm
```
def count_words(doc):
    normalised_doc = ''.join(c.lower() if c.isalpha() else ' ' for c in doc)
    frequencies = {}
    for word in normalised_doc.split():
        frequencies[word] = frequencies.get(word, 0) + 1

documents = [
    'It was the best of times, it was the worst of times.',
    'I went to the woods because I wished to live deliberately, to front only the essential facts of life...',
    'Friends, Romans, countrymen, lend me your ears; I come to bury Caesar, not to praise him.' 
    'I do not like green eggs and ham. I do not like them Sam-I-Am.'
]

counts = map(count_words, documents)

def combine_counts(d1, d2):
    d = d1.copy()
    for word, count in d2.items():
        d[word] = d.get(word, 0) + count
    return d

>>> from functool import reduce
>>> from map_reduce import combine_counts, counts
>>> total_counts = reduce(combine_counts, counts)
```
### The Iteration Protocols

### Putting the Protocols Together

### Alternative Iterable Protocol

### Extended `iter()` format

