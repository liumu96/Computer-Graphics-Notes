# Advance Tutorials

- Reference: [youtube](https://www.youtube.com/watch?v=lox29cXvwnk&list=PL7yh-TELLS1FuqLSjl5bgiQIEH25VEmIc&index=3&ab_channel=NeuralNine)

## Decorators

**Basics of Generators**

1.  Decorator Basics

    At its core, a decorator is a design pattern in Python that allows the modification of functions or methods using a concise syntax. Decorators are functions themselves, and they are applied using the `@decorator_name` syntax.

    ```python

    @my_decorator
    def my_function(): # Function logic here
    ```

2.  Functions as First-Class Objects

    To understand decorators, it's essential to grasp the concept of functions as first-class objects in Python. This means functions can be passed as arguments to other functions, returned as values from other functions, and assigned to variables.

    ```python

    def greet(name):
        return f"Hello, {name}!"
    greeting_function = greet
    print(greeting_function("Alice"))

    ```

**Practical Use Cases**

1. Logging and Timing

   Decorators are excellent for augmenting functions with additional behavior, such as logging or timing.

   ```python
   import time

   def timing_decorator(func):
       def wrapper(*args, **kwargs):
           start_time = time.time()
           result = func(*args, **kwargs)
           end_time = time.time()
           print(f"{func.__name__} took {end_time - start_time} seconds.")
           return result
       return wrapper

   @timing_decorator
   def slow_function():
       # Some time-consuming operation
       time.sleep(2)

   slow_function()
   ```

2. Authorization and Authentication

   Decorators can be used to enforce authorization and authentication checks before allowing access to certain functions or methods.

   ```python
   def require_admin(func):
       def wrapper(user, *args, **kwargs):
       if user.is_admin:
           return func(user, *args, **kwargs)
       else:
           raise PermissionError("Admin privileges required.")
   return wrapper

   @require_admin
   def delete_user(user, user_id):
       # Delete user logic
       print(f"User {user_id} deleted.")
   ```

   **Advanced Concepts**

3. Class-Based Decorators

   In addition to function-based decorators, Python supports class-based decorators. This opens up new possibilities for encapsulating state and behavior.

   ```python
   class LogCalls:
       def __init__(self, func):
           self.func = func

       def __call__(self, *args, **kwargs):
           print(f"Calling {self.func.__name__}")
           return self.func(*args, **kwargs)

   @LogCalls
   def example_function():
       # Function logic here
       pass
   ```

4. Chaining Decorators

   Multiple decorators can be applied to a single function, creating a chain of transformations.

   ```python
   @decorator1
   @decorator2
   @decorator3
   def my_function():
       # Function logic here
   ```

## Generators

Generators provide a memory-efficient and elegant solution for working with sequences of data.

**Understanding Generators**

1. Basics of Generators

Generators are special functions in Python that use the `yield` keyword to produce a sequence of values. Unlike regular functions that return once and are done, generators can pause and resume their execution, maintaining state between calls.

```python
def simple_generator():
    yield 1
    yield 2
    yield 3

gen = simple_generator()
for value in gen:
    print(value)

```

2. Lazy Evaluation

One of the key advantages of generators is lazy evaluation. Values are generated on-the-fly, consuming memory only when requested. This is particularly beneficial when dealing with large datasets or infinite sequences.

```python
def fibonacci_generator():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

fib_gen = fibonacci_generator()
for _ in range(10):
    print(next(fib_gen))
```

**Practical Use Cases**

1. Memory-Efficient Iteration

Generators are an excellent choice when working with large datasets. Instead of loading the entire dataset into memory, you can process it one piece at a time.

```python
def process_large_file(file_path):
    with open(file_path, 'r') as file:
        for line in file:
            # Process each line without loading the entire file
            yield process_line(line)
```

2. Infinite Sequences
   Generators can represent infinite sequences without consuming infinite memory. This is useful in scenarios where you need to generate values on-the-fly.

```python
def infinite_counter(start=0):
    while True:
        yield start
        start += 1
```

**Advanced Concepts**

1. Generator Expressions

Python also supports concise generator expressions, providing a more compact syntax for creating generators.

```python
squares = (x**2 for x in range(10))
```

2. Coroutines and Async Generators

Generators can be used as coroutines for asynchronous programming. This allows you to write asynchronous code using a synchronous style.

```python
async def async_data_fetcher():
    while True:
        data = await fetch_data_async()
        yield data
```

## Argument Parsing

## Encapsulation

## Type Hinting

## Factory Design Pattern

## Proxy Design Pattern

## Singleton Design Pattern

## Composite Design Pattern
