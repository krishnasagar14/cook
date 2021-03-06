# [Cookpy — A Modern Build System](https://getcook.org/)

⚠**️ This software is currently alpha and not ready for production use. 
Not everything mentioned below will work.**


## Overview

Cookpy is an extensible, dynamic, parallel and cross-platform
build system. It is based on the concept that writing build definitions should
be as powerful and easy as possible, which is why everything in Python. While 
many other systems may be slightly faster in benchmarks, we believe that, at 
least for most projects, these differences do not outweigh the advantages you 
get by using a more powerful system.


## Example

Using the built-in rules is straightforward. You just import the corresponding 
module and call the supplied rule. This is all you need to get a working build 
going. The following example will automatically work on any supported platform.

```python
from cookpy import cpp

cpp.executable(
    sources=['main.cpp'],
    destination='main'
)
```

Executing this script will build an executable using the correct 
platform-specific details. For example, the output is either named `main` or 
`main.exe` depending on the operating system.

```
$ ls
BUILD.py  main.cpp  main.h
$ cook
[INFO] Evaluating build script...
[  0%] Compile ".cookpy/intermediate/0271de2df0c16df23a49aa4b52371a04.o"                            
[ 50%] Link "main"                                                             
[100%] Everything should be up-to-date. 
$ ls build/
main
```

You can also easily create your own rules. Upon calling, they are executed 
until the required information is passed back to the system using 
`yield core.publish(...)`. Everything after that is executed shortly after if 
the system decides it is necessary to do so.

```python
from cookpy import core

@core.rule
def replace(source, destination, mapping):
    source = core.resolve(source)

    yield core.publish(
        inputs=[source],
        outputs=[destination],
        check=mapping
    )
    
    with open(source) as file:
        content = file.read()
    for key, value in mapping.items():
        content.replace(key, value)
    with open(destination, 'w') as file:
        file.write(content)
```

Please look at the documentation if you want to know more.
