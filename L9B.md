# L09 03.05.19

Note: Missed class, notes from Serena's notes

## File I/O 

**EOF** - a special value indicating end-of-file, usually #defined to be -1, not to be confused with or assumed to be the value -1

getchar( ) and other similar functions return int rather than unsigned char to enable return of EOF, the special value of type int

```c
FILE *fopen(const char *filename, char char *mode);
```
> Opens a file, and returns a FILE * , that you can pass to other file-related functions to tell them which file they should operate on. 
> 
> FILE * is an example of an **opaque handle**, which means we aren't dependent on what's in the struct
> FILE * is a pointer to a FILE, which is a struct
> The pointer is not used to dererference and access, you do not read the struct
> The sturcture is not part of the C language, it is machine dependant and not portable
> Use it as a handle to pass around to functions for reading, writing, etc.
> 

Define the structure FILE with typedef:
Example: 
```c
typedef int MyInt
typedef struct _FILE FILE
```
The **mode** argument:
> "r" open for reading (file must already exist)
> "w" open for writing (will trash existing file)
> "a" open for appending (writes will always go to the end of file)
> 
> "r+" open for reading and writing (file must already exist)
> "w+" open for reading and writing (will trash existing file)
> "a+" open for reading and appending (writes will go to the end of file)
> 

With the exception of a/a+, reading and writing being from the beginning

### ncat.c - example of using file-related functions

```c
/*
* ncat <file_name>
*
*   - reads a file line-by-line, 
*     printing them out with line numbers
*/

#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv)
{
    if (argc != 2) {
        fprintf(stderr, "%s\n", "usage: ncat <file_name>");
        exit(1);
    }

    char *filename = argv[1];
    FILE *fp = fopen(filename, "r");
    if (fp == NULL) {
        perror(filename);
        exit(1);
    }

    char buf[100];
    int lineno = 1;
    while (fgets(buf, sizeof(buf), fp) != NULL) {
        printf("%4d ", lineno++);
        if (fputs(buf, stdout) == EOF) {
            perror("can't write to stdout");
            exit(1);
        }
    }

    if (ferror(fp)) {
        perror(filename);
        exit(1);
    }

    fclose(fp);
    return 0;
}
```

ncat.c - given a file, it reads each line, prints out program with line number displayed.
> It is like cat, but adds line numbers
> 

If there are not two arguments (./ncat and whatever file is to be ncat-ed), ncat will print out proper usage, and exit. 
> This happens at line 14, which uses a function fprintf
> fprintf is like printf, but takes an additional parameter (in this case it's stderr), this is how you print to stderr
> 
It opens the file using fopen( ) in read mode.
> If openning the file fails (no such file), perror(filename) line 21 
> ex. typing "./ncat akdnaskjda"
> 

The next part of the program uses fgets. 
> fgets reads at most (size - 1) characters into buffer and continues until it reaches a newline 
> 
> buf was hardcoded to be 100, but this is not necessarily meaningful. Had it been hardcoded to 19, it would only read 19 chatacters.
> 
> It only reads one line
> 
> Writes the '/0' at the end of the buffer and then stops 
> If it writes (size-1) bytes, it would terminate with a '/0'
> 
> Writes at most (size) bytes/chars
>
> It returns NULL on EOF or error. So when it returns NULL, it is not immediately apparent if it was an error (i.e. the file was deleted while reading) or if it reached the end of the file. You can call ferror( ) as the program does at line 35 to determine if it was an error or not. 
> 

fgets( ) gets one line at a time, puts it into buf, prints out the line number, and then calls fputs( ) on buf to print out the line. 

The program prints out the line number
> "%4d" right-justifies with 4 spaces
> 

Then the program calls fputs( )
> fputs( ) takes a char * and a file, and it writes the string from char * to the file (in this case, stdout, so it will write to the screen instead of a file).
> 
> **stdout is of type FILE * and it must be a global variable**. It is created for you. The same applies to stdin (so in UNIX, keyboard and screen are treated as files)
> 
> It returns EOF on error 


When a program runs, it pre-opens 3 files: stdin, stdout, stderr
> The OS opens the files, and the results are FILE * s (pointers to FILE) that get stored as global variables in the name "stdin"/"stdout"/"stderr"
> 

So essentially, in ncat, we are reading from fp and writing each line sequentially to another file, which happens to be stdout in this case.

This process relates back to **cp command**, which keeps reading and copying over bytes from one fiel to another. It has a lot of options the command, but this is the basic idea.

**getchar( )** reads an int and returns the char if successful.

**fclose( )** closes a file and must be coled each time fopen( ) is called. 

There is a bug in this program. The problem is that the line number is printed even if we are not done. **FIGURE OUT THIS BUG AND WHAT IT MEANS**

### EOF 

There is no such thing a san EOF byte in the file - EOF is just a signal returned. It is a special value. 


### Buffering on standard I/O 

Buffering determines how often the contents of a stream are sent to their destination. This is useful because sending one character at a time is inefficient. 

What is the difference between stdout and stderr?

**Standard error is unbuffered**. Whatever you send to stderr, it will send immediately. **Unbuffered streams are constantly flushed to their destination**. 

> **Line-buffered** streams are only flushed to its destination after a newline character is written. So for example, printf( ) goes to stdout, which is line-buffered when it's connected to terminal screen. So it will not actually print until a newline character is reached. If your program crashes before it reaches a printf with a newline, nothing will print. So when you call printf, you are actually printing lines, not characters.
> 
> **Block-buffered** streams are flushed when they reach a certain size. Stdin and everything else (all other files, remember stdin/out/err are all files!) are block-buffered. 
> 

**fflush(fp)** can be used to manually flush the buffer for any file pointer.

**setbuf(fp, NULL)** turns off buffering for fp, to make it unbuffered. 
















```c
```
















