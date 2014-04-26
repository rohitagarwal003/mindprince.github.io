---
layout: post
title: "Notes on Apache log4j"
date: 2014-01-04 12:20
comments: true
tags: common-tasks logging
description: "This post contains some example log4j.properties files which I use most frequently."
---

Setup a working directory. All the following commands are run from this directory. All relative file locations are relative to this directory.

```
$ mkdir log-test
$ cd log-test
```

Create a test java program `com/mindprince/MyLogTest.java`.

```java
package com.mindprince;

import org.apache.log4j.Logger;

public class MyLogTest {
  static Logger LOG = Logger.getLogger(MyLogTest.class);

  public static void main(String[] args) {
    LOG.debug("Enter main method.");
    FooBarBaz.foo();
    FooBarBaz.bar();
    FooBarBaz.baz();
    LOG.debug("Exit main method.");
  }
}

class FooBarBaz {
  static Logger LOG = Logger.getLogger(FooBarBaz.class);

  public static void foo() {
    LOG.debug("Enter foo method.");
    LOG.info("This is information from foo.");
    LOG.debug("Exit foo method.");
  }

  public static void bar() {
    LOG.debug("Enter bar method.");
    LOG.warn("This is a warning from bar.");
    LOG.debug("Exit bar method.");
  }

  public static void baz() {
    LOG.debug("Enter baz method.");
    LOG.error("This error occurred in baz.");
    LOG.debug("Exit baz method.");
  }
}
```

Compile the program.

```
$ export CLASSPATH=$CLASSPATH:.:/path/to/log4j-1.2.17.jar
$ javac com/mindprince/MyLogTest.java
```

Create a `log4j.properties` file in the current working directory (`log-test/`). Log4j will automatically pick this up as this directory is in the classpath.

```
# Root logger option
log4j.rootLogger=INFO, stdout

# Direct log messages to stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c %x - %m%n
```

```
$ java com.mindprince.MyLogTest
2013-12-12 14:41:52 [main] INFO  com.mindprince.FooBarBaz  - This is information from foo.
2013-12-12 14:41:52 [main] WARN  com.mindprince.FooBarBaz  - This is a warning from bar.
2013-12-12 14:41:52 [main] ERROR com.mindprince.FooBarBaz  - This error occurred in baz.
```

Sometimes it is useful to see log4j's internal debug information.

```
$ java -Dlog4j.debug=true com.mindprince.MyLogTest
log4j: Trying to find [log4j.xml] using context classloader sun.misc.Launcher$AppClassLoader@1372a1a.
log4j: Trying to find [log4j.xml] using sun.misc.Launcher$AppClassLoader@1372a1a class loader.
log4j: Trying to find [log4j.xml] using ClassLoader.getSystemResource().
log4j: Trying to find [log4j.properties] using context classloader sun.misc.Launcher$AppClassLoader@1372a1a.
log4j: Using URL [file:/home/rohita/log-test/log4j.properties] for automatic log4j configuration.
log4j: Reading configuration from URL file:/home/rohita/log-test/log4j.properties
log4j: Parsing for [root] with value=[INFO, stdout].
log4j: Level token is [INFO].
log4j: Category root set to INFO
log4j: Parsing appender named "stdout".
log4j: Parsing layout options for "stdout".
log4j: Setting property [conversionPattern] to [%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c %x - %m%n].
log4j: End of parsing for "stdout".
log4j: Parsed "stdout" options.
log4j: Finished configuring.
2013-12-12 14:42:50 [main] INFO  com.mindprince.FooBarBaz  - This is information from foo.
2013-12-12 14:42:50 [main] WARN  com.mindprince.FooBarBaz  - This is a warning from bar.
2013-12-12 14:42:50 [main] ERROR com.mindprince.FooBarBaz  - This error occurred in baz.
```

To log messages to a file instead of the console use the following properties:

