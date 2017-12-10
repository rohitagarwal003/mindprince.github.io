---
layout: post
title: "Command-line arguments in Python, Java and C"
date: 2013-06-30 17:02
comments: true
description: "How to handle command-line arguments in python, java and c." 
tags: [common-tasks, python, java, c]
---

Handling command-line arguments is a very [common task](/categories/common-tasks/). Here are snippets of code I use to handle command line arguments in Python, Java and C.

### Command-line arguments in Python
```python
import sys
# sys.argv is a list of strings.
# argv[0] is the script name
for arg in sys.argv:
    # do something with the argument
    print arg
```

```console
$ python cmd_line_args.py Position1 Position2 Position3
cmd_line_args.py
Position1
Position2
Position3
$ python cmd_line_args.py "Position1 Position2 Position3"
cmd_line_args.py
Position1 Position2 Position3
```

```python
import sys
# sys.argv is a list of strings.
# argv[0] is the script name
for arg in sys.argv[1:]:
    # if you are sure that the arguments are integers
    arg_int = int(arg)
    # do something with the argument
    print arg_int
```

```console
$ python cmd_line_args.py 2013 6 30
2013
6
30
$ python cmd_line_args.py Position1 Position2 Position3
Traceback (most recent call last):
  File "cmd_line_args.py", line 6, in <module>
    arg_int = int(arg)
ValueError: invalid literal for int() with base 10: 'Position1'
```

Reference: [Python Tutorial](http://docs.python.org/2/tutorial/interpreter.html#argument-passing).

For more advanced needs, you can try the [argparse module](http://docs.python.org/2/howto/argparse.html).

### Command-line arguments in Java
```java
public class CommandLineArguments {
    public static void main(String[] args) {
        // args is an array of Strings
        // You can access various positional arguments using
        // args[0], args[1], etc. Or you can iterate over them.
        for (String argument : args) {
            // do something with the argument
            System.out.println(argument);
        }
    }
}
```

```console
$ javac CommandLineArguments.java 
$ java CommandLineArguments Position1 Position2 Position3
Position1
Position2
Position3
$ java CommandLineArguments "Position1 Position2 Position3"
Position1 Position2 Position3
```

```java
public class CommandLineArgumentsInt {
    public static void main(String[] args) {
        // args is an array of Strings
        // You can access various positional arguments using
        // args[0], args[1], etc. Or you can iterate over them.
        for (String argument : args) {
            // If you are sure that the arguments are integers
            int arg = Integer.parseInt(argument);
            // do something with the argument
            System.out.println(arg);
        }
    }
}
```

```console
$ javac CommandLineArgumentsInt.java 
$ java CommandLineArgumentsInt 2013 6 30
2013
6
30
$ java CommandLineArgumentsInt Position1 Position2 Position3
Exception in thread "main" java.lang.NumberFormatException: For input string: "Position1"
	at java.lang.NumberFormatException.forInputString(NumberFormatException.java:48)
	at java.lang.Integer.parseInt(Integer.java:449)
	at java.lang.Integer.parseInt(Integer.java:499)
	at CommandLineArgumentsInt.main(CommandLineArgumentsInt.java:8)
```

Reference: [The Java Tutorial](http://docs.oracle.com/javase/tutorial/essential/environment/cmdLineArgs.html).

For more advanced needs, see [this Stack Overflow question](http://stackoverflow.com/questions/367706/is-there-a-good-command-line-argument-parser-for-java).

### Command-line arguments in C
```c
#include <stdio.h>

int main(int argc, char* argv[]) {
    // argc refers to the number of arguments passed.
    // argv is an array of char pointers which contain the passed arguments.
    // argv[0] is the path and name of the program itself
    for (int i = 0; i < argc; i++) {
        // do something whith the argument
        printf("%s\n", argv[i]);
    }
}
```

```console
$ cc cmd_line_args.c 
$ ./a.out Position1 Position2 Position3
./a.out
Position1
Position2
Position3
$ ./a.out "Position1 Position2 Position3"
./a.out
Position1 Position2 Position3
```

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[]) {
    // argc refers to the number of arguments passed.
    // argv is an array of char pointers which contain the passed arguments.
    // argv[0] is the path and name of the program itself
    for (int i = 1; i < argc; i++) {
        // If you are sure that the arguments are integers
        int argument = atoi(argv[i]);

        // do something whith the argument
        printf("%d\n", argument);
    }
}
```

```console
$ cc cmd_line_args.c 
$ ./a.out 2013 6 30
2013
6
30
$ ./a.out Position1 Position2 Position3
0
0
0
```

For more advanced needs, use [getopt](http://www.gnu.org/software/libc/manual/html_node/Getopt.html).
