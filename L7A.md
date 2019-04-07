# L7 - 02.19.19
Note: was in class

## Const 

```c
void strcpy(char *t, const char *s);
```
> char *t is the target
> const char *s is the source 
> 

Const tells us that this function won't modify what s points to

```c
const char *p = &c;

//NO 
*p = 'A'; // ERROR compiler, cannot change the thing 
          // that p points to     
// YES
p = &d; // WORKS - change p itself, where it points

char *q = (char *) p; // cast const p to non-const
*q = 'A'; // WORKS 
```

Const is most useful for documentation and accident catching with the compiler errors, but you can work around it 

## Pointer to Function 
```c
void main(){
  int a[5] = {1, 27, -12, 0, 5};
  qsort (&a[0], 5, sizeof(int), ________); 
}
```

What goes in place of the blanks?

qsort prototype: 
```c
void qsort(void *a, size_t num, size_t size, 
          int (*f)(const void *, const void *));
```
> a is the pointer to what we are sorting
> num is the number of elements in whatever we're sorting
> size is the size of each individual element
> and the last is a pointer to a function 
> 

```c
int (*)(const void *, const void *)
```
> The (*) indicates a poitner to a function
> int indicates the return type of that function
> There are two inputs to this function, both are const void pointers
> 

qsort implements quicksort algorithm with a starting address of an array. It knows how big they are so it can recognize chunks of elements for the operation of swapping.

qsort MOVES elements but it does not know how to COMPARE elements. That is where the (*f) comes in. The function that qsort is called with to point to is the fucntion that knows how to COMPARE the elements. 

We must write the function that compares two elements and qsort will repeatedly call it. So in calling qsort, we have to pass an argument to it that is a pointer to a comparator function. 

For example: 
```c
 qsort (&a[0], 5, sizeof(int), &compInt); 
 // where compInt is a function we implement to 
 // compare integer elements
 ```

**typedef** introduces a a synonym for a type. This is where size_t comes from. For example:
```c 
typedef int MyInt;
```

Implementing the comparator function: 
```c
int compInt(const void *x, const void *y){
  int *a = (int *) x;  // We know we're sorting int
  int *b = (int *) y;  // so we cast void* to int*
          // Now that they're casted, we can deref
  if (*a < *b) 
    return -1;
  else if (*a > *b)
    return 1; 
  else 
    return 0; 
}
```

Alternatively to assigning a and b, we could have dereferenced directly after casting as follows: 
```c
  *(int *)x - *(int *)y; 
```
 This will automatically return 0 if they're equal, a positive number if x > y, and a negative number if y > x. So the entire above compInt can be replaced by returning the above. 
 
Note: when calling qsort, you can also just call compInt without &compInt and it will automatically cast it to an address

```c
int main(){
  int(*f1)(void *);  //Declare a pointer to a funct
                     // f1 is a variable 
                     // f1's type is int(*)(void *)
  int *f2(void *);   // f2 is a protype of a funct
                     // the function takes void *
                     // and returns int *
  int(*f3[5])(void *); // f3 is an array of
        // five things that are each of type of f1
}
```

**Parsing function pointers**
```c
int foo(void *p) {... return 5;}

f1 = &foo;  // f1 is a pointer to foo

foo(NULL);   // WORKS
(*f1)(NULL); // WORKS, same as foo(NULL);
f1(NULL);    // WORKS, call without dereferencing 
             // the pointer works the same way
             
             
f3[0] = foo; // assign foo as first element of f3
fr[0](NULL); // WORKS, same as above 
```

## Complex Declarations

Strategy #1 - from outside inwards - Replace inside elements with E
```c
char (*(*x())[])()

1. char (E)()
    where E is *(*x())[]
    E is a function returning char 
2. Unhide E, *(*x())[] --> *(E)[]
    E is *x()
    E is an array of pointers 
3. Unhide E, *x()
    x is a function that returns a pointer 
    
Read it backwards:
x is a function that returns a pointer to an array
of pointers to functions returning a char 
```

Strategy #2 - counterclockwise and outward
```c
char (*(*x())[])()

1. Start at name
    "x is"
2. swirl forwards
    () "a function with unspecified parameters"
3. swirl backwards
    * "that returns a pointer to"
4. swirl forwards
    [] "an array of"
5. swirl backwards
    * "pointers to"
6. swirl fowards
  () "functions with unspecified parameters"
7. swirl backwards
    char "that return char"
    
Read it all together!
```

## Structs 

Structs are a way of gouping or aggregating things. When you declare a struct, you declare a new type. Thist must go at the top of the file that uses it or in a header file.

```c
struct Pt{    // Declare a struct - a point (x,y)
  double x;
  double y;
};

int main(){
  struct Pt p1; // Create p1 of type Pt
  
  p1.x = 1.0; // Assign the x component
  p1.y = 2.0; /// Assign the y component 
  struct Pt *q1 = &p1; // A pointer to the struct
}
```
p1 is a 16 byte structure on the stack. The size of a struct object is the size of its component parts, in this case two doubles which are each 8 bytes. 

@startuml
object q1
object p1
p1 : x = 1.0
p1 : y = 2.0 
q1 --> p1: points to
@enduml

p1 is declared on the stack as successive variables. q1 points to the first one. Say 1.0 is at adress 2000 and 2.0 at adress 2008. The struct goes from 2000 to 2016

What is the difference between &p1 and &p1.x? They address the same thing, but a different type. 
> &p1.x is of type double *
> &p1.x + 1 --> 2008, jump to next double
> &p1 + 1 --> 2016, jump to next struct
> Will not work: q1 = &p1.x because q1 is declared as a pointer to a struct 

Dereferncing a pointer to a struct:
```c
  (*q1).x = 3.0; // You access the struct directly,
                // so the dot notation works 
  q1 -> x = 4.0; // the -> is how to access the
                // components of a struct by
                // its pointer
```
Allocating an array of structs:
```c
struct pt *p = malloc(sizeof(struct pt)*5);
p[2].x = 100; // VALID SYNTAX
            // p[2] = *(p +2)
            // This will assign 100 to the 
            // third struct's x field
(p + 2) -> y = 200; // VALID SYNTAX 
```

> sizeof( p) is 8, it's a pointer!
> sizeof(*p) is 16, it's a struct of size 16