# Summary
I think I should do programming like play game.  the more I do, the more skills I get.

# Function abstraction
Functions are, in effect, abstractions that describe conpound operations independent of particular values of their arguments.

In general, lacking function definition would put us at the disvantage of forcing us to work always at the level of particular operations that happen to be primitives in the language rather than in terms of high-level operations.  Our programs would be able to compute squares, but our language would lack the ability to express the concept of squaring.

take as example:

    def square(x):
        return x * x

Here we have abstract the describe compound operations independent on particular values of their arguments.  We are not talking about the square of a particular number, but rather about a method of any number.  

It's important that function can be argument of other functions, and function can be return value.

## Exercise

### Exercise 1
Consider these codes:

    def sum_naturals(n):
        """
        computes the sum of natural numbers up to n
        """
        total, k = 0, 1
        while k <= n:
            total, k = total + k, k + 1
        return total
    
    def sum_cubes(n):
        total, k = 0, 1
        while k <= n:
            total, k = total + pow(k, 3), k + 1
        return total

    def pi_sum(n):
        total, k = 0, 1
        while k <= n:
            total, k = total + 8 / (k * (k + 2)), k + 4
        return total

How to abstract this?

# Data abstraction
The basic idea of *data abstraction* is to structure programs so that they operate on *abstract data*.  That is, our client code should use data in such a way as to make as few assumptions about the data as possible.  At the same time, a concrete data representation is defined, independently of the programs that use the data.

When using an ADT, we focus on the operations specified in the API and pay no attention to the data representation;  When implementing an ADT, we focus on the data, then implement operations on that data.

## what exactly is meant by data
It is not enough to say "whatever is implemented by given selectors and constructors."  We need to guarantee that these functions together specify right behavior.

In general, we can think of an abstract data type as defined by some collections of constructors and selectors, together with some behavior conditions.  Actually, data can be function, if we use the relative constructors and selectors can match behavior.

So, we descript as *pair* is that:

If a pair *p* was constructed from value *x* and *y*, then getitem_pair(p, 0) return x, getitem_pair(p, 0) return y.  This is data.  It's defined by some collections of constructors and selectors, and some behavior.  If these functions together specify right behavior, the implementation is right.

typically, a pair can be implented like this:

    def make_pair(x, y):
        return (x, y)
    
    def get_item_pair(p, i):
        return p[i]

but, if getitem_pair can return something right, so we can think this implementation is also legal, take another implementation of pair as example:

    def make_pair(x, y):
        def dispatch(m):
            if m == 0:
                return x
            elif m == 1:
                return y
        return dispatch

    def getitem_pair(p, i):
        return p(i)

## mutable object
how to implement something like this:

    >>> withdraw(25)
    75
    >>> withdraw(25)
    50
    >>> withdraw(60)
    'Insufficient funds'

for the upper example, consider it as the initial balance is 100.  How to implement it only with functions?  No using class.

Note that because we have state in object, so the object state is assigned by reference.  Because the object is expressed by function, and assign object is just assign function without function, it's not creating a new frame.  So they are assign by reference..

The key to correctly analyzing code with non-local assignment is to remember that only function calls can introduce new frames.  Assignment statements always change binding in existing frames.

### List
List in python is a mutable object

#### implementation list as a function

    def make_mutable_rlist():
        contents = empty_rlist
        def dispatch(message, value=None):
            nonlocal contents
            if message == 'len':
                return len_rlist(contents)
            elif message == 'getitem':
                return getitem_rlist(contents, value)
            elif message == 'push_first':
                contents = make_rlist(value, contents)
            elif message == 'pop_first':
                f = first(contents)
                contents = rest(contents)
                return f
            elif message == 'str':
                return str(contents)
        return dispatch
    
    def to_mutable_rlist(source):
        s = make_mutable_rlist()
        for element in reversed(source):
            s('push_first', element)
        return s

### Dict