```
# Root logger option
log4j.rootLogger=DEBUG, file

# Direct log messages to a log file
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=/path/to/logging.log
log4j.appender.file.MaxFileSize=1MB
# Keep one backup file
log4j.appender.file.MaxBackupIndex=1
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c %x - %m%n
```

You can manually specify the location of your log4j properties file using `-Dlog4j.configuration`

```
$ java -Dlog4j.configuration=file:/path/to/log4j-tofile.properties com.mindprince.MyLogTest

$ cat /path/to/logging.log
2013-12-12 14:44:07 [main] DEBUG com.mindprince.MyLogTest  - Enter main method.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.FooBarBaz  - Enter foo method.
2013-12-12 14:44:07 [main] INFO  com.mindprince.FooBarBaz  - This is information from foo.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.FooBarBaz  - Exit foo method.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.FooBarBaz  - Enter bar method.
2013-12-12 14:44:07 [main] WARN  com.mindprince.FooBarBaz  - This is a warning from bar.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.FooBarBaz  - Exit bar method.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.FooBarBaz  - Enter baz method.
2013-12-12 14:44:07 [main] ERROR com.mindprince.FooBarBaz  - This error occurred in baz.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.FooBarBaz  - Exit baz method.
2013-12-12 14:44:07 [main] DEBUG com.mindprince.MyLogTest  - Exit main method.
```

Sometimes it is useful to change the logging behaviour for particular classes:

```
# Root logger option
log4j.rootLogger=DEBUG, stdout

# Direct log messages to stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c %x - %m%n

# Print only messages of level WARN or above for FooBarBaz
log4j.logger.com.mindprince.FooBarBaz=WARN
```

```
$ java -Dlog4j.configuration=file:/path/to/log4j-selective.properties com.mindprince.MyLogTest
2013-12-12 14:51:36 [main] DEBUG com.mindprince.MyLogTest  - Enter main method.
2013-12-12 14:51:36 [main] WARN  com.mindprince.FooBarBaz  - This is a warning from bar.
2013-12-12 14:51:36 [main] ERROR com.mindprince.FooBarBaz  - This error occurred in baz.
2013-12-12 14:51:36 [main] DEBUG com.mindprince.MyLogTest  - Exit main method.
```

I usually log everything to a file but only log important messages to the console:

```
# Root logger option
log4j.rootLogger=DEBUG, stdout, file

# Direct log messages to stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c %x - %m%n
# Print only messages of level WARN or above on the console.
log4j.appender.stdout.Threshold=WARN

# Direct log messages to a log file
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=/path/to/messages.log
log4j.appender.file.MaxFileSize=1MB
log4j.appender.file.MaxBackupIndex=1
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c %x - %m%n
```

```
$ java -Dlog4j.configuration=file:/path/to/log4j-both.properties com.mindprince.MyLogTest
2013-12-12 15:10:31 [main] WARN  com.mindprince.FooBarBaz  - This is a warning from bar.
2013-12-12 15:10:31 [main] ERROR com.mindprince.FooBarBaz  - This error occurred in baz.

$ cat /path/to/messages.log
2013-12-12 15:10:31 [main] DEBUG com.mindprince.MyLogTest  - Enter main method.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.FooBarBaz  - Enter foo method.
2013-12-12 15:10:31 [main] INFO  com.mindprince.FooBarBaz  - This is information from foo.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.FooBarBaz  - Exit foo method.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.FooBarBaz  - Enter bar method.
2013-12-12 15:10:31 [main] WARN  com.mindprince.FooBarBaz  - This is a warning from bar.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.FooBarBaz  - Exit bar method.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.FooBarBaz  - Enter baz method.
2013-12-12 15:10:31 [main] ERROR com.mindprince.FooBarBaz  - This error occurred in baz.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.FooBarBaz  - Exit baz method.
2013-12-12 15:10:31 [main] DEBUG com.mindprince.MyLogTest  - Exit main method.
```
