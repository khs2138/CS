# L09 03.07.19

## Binary Files 

**Binary files** are files that are not text files. To file modes (r/w/a/r+/w+/a+), you need to add "b" for compatibility with Windows, but this is not needed in Linux. 

C was ported to other platforms, but it was created for Unix systems. When it was ported to Windows, they decided a newline character is 2 bytes \r\n (13 and 10) at the end of each line in a file. 

In Windows, when printing/writing, it automatically turns \n into \r\n. When reading, it turns \r\n into \n.

But this should not happen when not working with text files (i.e. image files). The 'b' flag indicates to avoid the above conversions. In Linux/Unix, the 'b' does nothing, but is needed for portability of a code. 

## fread( ) and fseek( )

```c
size_t fread(void *p, size_t size, size_t n, FILE* file);
```
> fread( ) reads n objects of size bytes long from file into the memory location pointed to by p. 
> 
> It returns the number of objects successfully read, which may be less than the requested number n, in which case feof( ) and ferror( ) can be used to determine status. 


```c
int fseek(FILE *file, long offset, int origin);
```
> fseek( ) lets you hope through a function, doing nothing other than changing the position in the FILE structure. 
> 
> If the file you are using is a binary file, offset can be any number of bytes/characters from origin, which should be set to one of the following
> > SEEK_SET - from the beginning
> > SEEK_CUR - move the pointer from where you are (negative offset moves it backwards)
> > SEEK_END - at the end
>
> Returns 0 on the success, nonzero on error 
> 

The new position, measured in bytes, is obtained by adding offset bytes to the position specified by origin.  If origin is set to SEEK_SET, SEEK_CUR, or SEEK_END, the offset is relative to the start of the file, the current position indicator, or end-of-file, respectively.
     
## Lab 4 Demo 

>**mdb-cs3157** is the database file. Can put name and message into the file
>**mdb-lookup** is used to look up messages (takes a command-line parameter for what database to look into)
>**mdb-lookup-cs3157** (takes no parameter, hardcoded) can search for messages/name in database
> CTRL-D to exit: this simulates the EOF condition. The program can detect this and quit assignment
> 
> Lab: write mdb-lookup.c
>

We can copy over executables (mdb-add and mdb-lookup) into our directory to work with them. 

```c
cd cs3157-pub/bin/
ll

./mdb-lookup-cs3157
./mdb-add-cs3157
(type in name and message)
```
If we add an entry, the database file grows. Each record is 40 bytes. 

Use strcpy() to write into the struct MdbRec

Dump 40 bytes into a file.
The program reads 40 bytes at a time.
The structure gets put into a linked list.
Keep reading the records and load into a linked list.
Manage the memory (malloc).
Continue until fread() fails.

Once the linked list is built, enter the lookup prompt. Take user input (only 5 characters), and go through linked list for a match. (If no input, prints everything). 

The unfilled spaces in the array are filled with random bytes in the struct. 

Binary file - cannot use fgets() to read from the database. Might have a \n byte in the garbage. Don't use fgets() to read a fixed amount of data. 

