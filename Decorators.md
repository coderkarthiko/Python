##### Decorators

<br>

Everything in Python is an object. Even functions. Here's a simple function.

```python
def f():
	return "HELLO"


f()
```

```
HELLO
```

<br>

We can pass a function as an argument into another function.

```python
def g(function):
	return function() + " WORLD!"


g(f)
```

```
HELLO WORLD!
```

<br>

Some more examples! `lambda` functions can be arguments too.

```python
a = lambda : "Hello"
b = lambda : " my"
c = lambda : " name"
d = lambda : " is"
e = lambda : " Karthik!"


def f(*args):
	sm = ""
	for arg in args:
		sm += arg()
	return sm
	
	
print(f(a, b, c, d, e))
```

```
Hello, my name is Karthik!
```

<br>

Since functions are objects, we can return them.

```python
def outer(a):

	def blerg(x):
		return a * x ** 2

	return blerg
	
	
parab = outer(7)
print(parab(2))
```

```
28
```

<br>

Make a polynomial function generator.

```python
def polynomial(*coeffs):
    
    def wrapper(x):
        res = coeffs[0]
        for i in range(1, len(coeffs)):
            res = res * x + coeffs[i]
        return res
                 
    return wrapper
	
	
pol4 = polynomial(3, 4, 2, 6)
print(pol4(2))
```

```
50
```

<br>

Enter decorators! A decorator is a function that takes another function and extends the behavior of the latter function without explicitly modifying it.

```python
def A(*args):
    print(sum(args))
    

def B(f): 

    def wrapper(*args):
        print("before " + f.__name__)
        f(*args)
        print("after " + f.__name__)
    
    return wrapper
    
    
A = B(A)
A(1, 2, 3, 4, 5)
```

```
before A
15
after A
```

<br>

Alternate implementation.

```python
def B(f):

	def wrapper(*args):
		print("before " + f.__name__)
		f(*args)
		print("after " + f.__name__)
		
	return wrapper
	
	
@B
def A(*args):
	print(sum(args))
	
	
A(1, 2, 3, 4, 5)
```

```
before A
15
after A
```

<br>

The `functools.wraps` makes sure the original function (that was passed into the decorator) retains it's name, etc.

```python
def decor(f):
    
    @functools.wraps(f)
    def wrap(*args, **kwargs):
        start = time.time()
        res = f(*args, **kwargs)
        end = time.time()
        print(f"function '{f.__name__}' executed in {end - start} seconds")
		return res
    
    return wrap


@decor
def test(n):
    sum([i**2 for i in range(n)])
    return None
    
    
test(100000)
```

```
function 'test' executed in 0.10294055938720703 seconds
```

<br>

Count the number of times a function has been executed using decorators.

```python
def decor(f):

    @functools.wraps(f)
    def wrap(*args, **kwargs):
        wrap.calls += 1
        res = f(*args, **kwargs)
        print(f"function '{f.__name__}' has been called {wrap.calls} times")
        return res
    wrap.calls = 0
    
    return wrap
    
    
@decor
def dummy():
    pass
    
    
for i in range(10):
    dummy()
```

```
function 'dummy' has been called 1 times
function 'dummy' has been called 2 times
function 'dummy' has been called 3 times
function 'dummy' has been called 4 times
function 'dummy' has been called 5 times
function 'dummy' has been called 6 times
function 'dummy' has been called 7 times
function 'dummy' has been called 8 times
function 'dummy' has been called 9 times
function 'dummy' has been called 10 times
```

<br>

Alternate implementation.

```python
class decor:

    def __init__(self, f):
        self.f = f
        self.calls = 0
        
    def __call__(self, *args, **kwargs):
        self.calls += 1
        return self.f(*args, **kwargs)
    
    def reset_call_count(self):
        self.calls = 0
        

@decor
def f():
    pass


f()
f()
f()
f()
f()
f()

print(f.calls)
f.reset_call_count()
print(f.calls)
```

```
6
0
```

<br>

Debugging functions.

```python
def decor(f):

    @functools.wraps(f)
    def wrap(*args, **kwargs):
        argskwargs = [str(arg) for arg in args] + [f"{k}={v}" for k, v in kwargs.items()]
        print(f"function {f.__name__}({', '.join(argskwargs)}) executed")
        res = f(*args, **kwargs)
        return res
        
    return wrap
    

@decor
def f(*args):
    return sum(args)
    
    
print(f(1, 2, 3, 4, 5))
```

```
function f(1, 2, 3, 4, 5) executed
```
