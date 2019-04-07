# L09 02.21.19

Note: overlaps with 2/21
Note: missed class, making up from Serena notes and textbook

## Standard I/O/Error

**scanf()** reads user input from theyboard and parses that characters, recognizing it as either an integer, double, string, etc.

Every program has some notion of standard input and standard output.
**standard input** where the typical input comes from  - keyboard
**standard output** where the typical output comes from - screen

>Programs associated with stdin/stdoout:
> - **scanf( )** always reads from standard input
> - **printf( )** always reads from standard output
> These are both **variadic functions** which means they take a variable number of arguments(they can have 0 or 20 or 100 or 437 arguments)

By default, when a program starts up, standard input is connected to the keyboard, and standard output to the screen 
**standard error** is also connected to the terminals creen. We can send output to standard error

>Example of standard error: valgring prints its own message, these messages actually go to standard error, a different channel than standard output. 
>
> Both standard output and error go to the terminal screen int his case, although they can go to separate places 
> 
Three standard I/O channels to start with:
> standard input - connected to keybord
> standard output - connected to terminal output
> standard error - connect to terminal output 

## Redirection

**It is possible to redirect output using the > shell operator**
> Output can be redirected to a particular file
> Either appends or overwrites the output of an existing file with that name


```c
./a.out > myout
```
> The output goes to the file "myout", not to erminal screen
> The input is still taken from the terminal, if there is input to take
> This overwrites the existing "myout" file, and creates a new file
> 

```c
cat myout
```
> Dumps the output of ./a.out onto terminal screen
> 
```c
./a.out >> myout
```
> This appends the output to the contents of myout, instead of replacing 
> 

```c
cat myout
```
> The 'cat' command reads a file byte by byte and sends output to standard output and then it apepars in the terminal 
> 
```c
cat a.out
```
> Outputs lots of random non-human readable bytes
> 

**It is also possible to redirect input using the < shell operator**

Assume there is a file "myin", which has nothing other than the integer "678" in its text.

```c
cat myin
```
> Displays 678 on the screen
> 
```c
./a.out < myin >> myout
```
> Input is read from myin and the output of ./a.out is appended to myout
> 

Differentiate between standard I/O and file I/O:

>The program takes it input from myin and writes it output to myout, this is standard I/O not file I/O
>
>The shell program is doing the I/O redirection - you are telling the shell program to run a.out, but before that, rearrange the program's (a.out) standard input and output so that it comes from myin and goes to myout
>
>The file (a.out) does not know where theinput is ocming from or where the output will be sent to 
>
> It is the shell that interprets the command as standard I/O before the program is run 
> 
> The mechanism of standard I/O has been modified to come from and go to files instead of the keyboard or terminal, respectively 
> 
> To read from or write to more than one file, you need to do file I/O: opening files in the program usi ng the file name and reading the file. File I/O entails opening and reading files from within a program
> 

Note: the shell interprets the command before any command line arguments to a program are considered. So '< myin >> myout' are not interpreted as command line arguments to a.out but rather as part of a command for standard I/O

