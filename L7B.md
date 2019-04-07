# L7/9 - 02.21.19 
Note: missed class, notes from girl from mesorah 

## Passing by value 

```c
// DOES NOT WORK, PASS BY REFERENCE
void transpose(struct pt p) {
  swap(&p.x, &p.y);

}
// p is a local variable in transpose 
// this takes the values of p given in main

int main(){
  struct pt p = {1.0, 2.0}
  transpose(p);
}
```


```c
// WORKS, PASS BY VALUE
void transpose(struct pt *p) {
  swap(&p -> x, &p -> y);

}
// p is a local variable in transpose 
// this takes the values of p given in main

int main(){
  struct pt p = {1.0, 2.0}
  transpose(&p);
}
```

In both cases, the & is there in the call to swap because swap takes a pointer. So regardless the and will be there. The difference is in the first version which does not work, we are passing a struct itself to swap. Whereas in the second version, we are passing a pointer to a struct by passing &p. The fact that p is used in both transpose and main should not be confused. In the workign second version, transpose's p is a pointer to main's p, which is a struct.

## Linked List in C

```c
struct Node{
  struct Node *next;
  int val; 
};
```
The above declares a type of struct, a Node, which forms a linked list by having two fields: a next field and a value itself. 

**Self-referential structure** The declaration of a node with a node as one of its fields works because the field is a pointer a struct. You cannot put a struct itself as  field. A pointer is 8 bytes, so the compiler can identify the size of the struct which is 12 bytes total, but it will most probably be allocated 16 bytes, as a multiple of 8.

What is the significance of allocating in multiples of 8? if we assign these things to be an array, they each have to occupy address multiples of 8 apart in order to use a pointer to move from one element to the next (pointers are of size 8). 


### create2Nodes()

```c
struct Node *create2Nodes(){
  struct Node *n1 = malloc(sizeof(struct Node));
  n1 -> next = NULL;
  n1 -> val = 100; 
  struct Node *n2 = malloc(sizeof(struct Node));
  n2 -> next = n1;
  n2 -> val = 200; 
  return n2
}

int main(){
  struct Node *head = NULL;
  head = create2Nodes();
  printf("head n2 %d--> n1 %d", 
        head->val, head->next->val);
  free(head->next);
  free(head); 
}
```
Prints: 
> head n2 200 --> n1 100
@startuml
object null
object head
head --> null: points to
@enduml

@startuml
object node1
node1 : val = 100
object node2
object head
head --> node2: points to
object null
node2 : val = 300
node2 --> node1: points to
node1 --> null: points to
@enduml


Every time you malloc something, you have to subsequently free it. Free it in the proper order so that you do not lose a reference to the next thing that you have to free. 

Head is a pointer to a node, n2 (which is a node)
An empty linked list is a head pointer that points to null, like on the left before enterring the function create2Nodes()

|Command| Stack | Heap |
| ------|----|-------|
|struct Node *head = NULL | head [ ] | |
|*enter create2Nodes()* | | |
|struct Node *n1 = malloc... | n1 [ ]| |
|n1 -> next = NULL| |next [ ] |
| n1 -> val = 100| | val = 100|
|struct Node *n1 = malloc... | n2 [ ]| |
|n1 -> next = NULL| |next [ ] points to next[ ] above|
| n1 -> val = 100| | val = 200|
|*function return to main* |Cleared | Maintained|
|head = create2Nodes()|head[ ] | points to next #2| 
|free(head->next) |  |Remove memory of next, val #1 | 
|free(head) | | Remove memory of next, val #2| 

You can use nodes themselves in a linked list as a while loop header.
```c
Node *n = head;
while(n){
  printf("%d-->", n -> val);
  n = n -> next; 
}
```
# L9 - I/O
## Standard I/O and redirection 

The keyboard is the **standard input.** Standard text stream is a series of lines: a series of characters followed by a new line character. You can read one character at a time from standard input (with getchar()). 

```c
int getchar(void) // Returns a char or EOF
```

The terminal screen is **standard output.**

**Standard error** is to output a character stream onto the terminal 

**Redirection**: 
> **executable > filename** redirects output into the named file, rather than on the terminal screen 
> **executable < filename** inputs the required values for the executable from the filename 
> Redirections can be combined without differentiation as to order of input/output
>  **executable < filename1 > filename2** run the executable with input from filename1 and put the output into filename2
>  **executable 2> filename** redirects the output into filename 
>  **executable >> filename** The output of the executable will be appended to whatever exists in filename, rather than overwriting everything 
>  **executable >> filename 2>&1** redirect the error to the same place as the output

## Pipeline

Pipeline feeds output of one program to the input of another 
> **executable < filename | executable** 
The following code will print any lines that the pattern hex is found 
```c
$./a.out < myin | grep hex 
```
echo prints whatever you write so:
```c
echo 555 | ./a.out
```
The above code will input 555 into the program of a.out 