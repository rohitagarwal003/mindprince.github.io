---
layout: post
title: "File handling in Python, Java and C"
date: 2013-06-29 17:30
comments: true
categories: [common-tasks, java, python, c]
description: "How to perform common file handling tasks in python, java and c."
---
File handling is a very [common task](/categories/common-tasks/). Here are snippets of code I use to perform various file handling operations in Python, Java and C.
## Reading a file in Python

### Reading a file at once
{% codeblock lang:python %}
with open("/path/to/file/filename.txt", "r") as f:
    text = f.read()
    # text is a string which contains all the text in the file.
    # this may cause problems with very big files.

    # do something with text
    print text
{% endcodeblock %}

### Reading a file line by line
{% codeblock lang:python %}
with open("/path/to/file/filename.txt", "r") as f:
    for line in f:
        # line is a string
        # a newline character (\n) is present at the end of the string
        # and is only omitted on the last line of the file if the
        # file doesn't end in a newline.

        # do something with line
        print line
{% endcodeblock %}

### Reading a file word by word
{% codeblock lang:python %}
with open("/path/to/file/filename.txt", "r") as f:
    for line in f:
        for word in line.split():
            # do something with word
            print word
{% endcodeblock %}

### Reading a file character by character
{% codeblock lang:python %}
with open("/path/to/file/filename.txt", "r") as f:
    while True:
        character = f.read(1)
        if character:
            # do something with character
            print character
        else:
            # end-of-file reached
            break
{% endcodeblock %}

## Writing a file in Python
{% codeblock lang:python %}
with open("/path/to/file/filename.txt", "w") as f:
    # write to the file using f.write()
    f.write('Whatever you want to write.\n')
    f.write('Really, anything.\n')
{% endcodeblock %}

<!--more-->

***

## Reading a file in Java

### Reading a file at once
There is no obvious way to do this in Java. [This question](http://stackoverflow.com/questions/3402735/what-is-simplest-way-to-read-a-file-into-string-in-java) provides various hacks.

### Reading a file line by line
{% codeblock lang:java %}
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class FileLL {
    public static void main(String[] args) throws FileNotFoundException {
        Scanner s = new Scanner(new BufferedReader(new FileReader("/path/to/file/filename.txt")));
        while (s.hasNext()) {
            String line = s.nextLine();
            // line does not contain a newline (\n) at the end.

            // do something with line
            System.out.print(line);
        }
        s.close();
    }
}
{% endcodeblock %}

### Reading a file word by word
{% codeblock lang:java %}
import java.io.FileReader;
import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class FileWW {
    public static void main(String[] args) throws FileNotFoundException {
        Scanner s = new Scanner(new BufferedReader(new FileReader("/path/to/file/filename.txt")));
        while (s.hasNext()) {
            String word = s.next();

            // do something with the word
            System.out.println(word);
        }
        s.close();
    }
}
{% endcodeblock %}

### Reading a file character by character
{% codeblock lang:java %}
import java.io.FileReader;
import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.IOException;

public class FileCC {
    public static void main(String[] args) throws FileNotFoundException, IOException {
        BufferedReader br = new BufferedReader(new FileReader("/path/to/file/filename.txt"));
        int ch;
        while ((ch = br.read()) != -1) {
            char character = (char) ch;

            // do something with the character.
            System.out.print(character);
        }
    }
}
{% endcodeblock %}

## Writing a file in Java
{% codeblock lang:java %}
import java.io.FileWriter;
import java.io.BufferedWriter;
import java.io.IOException;

public class FileW {
    public static void main(String[] args) throws IOException {
        BufferedWriter bw = new BufferedWriter(new FileWriter("/path/to/file/filename.txt"));
        bw.write("Whatever you want to write.\n");
        bw.write("Really, anything.\n");
        bw.close();
    }
}
{% endcodeblock %}
***

## Reading a file in C

### Reading a file at once
I don't know of a straightforward way to do this. Feel free to chime in the comments if you know one.

### Reading a file line by line
{% codeblock lang:c %}
#include <stdlib.h>
#include <stdio.h>

int main() {

    FILE *fp = fopen("/path/to/file/filename.txt", "r");
    if (fp == NULL) {
        fprintf(stderr, "Can't open input file!\n");
        exit(1);
    }

    char line[1000];
    // assuming no line in the file is longer than 1000 characters.
    while (fgets(line, sizeof line, fp) != NULL) {
        // line contains a newline (\n) at the end
        // except maybe for the last line of the file
        // if the file doesn't end with a newline

        // do something with line
        printf("%s\n", line);
    }

    fclose(fp);

    return 0;
}
{% endcodeblock %}

### Reading a file word by word
{% codeblock lang:c %}
#include <stdlib.h>
#include <stdio.h>

int main() {

    FILE *fp = fopen("/path/to/file/filename.txt", "r");
    if (fp == NULL) {
        fprintf(stderr, "Can't open input file!\n");
        exit(1);
    }

    char word[50];
    // assuming no word in the file is longer than 50 characters.
    while (fscanf(fp, "%s", word) != EOF) {
        // do something with word
        printf("%s\n", word);
    }

    fclose(fp);

    return 0;
}
{% endcodeblock %}

### Reading a file character by character
{% codeblock lang:c %}
#include <stdlib.h>
#include <stdio.h>

int main() {

    FILE *fp = fopen("/path/to/file/filename.txt", "r");
    if (fp == NULL) {
        fprintf(stderr, "Can't open input file!\n");
        exit(1);
    }

    int ch;
    while ((ch = fgetc(fp)) != EOF) {
        char character = (char) ch;
        // do something with character
        printf("%c", character);
    }

    fclose(fp);

    return 0;
}
{% endcodeblock %}

## Writing a file in C
{% codeblock lang:c %}
#include <stdlib.h>
#include <stdio.h>

int main() {

    FILE *fp = fopen("/path/to/file/filename.txt", "w");
    if (fp == NULL) {
        fprintf(stderr, "Can't open input file!\n");
        exit(1);
    }
    fprintf(fp, "Whatever you want to write.\n");
    fprintf(fp, "Really, anything.\n");
    fclose(fp);

    return 0;
}
{% endcodeblock %}