Let's say that within a.out there was 2 scanf( )'s. The standard I/O will not switch back to the keyboard for the second one. So the second scanf( ) would fail because it is coming from a file (myin). The return value (of scanf( ) #2) would be 0 and you would have some garbage value for the second input. 

In general, scanf() keeps reading until it sees whitespace (newline character, tab, space)

Underlying UNIX philosopy: treat everything as a file. Even the speaker on your computer, for an example. There is a special file representing that device and you write bytes to it that are outputted as sound.

```c
./a.out < myin >> myout 2>&1
```
> Redirect standard error to standard output 
> Redirect standard output to myout (in append mode) and redirect "2" (standard error) to where "1" (standard output) goes 
>


### Pipeline - redirect input from one program to come from output of another

```c
echo 5
// Prints:
5
```
> echo will print out whatever the commmand line arguments are
> It sends its input to standard output
> A newling character is also sent to the end 

```c
echo | ./a.out > myout
```
The **| operator**  is used to connect the standard output of a program to (using a direct pipeline) to the standard input of another program
> In the above example, the output of echo becomes the input of a.out

Philosophy of UNIX: you have small programs that can be stitched together to do what you want

**Pipeline example** - the w command tells you who's logged inr ight now, and other info 

Once logged into clac:
```c
w

// PRINTS:
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
dfj2106  pts/0    207.237.164.217  14Mar19 13days  0.64s  0.23s vim mdb-lookup.
flq2101  pts/2    tmux(1217).%0    07Mar19 37:18m 21.42s 20.91s vim mdb-lookup-
dfj2106  pts/3    207.237.164.217  14Mar19 13days  0.09s  0.03s vim mdb.h
htd2109  pts/4    tmux(24670).%0   04Mar19 22days  2.82s  2.77s vim mylist.c
.
.
.
ds3439   pts/108  160.39.29.16     13:13    4:51   0.08s  0.08s -bash
jk4102   pts/110  160.39.130.161   13:25    2:47   0.16s  0.16s -bash
ob2267   pts/112  160.39.162.232   13:29    4:19   0.07s  0.07s -bash
hcc2134  pts/114  160.39.249.61    13:30   43.00s  0.07s  0.07s -bash
nelson   pts/118  tmux(30877).%3   04Mar19 22days  0.51s  0.44s vim mylist.c

```

```c
w | tail -n 4

// PRINTS:
jk4102   pts/110  160.39.130.161   13:25    3:57   0.16s  0.16s -bash
ob2267   pts/112  160.39.162.232   13:29    5:29   0.07s  0.07s -bash
hcc2134  pts/114  160.39.249.61    13:30    1:53   0.07s  0.07s -bash
nelson   pts/118  tmux(30877).%3   04Mar19 22days  0.51s  0.44s vim mylist.c
```
> tail takes input and outputs the last n lines (default is 10). Here, we are feeding the output of w (as the first 4 and last 4 lines are showed above), and putting all of that list of users into the input for tail. We are then telling tail to output (print to terminal) only the last 4 lines. As demonstrated above 
> 

```c
w | tail -n +3

//PRINTS all of the output of w 
// without the first two liines

```
> Skips the first two lines
> 

```c
w | tail -n + 3 | grep vim
```
> grep looks for the characters "vim" in a given input and outputs wherever they are found. This will print all lines of w where vim appears, with "vim" in red, and skipping the first two lines
> 

```c
w | tail -n +3 | grep vim | cut -d " " -f 1

// PRINTS
dfj2106
flq2101
dfj2106
htd2109
.
.
.
```

> cut removes sections of lines, delimitted by space " " so this will print out each line of w, minus the top 2, where vim appears, but it will only print from the first character until the first space, i.e. the first field of the "table", i.e. the UNIs
> 

```c
w | tail -n +3 | grep vim | cut -d " " -f 1 | 
  sed 's/$/@columbia.edu/'
  
  // PRINTS:
dfj2106@columbia.edu
flq2101@columbia.edu
dfj2106@columbia.edu
htd2109@columbia.edu
.
.
.
```
> sed substitutes, /$ is the end of the line$
> 
```c
w | tail -n +3 | grep vim | cut -d " " -f 1 | 
  sed 's/$/@columbia.edu/' | sort > vimusers.txt
``````
> Sorts (sort) the output of UNIs (cut -d " " -f 1) appended by @columbia.edu (sed 's/$/@columbia.edu/)from all users logged in (w) minus the first two (tail -n +3) who are currently running something that contains "vim" (grep vim) and puts the output in a file called vimusers.txt
> 
> After running the above command, there is a file called vimusers.txt which has the list of emails of users in alphabetical order. 
> 

### printf( )

**The printf( ) prototype:**

```c
int printf(const char *format, ...); 
```
printf( ) takes char * (a string) and the arguments (if any) that come after depends on the format within the char * :
> %d - int
> %u - unsigned int
> %ld - long
> %lu - unsigned long
> %s - string (char * )
> %f - double
> %p - memory address (void * ), a generic pointer 

Functionality of printf( ):
> Keeps printing, and if it encounters the % sign, then it knows that the next character will determine the type printed from the next argument in the list
> 
> Grabs bytes on the stack (not allocated) as it encounters type specifiers (4 bytes for int, etc.)
> 
> Normally arguments are put ont he stack (fixed parameter list), but for printf( ), this is determined at runtime because we do not know in advance how many parameters there will be 
> 

### scanf( )

**The scanf( ) prototype:**

```c
int scanf(const char *format, ...);
```
scanf( ) takes a char * (a pointer to a char). It reads chars from standard input, interprets them according to the format specified, and stores results. Each argument must be a pointer, indicate where to store the corrresponding input. It returns the number of items found. 

Example: 
```c
// Read 25 Dec 1998

int day, year;
char monthName[20];
scanf("%d %s %d\n", &day, monthName, &year);
// &day and &year turns a variable into a pointer
// monthName is already a pointer
```

**The sscanf( ) prototype:**

```c
int sscanf(const char *input_string, const char *format, ...)
```
> Instead of reading from standard input, it reads from a string 
> Going from string to int
> Equivalent to Integer.parseInt( ) in Java
> 
> format - a string that contains one or more of: whitespace characters, non-whitespace characters, format specifiers
> 
Example using sscanf( ):
```c
int main () {
   int day, year;
   char weekday[20], month[20], dtm[100];

   strcpy( dtm, "Saturday March 25 1989" );
   sscanf( dtm, "%s %s %d  %d", weekday, month, &day, &year );

   printf("%s %d, %d = %s\n", month, day, year, weekday );
    
   return(0);
}
```

**The sprintf prototype**

```c
int sprintf(char *str, const char *format, ...)
```

> Instead of printing to standard output (screen), it would, for example, write to memory pointed to by output_buffer
> This is equivalent to Java's toString because it can take any types of arguments (int/double) and incorporate them into a string 
> 
Example using sprintf( ):
```c
int main () {
   char str[80];

   sprintf(str, "Value of Pi = %f", M_PI);
   puts(str);
   
   return(0);
}
// M_PI is an internal variable of math.h
// PRINTS: 
// Value of Pi = 3.141593
```
**The snprintf prototype**
```c
int snprintf(char *output_buffer, size_t size, const char * format, ...);
```
> This is a safer version of sprintf. It prevents you from writing past what is allocated in output_buffer if you specify the size. 
> 

### More on the I/O channels

There is only 1 input (standard input) and 2 output (standard output and erorr) channels. This is limitted.

For example, you cannot read from two files and output to five files with redirection. 