#### implement dict as a function

    def make_dict():
        records = []
        def getitem(key):
            for k, v in records:
                if k == key:
                    return v
        def setitem(key, value):
            for item in records:
                if item[0] == key:
                    item[1] = value
                    return
            records.append([key, value])
        def dispatch(message, key=None, value=None):
            if 'getitem' == message:
                return getitem(key)
            elif message == 'setitem':
                setitem(key, value)
            elif message == 'keys':
                return tuple(k for k, _ in records)
            elif message == 'values':
                return tuple(v for _, v in records)
        return dispatch

The central idea in message passing was that data values should have behavior by responding to messages that are relevant to the abstract type they represent.  Methods and instance attributes, these two concepts replicate much of the behavior of a dispatch dictionary in a message passing implementation of data value.(implement data as a function)

## The role of objects
The python object system is designed to make data abstraction and message passing both convenient and flexible.

In particular, we would like our object system to promote a *separation of concerns* among the different aspects of the program.  Each object in a program encapsulates and manages some part of the program's state, and each class statement defines the functions that implement some part of the program's overall logic.  Abstraction barriers enforce the boundaries between different aspects of a large program.

Object-oriented programming is particularly well-suited to programs that model systems that have separate but interacting parts.

On the other hand, classes may not provide the best mechanism for implementing certain abstractions.  Functional abstractions provide a more natural metaphor for representing relation between inputs and outputs.  One should not feel compelled to fit every bit of logic in a program within a class, especially when defining independent functions for manipulating data is more natural.

## instance and class
instance and class are basic OO programming in many programming language, in general, classes and objects can themselves be represented using just functions and dictionaries.  

In order to implement objects, we will abandon dot notation.  We will focus instead on user-defined classes without multiple inheritance and withour introspective behavior.

### instance implement

    def make_instance(cls):
        def get_value(name):
            if name in attributes:
                return attributes[name]
            else:
                value = cls['get'](name)
                return bind_method(value, instance)
        def set_value(name, value):
            attributes[name] = value
        attributes = {}
        instance = {'get': get_value, 'set': set_value}
        return instance

# Stage of Design Abstraction data types
## Encapsulation
## Designing APIS (The key point to do exercise in projects)
One of the most important and most challenging steps in building modern software is designing APIs.  There are numerous potential pitfalls when designing an API:
1. Too hard to implement, making it difficult or impossible to develop
2. Too hard to use, leading to complicated client code.
3. Too narrow, omitting methods that clients need
4. Too wide, including a large number of methods not needed by any client.
5. Too general, providing no useful abstractions.
6. Too dependent on a particular representation, therefore not freeing client code from the details of the representation.

In summary, provide to clients the methods they need and no others.

A good api should:
1. Easy to learn
2. Usable, even without documentation
3. Hard to misuse
4. Powerful and easy to extend
5. Consistent

### The rules of become a API designer
1. Start building applications with the API
2. Think in terms of APIs
3. Even if you will always be the only programmer on that thing

## ....others to omit..

# Some suggestion get from other article
## Class Design
Design for Subclassing
* Build your class so that a subclass might improve/change certain behavior
* Provide ways to hook into specific parts of the execution
* If class is not designed for subclassing, document it as such

## Defaults/Common Use Cases
* Think of the most common use cases, you will have them if you use your API
* Make sure the API provides easy ways to do that
* If you see that your code does things the API should be doing instead, move that specific code over.

## Consistent Parameters
* Ordering of parameters is important
* What you're operating on should always be the first parameter
* Similar methods should have same ordering of parameters and types.
* If the order is the wrong way round, stick with it!  Consistency more important.

## API about Data structures
* If users have to parse return values of APIs, you(API designer) are doing something wrong.
* If an implementation detail becomes an interface it prevents future improvements.

## Implementations vs interface
* Interface must be independent of implementation
* Don't let implementation details lead into the API.  (exceptions, error codes, etc)

Note that a *leaky abstraction* is an *abstraction* that exposes details and limitations of its underlying implementation to its users that should ideally be hidden away.

# Notes
The key to success in modular programming is to maintain independent among modules.  We do so by insisting on the API being the only point of dependence between client and implementation.