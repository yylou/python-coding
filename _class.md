# Python Coding

* ### [Menu](./README.md)
* ### Class
    * [Classes, Instances, and Attributes](#p1)
    * [Pre-Defined Attributes for a Class](#p2)
    * [Pre-Defined Attributes for an Instance](#p3)
    * [How Python Creates an Instance from a Class](#p4)
    * [Destruction of Instance Objects](#p5)
    * [Encapsulation, Inheritance, and Polymorphism](#p6)
    * [Advantages of Inheritance](#p7)
    * [Method Overriding, Operator Overloading](#p8)
    * [Implementing duck typing](#p9)
    * [Abstract Base Classes (ABC)](#p10)
    * [Iterable vs. Iterator](#p11)

<br />

## Classes, Instances, and Attributes               <a name="p1"></a>
For the purpose of writing code, **a class is a data structure with attributes (attributes are also referred to as properties or members).**  
To endow instances with behaviors, **a class can be provided with methods.**  
(Methods act as an interface between a program and the properties of a class in the program)
* A method that is invoked on an instance is sometimes called an **instance method**.
* You can also invoke a method directly on a class, in which case it is called a **class method or a static method**.
* Attributes that take data values on a **per-instance** basis are frequently referred to as **instance variables**.  
  (The instance variables are unique to each instance or object of the class.)
* Attributes that take on values on a **per-class** basis are called **class attributes or static attributes or class variables**.  
  (The class variables are shared by all instances or objects of the class.)

## Pre-Defined Attributes for a Class               <a name="p2"></a>
* ```__name__```    : string name of the class
* ```__doc__```     : documentation string for the class
* ```__bases__```   : tuple of parent classes of the class
* ```__dict__```    : dictionary whose keys are the names of the class variables and the methods of the class and whose values are the corresponding bindings
* ```__module__```  : module in which the class is defined

## Pre-Defined Attributes for an Instance           <a name="p3"></a>
* ```__class__```   : string name of the class from which the instance was constructed
* ```__dict__```    : dictionary whose keys are the names of the instance variables

```Python
# As an alternative to invoking dict on a class name
"""
Returns a list of all the attribute names, for variables and for methods, for the class
(both directly defined for the class and inherited from a class???s superclasses).
"""
dir(MyClass)        # type: list
MyClass.__dict__    # type: mappingproxy
```

```Python
class Person(object):
    "A very simple class"
    class_var = None        # class variable

    @classmethod
    def demo1(cls): print(f'Class method prints class variable: {cls.class_var}')

    @staticmethod
    def demo2(): print(f'Static method does not use a reference to the object or class')

    def __init__(self, name, yy):
        self.name   = name      # instance variable
        self.age    = yy        # instance variable
        self.__pwd  = '123'     # private attribute

    def __showpwd(self):        # private method
        print(f'Password: {self.__pwd}')

# Test code
a_person = Person("Zaphod", 114)
print(a_person.name)        # Zaphod
print(a_person.age)         # 114

# Class Attributes
"""
In Python, whenever we create a class, it is, by default, a subclass of the built-in Python object class.
"""
print(Person.__name__)      # Person
print(Person.__doc__)       # A very simple class
print(Person.__module__)    # __main__
print(Person.__bases__)     # (<class 'object'>,)
print(Person.__dict__)      # {'__module__':        '__main__',
                            #  '__doc__':           'A very simple class', 
                            #  'class_var':         None, 
                            #  'demo1':             <classmethod object at 0x102511a60>,
                            #  'demo2':             <staticmethod object at 0x1025118b0>, 
                            #  '__init__':          <function Person.__init__ at 0x107efd940>,
                            #  '_Person__showpwd':  <function Person.__showpwd at 0x10261a820>,
                            #  '__dict__':          <attribute '__dict__' of 'Person' objects>,
                            #  '__weakref__':       <attribute '__weakref__' of 'Person' objects>}

print(dir(Person))          # ['__class__', '__init__', '__dict__', '__dir__', '__doc__',
                            #  '__eq__', '__ge__', '__gt__', '__le__', '__lt__', '__ne__', 
                            #  '__format__', '__sizeof__', '__str__', '__module__', '__getattribute__', '__new__', 
                            #  '__reduce__', '__reduce_ex__', '__repr__', '__delattr__', '__setattr__',
                            #  '__hash__', '__init_subclass__', '__subclasshook__', '__weakref__',
                            #  '_Person__showpwd', 'class_variable', 'demo1', 'demo2']

# Instance Attributes
print(a_person.__class__)   # __main__.Person
print(a_person.__dict__)    # {'name': 'Zaphod', 'age': 114}
```

<br />

## How Python Creates an Instance from a Class      <a name="p4"></a>
Step 1.  
* The call to the constructor creates what may be referred to as a generic instance from the class definition.  
* **The generic instance???s memory allocation is customized with the code in the method ```__new__()``` of the class.**  
  (This method may either be defined directly for the class or the class may inherit it from one of its parent classes)
* The method ```__new__()``` is implicitly considered by Python to be a static method.
* If a class does not provide its own definition for ```__new__()```, a search is conducted for this method in the parent classes of the class.

Step 2.  
* Then the instance method ```__init__()``` of the class is invoked to initialize the instance returned by ```__new__()```.  
  **(The initializer is used to initialize an object of a class. It's used to define and assign values to instance variables.)**

```Python
class X:
    def __new__(cls):
        print("__new__  invoked @ X")
        return object.__new__(cls)
    
    def __init__(self):
        print("__init__ invoked @ X")

class Y(X):
    def __new__(cls):
        print("__new__  invoked @ Y")
        return object.__new__(cls)
    
    def __init__(self):
        print("__init__ invoked @ Y")
        super().__init__()

xobj = X()      # __new__  invoked @ X
                # __init__ invoked @ X

yobj = Y()      # __new__  invoked @ Y
                # __init__ invoked @ Y
                # __init__ invoked @ X

print(Y.__bases__)  # (<class '__main__.X'>,)

print(X.__dict__)   # {'__module__': '__main__',
                    #  '__new__': <staticmethod object at 0x1006a7be0>,
                    #  '__init__': <function X.__init__ at 0x1007899d0>,
                    #  '__dict__': <attribute '__dict__' of 'X' objects>,
                    #  '__weakref__': <attribute '__weakref__' of 'X' objects>,
                    #  '__doc__': None}
```

```Python
"""
The order in which the class and its bases are searched for the implementation code is
commonly referred to as the Method Resolution Order (MRO)
"""

class A(object):
    def __init__(self):
        print("__init__ invoked @ A")

class B(A):
    def __init__(self):
        print("__init__ invoked @ B")
        super().__init__()

class C(A):
    def __init__(self):
        print("__init__ invoked @ C")
        super().__init__()

class D(B, C):
    def __init__(self):
        print("__init__ invoked @ D")
        super().__init__()

d = D()     # __init__ invoked @ D
            # __init__ invoked @ B
            # __init__ invoked @ C
            # __init__ invoked @ A

print(D.__mro__)    # (<class '__main__.D'>,
                    #  <class '__main__.B'>,
                    #  <class '__main__.C'>,
                    #  <class '__main__.A'>,
                    #  <class 'object'>)
```

<br />

## Destruction of Instance Objects                  <a name="p5"></a>
* **Python comes with an automatic garbage collector.**
* Each object created is kept track of through reference counting.
* Each time an object is assigned to a variable, its reference count goes up by one, signifying the fact that there is one more variable holding a reference to the object.
* Each time a variable whose referent object either goes out of scope or is changed, the reference count associated with the object is decreased by one.
* **When the reference count associated with an object goes to zero, it becomes a candidate for garbage collection.**
* Python provides us with ```__del__()``` for cleaning up beyond what is done by automatic garbage collection.

## Encapsulation, Inheritance, and Polymorphism     <a name="p6"></a>
* **Hiding or controlling access** to the implementation-related attributes and the methods of a class is called encapsulation.
* **Inheritance** in object-oriented code allows a subclass to inherit some or all of the attributes and methods of its superclass(es).  
  **(The use of ```super()``` comes into play when we implement inheritance. It's used in a child class to refer to the parent class.)**
* **Polymorphism** basically means that a given category of objects can exhibit multiple identities at the same time.  
  (In programming, polymorphism refers to the same object exhibiting different forms and behaviors.)
* **Polymorphism** in a nutshell allows us to manipulate instances belonging to the different classes of a hierarchy through a common interface defined for the root class.

## Advantages of Inheritance                        <a name="p7"></a>
* **Reusability:** Inheritance makes the code reusable.
* **Code Modification:** Inheritance ensures that all changes are localized and inconsistencies are avioded.
* **Extensibility:** Using inheritance, one can extend the base class as per the requirements of the derived class.  
  (Inheritance provides an easy way to upgrade or enhance specific parts of a product without changing the core attributes.)
* **Data Hiding**: The base class can keep some data private so that the derived class cannot alter it.  
  (**This concept is called encapsulation.**)

## Method Overriding, Operator Overloading          <a name="p8"></a>
* **Method overriding** is the process of redefining a parent class's method in a subclass.  
  (In other words, if a subclass provides a specific implementation of a method that had already been defined in one of its parent classes, it is known as method overriding.)
* When a class is defined, its objects can interact with each other through the operators, but it is **necessary to define the behavior of these operators through operator overloading.**

```Python
"""
" + "   __add__(self, other)
" - "   __sub__(self, other)
" / "   __truediv__(self, other)
" * "   __mul__(self, other)
" < "   __lt__(self, other)
" > "   __gt__(self, other)
" == "  __eq__(self, other)
"""

class Compute:
    def __init__(self, real=0, imag=0):
        self.real = real
        self.imag = imag

    def __add__(self, other):  # overloading the `+` operator
        temp = Compute(self.real + other.real, self.imag + other.imag)
        return temp

    def __sub__(self, other):  # overloading the `-` operator
        temp = Compute(self.real - other.real, self.imag - other.imag)
        return temp

obj1 = Compute(3, 7)    # obj1.real = 3, obj2.imag = 7
obj2 = Compute(2, 5)    # obj1.real = 2, obj2.imag = 5
obj3 = obj1 + obj2      # obj1.real = 5, obj2.imag = 12
obj4 = obj1 - obj2      # obj1.real = 1, obj2.imag = 2
```

<br />

## Implementing duck typing                         <a name="p9"></a>
```Python
class Dog:
    def Speak(self): print("Woof woof")

class Cat:
    def Speak(self): print("Meow meow")

class AnimalSound:
    def Sound(self, animal): animal.Speak()

sound = AnimalSound()
dog = Dog()
cat = Cat()

sound.Sound(dog)    # "Woof woof"
sound.Sound(cat)    # "Meow meow"
```

<br />

## Abstract Base Classes (ABC)                      <a name="p10"></a>
Abstract base classes define a set of methods and properties that **a class must implement in order to be considered a duck-type instance of that class.**

```Python
from abc import ABC, abstractmethod

class Shape(ABC):  # Shape is a child class of ABC
    @abstractmethod
    def area(self): pass

    @abstractmethod
    def perimeter(self): pass

class Square(Shape):
    def __init__(self, length): self.length = length


shape = Shape()
# This code will not compile since Shape has abstract methods without method definitions in it
# We haven't defined the abstract methods, area and perimeter, inside the parent class Shape or the child class Square
"""
Traceback (most recent call last):
  File "main.py", line 19, in <module>
    shape = Shape()
TypeError: Can't instantiate abstract class Shape with abstract methods area, perimeter
"""
```

```Python
from abc import ABC, abstractmethod

class Shape(ABC):  # Shape is a child class of ABC
    @abstractmethod
    def area(self): pass

    @abstractmethod
    def perimeter(self): pass

class Square(Shape):
    def __init__(self, length): self.length = length
    def area(self): return (self.length * self.length)
    def perimeter(self): return (4 * self.length)

square = Square(4)
square.area()       # 16
square.perimeter()  # 16
```

<br />

## Iterable vs. Iterator                            <a name="p11"></a>
```Python
import random
random.seed(0)

class X:
    def __init__(self, arr): self.arr = arr
    def __call__(self): return self.arr
    def __iter__(self): return Xiterator(self)
    def get_num(self, i): return self.arr[i]

class Xiterator:
    def __init__(self, xobj):
        self.items = xobj.arr
        self.index = -1

    def __iter__(self): return self
    def __next__(self):
        self.index += 1
        if self.index < len(self.items): return self.items[self.index]
        else: raise StopIteration
    
    next = __next__

xobj = X(random.sample(range(1,10), 5))
print(xobj.get_num(2))      # 1
print(xobj())               # [7, 9, 1, 3, 5]

for item in xobj: print(item, end=', ')  # 7, 9, 1, 3, 5,

iterator = iter(xobj)
print(iterator.next())  # 7
print(iterator.next())  # 9
```