Properties and Class Methods
----------------------------

## Class Attributes
### Class Attributes vs. Instance Attributes
* Instance attributes are assigned on a per object basis usually in the `__init__` method of a class
* Class attributes are associated with the class not each instance of the class individually but all instances of the class
* Recall python's rule of searching scope LEGB (Local, enclosing function, global, built-in) and that classes do not introduce new scopes (they exist at the global or modual scope)
```python
class ShippingContainer:
    next_serial = 1234

    def __init__(self, owner_code, contents):
        self.owner_code = owner_code
        self.contents = contents
        self.next_serial = ShippingContainer.next_serial
        ShippingContainer.next_serial += 1
```
* As with `self`, using the class name as a prefix for class attributes confers the same understandibility advantage and reduces the amount of work required to figure out which objects are being referenced 
* At the REPL, attributes can be accessed through the class instance
```
>>> c1 = ShippingContainer("YML", "books")
>>> c1.owner_code
>>> c1.contents
>>> ShippingContainer.next_serial
```
* *Zen of Python*: Explicit is better than implicit. Readibility counts.
* Using `self` in place of the class also works, but should be avoided as it's less clear which attributes are class attributes and which are instance attributes
* Another pitfall is that although you can read a class attribute through the `self` instance reference, attempting to assign to a class attribute through `self` will not have the desired effect. It hides the class attribute and creates a new instance attribute.
* `self.attr = something` _always_ creates an instance attribute, never a _class_ attribute
```
class ShippingContainer:
    next_serial = 1234

    def __init__(self, owner_code, contents):
        self.owner_code = owner_code
        self.contents = contents
        self.serial = self.next_serial
        self.next_serial += 1
```
* Idea: It's better to access class objects as attributes of the class objects rather than via the instance.
### Static Methods
#### Static methods with the `@staticmethod` decorator
* Methods starting with `_` indicate an implementation detail of a class; rarely form a useful part of the class interface.
```
class ShippingContainer:
    next_serial = 1234

    _get_next_serial(self):
        result = ShippingContainer.next_serial
	ShippingContainer.next_serial += 1
	return result

    def __init__(self, owner_code, contents):
        self.owner_code = owner_code
        self.contents = contents
        self.serial = self._get_next_serial() 
```
* We would like to associate `_get_next_serial()` with the class instead of the instance of the class
* There are two mechanisms to achieve this: `@staticmethod` decorator or `@classmethod` decorator
```
class ShippingContainer:
    next_serial = 1234

    @staticmethod
    _get_next_serial():
        result = ShippingContainer.next_serial
	ShippingContainer.next_serial += 1
	return result

    def __init__(self, owner_code, contents):
        self.owner_code = owner_code
        self.contents = contents
        self.serial = ShippingContainer._get_next_serial() 
```
* Static methods in python have no direct knowledge of the class within which they're defined
* They allow us to group a function in the class because the function is conceptually related to the class
* The _static_ terminology is a relic or anachronism from C and C++ 

### Class Methods
#### Class Methods with the `@classmethod` decorator
* An alternative to `@staticmethod`
* `@classmethod` accepts the class object as the first formal argument by convention using the abbreviatedname `cls` since we cannot use the fully spelled keyword class as an argument name
* `cls` is analagous to to the `self` argument for instance methods
```
class ShippingContainer:
    next_serial = 1234

    @classmethod(cls)
    _get_next_serial():
        result = cls.next_serial
	cls.next_serial += 1
	return result

    @classmethod(cls)
    def create_empty(cls, owner_code):
        return cls(owner_code, contents=None)

    @classmethod(cls)
    def create_with_items(cls, owner_code, items):
        return cls(owner_code, contents=list(items))

    def __init__(self, owner_code, contents):
        self.owner_code = owner_code
        self.contents = contents
        self.serial = ShippingContainer._get_next_serial() 
```
#### Choosing between `@staticmethod` and `@classmethod`
* If you need to refer to the class object within the method, for example to access a class attribute, prefer to use `@classmethod`
* `@classmethod` requires access to the class object to call other class methods or the constructor
* If you don't need to access the class object, use `@staticmethod`
* `@staticmethod` is most likely used for an implementation detail of a class, doesn't need access to either _class_ or _instance_ objects, and may be able to be moved to become a module-scope function; faciliates particular logical organization of the code, allowing us to put within classes that would otherwise need to be free functions.
* Class Methods for named constructors (aka factory functions), which construct objects with certain configurations
* This technique enables us to support multiple functions that behave similarly to constructors but with different behaviors without having to resort to contortions within the `init` method to interpret different forms of argument lists.

#### Static Methods with Inheritence
* Static methods in python can be overwritten in subclasses, unlike in many other languages
* Polymorphic overrid behavior requires `@staticmethod` on an instance 
