Summary
-------
### Local Functions
* `def` defines a function in the scope in which it's called, and this can be inside other functions
* Functions defined inside other functions are commonly called local functions
* A new local function is created each time the containing function is executed
* Local functions are no different from any other local name bindings and can be treated like any other object
* Local functions can access names in other scopes via the LEGB rule
* The enclosing scope for a local function includes the parameters of the enclosing functions
* Local functions can be useful for code organization
* Local functions are similar to lambdas but are more general and powerful
* Functions can return other functions including local functions defined in their body

```python
def function(a,b):
    print("A function")

    def local_function(x,y):
        print("A local function")
        return x * y + a * b

    return local_function
```

### Closures
* Closures allow local functions to access objects from scopes which have terminated
* Closures ensure that objects from terminated scopes are not garbage collected
* Functions with closures have a special `__closure__` attribute
```python
>>> p = function(5,7)
A function
>>> p
<function function.<locals>.local_function at 0x02890000>
>>> p.__closure__
```
* Local functions and closures are the keys to implementing function factories
### Function Factories
* Function factories are functions that create other functions
### Function Decorators
* Function decorators are used to modify the behavior of existing functions without having to change them directly
* Decorators are callable objects, which accept a single callable object as an argument and return a new callable object
```python
def log_to(stream):

    def logging_decorator(func):

       @wraps(func)
       def logging_wrapper(*args, **kwargs):
	   print(func.__name__ + "was called")
	   return func(*args, **kwargs)

       return logging_wrapper

    return logging_decorator

@second_decorator
@logging_decorator
def some_function(x):
    """A decorated function"""
    return x * x

@log_to(sys.stderr)
def another_function(x):
    """Another decorated function"""
    return x + x
```
* Use the @ symbol to apply decorators to functions
* Decorators can enhance maintainbility, readability, and scalability of designs
* Decorators can be any kind of callable object (e.g., functions, class objects, class instances, etc.)
* The `__name__` and `__doc__` attributes of decorated functions are actually those of the replacement function
* You can manually update the `__name__` and ``__doc__` attributes your wrapper functions
* The functools.wraps function can be used to create well-behavior wrappers in a simple and clear manner  
* Multiple decorators can be applied to a function
* Where multiple decorators are applied, they are applied in reverse order
* Decorators are a powerful tool, but make sure you don't overuse them or use them unnecessarily
* There is technically no such thing as a decorator that takes extra arguments
* To parameterize decorators you need a function that creates decorators
* Local functions can create closures over objects in any number of enclosing scopes
