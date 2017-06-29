# Chapter 4 Compound Types (part 2)

### 4.9 Using `new`/`delete` to manage dynamic arrays

- If all a program needs is a single value, you may as well declare a variable in the common way, because that is simpler, if less impressive, than using `new` and a pointer to manage a small data object. More typically, `new` is employed to deal with larger chunk of data, particularly when the size of the chunk is not known until runtime. Allocating the array during compile time is termed *early binding* or *static binding*, meaning the size of the array is built in the program code; whereas with `new`, it is permitted to create arrays and specify their sizes during runtime, and this is called *late binding* or *dynamic binding*.

- Dynamic memory allocation: `type *ptr = new type[size];`

  > The `new` returns the address of the array's 'first element's first byte, to the pointer `ptr`.

  ```c++
  int *p = new int[2]; int *q = new int[2] {1,2}; // size must be specified
  ```

  > Note that the brackets `[]` is <u>behind</u> the type name.

- Dynamic memory deallocation: `delete[]ptr;` 

  > The presence of the brackets `[]` tells the compiler that your intention is to free the whole array, not just the element pointed to by the pointer. As discussed in 4.1, the name of an array stores the address <u>of the first element</u>, NOT conceptually the address of the whole array. `delete` and `delete[]` can be viewed as different operators.

  ```C++
  delete[]p;   // free the whole block of memory
  delete p;    // just the memory block of the first element is freed
  ```

  > TIP: If your `new` statement has a pair of `[]`, then your corresponding `delete` should also has a pair of `[]` to balance it out. 
  >
  > Note that the brackets `[]` is <u>between</u> the keyword `delete` and the pointer.


- Using a dynamic arrays: note the fact that if `a` is a pointer, then `a[n]` is equivalent to `*(a+n)`.

  ```C++
  double *t = new double {0}, *p = new double[3];
  std::cout << "*t is " << *t << ", address = " << t << endl;
  delete t;
  p[0] = 0; p[1] = 8; p[2] = 8;
  std::cout << "p[2] is " << p[2] << ", address = " << p+2 << endl;
  delete[]p;
  ```

  - A side note: suppose `a` is an array name. Though `a` can be used to represent the array's address, `a` is an array name after all. That means this statement generates an error: `a = a + 1;`, because you cannot change the "value" of an array name (a name has no value). Discussed in 4.1.

  - However, in dynamic memory allocation, `int *a = new int[10];` means `a` is a pointer; though we can treat it as an array name in statement like `a[0] = 1;`, it is essentially a pointer, enabling you can change its value: `a = a + 1;` and this operation complies with pointer arithmetic operation rules.

    > The **near** equivalence of pointers and array names is arguably one of the beauties of C and C++, yet it is also controversially one of the flaws.

- Dynamic multidimensional array: a bit more complicated and care-demanding.

  - <u>A multidimensional array consists of multiple subarrays</u>. For instance, declaring a multidimensional array `int a[3][4];` means `a` is an array consisting of 3 elements, and each of the element of `a` is also an array, which holds 4 elements. Therefore, `a`, together with`a[0]`, `a[1]`, `a[2]`, is a pointer. This fact is exemplified in the following code:

    ```C++
    int a[3][4];
    std::cout << a << endl;        // 008FF990, an 'int **' pointer
    std::cout << a[0] << endl;     // 008FF990, an 'int *' pointer
    std::cout << a[1] << endl;     // 008FF9A0, an 'int *' pointer
    std::cout << a[2] << endl;     // 008FF9B0, an 'int *' pointer
    std::cout << a[2][3] << endl;  // -858993460 (garbage), a 'int'
    ```

    > NOTE: `a` is a pointer-to-pointer-to-`int` - sounds awkward? `a[0]` is a pointer-to-`int`, hence `a[0]` is of type `int *`. And pointer `a` points to array `a`'s first element, which is `a[0]` (a pointer `int *`), so `a` is `int **`.

    Likewise, in a 3-dimensional array `b[3][4][5]`, the following expressions are pointers: `b`, `b[0]`...`b[2]`, `b[0][0]`...`b[0][3]`, `b[1][0]`...`b[1][3]`, `b[2][0]`...`b[2][3]`. And expressions  `b[0][0][0]`...`b[2][3][4]` are integers.

    > TIP: for an n-dimensional array `a` of type `type`:
    >
    > `a[.][.]...[.][.][.]` (n indexes) is '`type`' (0 star); -- *sum = n*,
    >
    > `a[.][.]...[.][.]`(n-1 indexes) is '`type *`'(1 star); -- *sum = n*,
    >
    > `a[.][.]...[.]`(n-2 indexes) is '`type **`'(2 stars); -- *sum = n* , ... ,
    >
    > `a`(0 index) is '`type **..*`'(n stars). -- *sum = n*

  - With that in mind, it is natural to use the following codes to allocate and deallocate multidimensional arrays.

    ```C++
    // one-dimencional - an appetizer
    // allocate
    int *a = new int[size];
    // deallocate
    delete[]a;
    ```

    ```C++
    // two-dimensional - row, column
    // allocate
    int **b = new int*[row];  // each element of b is an 'int *'
    for(int i = 0; i < row; i++)
    {
      b[i] = new int[column]; // each element of b[i] is an 'int'
    }
    // deallocate
    for(int i = 0; i < row; i++)
    {
      delete[]b[i];           // b[i] is a pointer point to array
                              // {b[i][0] ... b[i][column-1]}
    }
    delete[]b;                // b is a pointer point to an array
                              // {b[0] ... b[row]}
    ```

    ```C++
    // three-dimensional - row, column, height
    // allocate
    int ***c = new int **[row];
    for(int i = 0; i < row; i++)
    {
      c[i] = new int **[column];  // c[i] is 'int **', 1-fold loop
      for(int j = 0; j < column; j++)
      {
        c[i][j] = new int[height];// c[i][j] is 'int *', 2-fold loop
      }
    }
    // deallocate
    for(int i = 0; i < row; i++)
    {
      for(int j = 0; j < column; j++)
      {
        delete[]c[i][j];         // c[i][j] is 'int *', 2-fold loop
      }
      delete[]c[i];              // c[i] is 'int **', 1-fold loop
    }
    delete[]c;
    ```

### 4.10 Using `new`/`delete` to manage dynamic structures

- The logic behind dynamic structures is muck like the logic behind dynamic simple variables or dynamic arrays. In fact, this line of thought applies to other dynamic types, class objects included.

- Basic usage:

  ```C++
  struct stock{
    std::string stockName;
    double timeTag;
    double price;
  };
  // allocate
  stock *Stock = new stock;
  stock *myStocks = new stock[17];
  ```

  ```C++
  // use. Pay attention to operator precedence rule in C++.
  Stock->stockName = "Emma Photography";
  (*Stock).timeTag = 111022;                // ugly but valid
  *Stock.price = 20.50;                     // invalid.
  myStocks[0]->stockName = "Dennis Studio";
  (*myStocks[0]).timeTag = 111022;          //ugly but valid
  *myStocks[0].price = 17.88;               // invalid.
  ```

  ```C++
  // deallocate
  delete Stock;
  delete[]myStocks;
  ```

### 4.11 Automatic storage, static storage, and dynamic storage

- C++ has three ways of managing memory for data, namely automatic, static, and dynamic storage, depending on the method used to allocate memory. Dynamic storage is sometimes called the *heap* or the *free store*.

- Data objects allocated in these three ways differ from each other in their *storage duration* - how long they remain in existence. Chapter 9 discusses other differences among these three storage methods in more detail.

- Automatic storage

  - Ordinary variables defined inside a function use *automatic storage* and thus are called *automatic variables*. This term suggests that these variables come to existence automatically when the code block containing them is invoked, and expire when the code block terminates. On the memory's point of view, memory blocks are allocated for them automatically when the code block starts running, and are freed automatically when the code block terminates.

    > Code block:  a section of code between braces `{}`. e.g. a function is a code block, and a `for` or `if` statement is also a code block (inside a function).

  - Automatic variables are local to the block of code that contains them. They are invisible outside the block, and exist during the block's lifetime.

  - Automatic variables are typically stored on the *stack* of the program's memory. This mean that when program execution enters a block of code, its variables are added consecutively to the stack in memory and are freed in reverse order (*First-in-last-out*, FILO). The stack grows and shrinks as execution proceeds.

- Static storage

  - *Static storage* is storage that exists throughout the lifetime of <u>the entire program</u>. There are two ways to make a variable static. One is to define it externally, outside any function. The other is to use the keyword `static` when declaring a variable, e.g. `static double price = 2.5;`.
  - Static variables are stored on *static storage area*.

- Dynamic storage

  - The glamorous `new` and `delete` operators provide a more flexible approach than automatic and static variables. These two operators manage a pool of memory, which is referred to as the *heap*, or *free store*.

  - The heap is separate from the memory used for automatic and static variables.

  - The lifetime of data on the heap is determined by `new` and `delete`, and is not tied arbitrarily to the life of a code block (automatic storage) or a program (static storage). Using `new` and `delete` gives you much more control over how a program uses memory than does using ordinary variable declarations.

  - In a stack, the automatic addition and removal mechanism results in the part of memory in use always being contiguous. Bur the interplay between `new` and `delete` can leave "holes" in the heap, making keeping track of where to allocate new memory requests a little bit tricky for the computer.

  - Generally, the efficiency of using the heap is lower than that of using the stack.

  - What happens if you *do not* call delete after creating a variable on the heap with the `new` operator? The variable or construct dynamically allocated on the free store continues to persist if `delete` is not called, even though the memory that contains the pointer has been freed due to rules of scope and object lifetime. In essence, you have no way to access the construct on the heap because the pointer to the memory that contains it is gone. You have now created a *memory leak*.

    > Even the best programmers and software companies create memory leaks. To avoid them, it is best to get into the habit of joining your `new` and `delete` operators immediately, planning for and entering the deletion of your construct as soon as you dynamically allocate it. C++’s smart pointers (Chapter 16) help automate the task.

### 4.12 Array alternatives 1: the `vector` template class

- The `vector` template class is similar to the `string` class in that it is a dynamic array - its size can be set during runtime, and you can append new data to the end or insert new data in the middle. Because it is dynamic, objects of `vector` class are stored in the heap, just like variables whose memory are allocated by `new`.

- Though `vector` objects are stored in the heap and thus use `use` and `delete` to manage the memory, it does not need you to take care - `new` and `delete` are  called automatically by `vector` objects. This feature enables you to use them just like ordinary automatic variables.

- The definition of `vector` is included in header `<vector>` and the `vector` identifier is part of the `std` namespace, as are other template classes/objects identifiers.

- Declare `vector` objects of a certain type

  ```C++
  std::vector<int> vi;         // create a zero-size vector of 'int'
  std::vector<double> vd(n);   // create an vector of n 'double'
  std::vector<std::string> vst;// the type can be of other template class
  std::vector<Car> vcar(6);    // the type it stores can be user-defined
  std::vector<float> vf(2) = {1.0, 2}; // list-initialization is allowed
  ```

- Some methods `vector` class provides:

  ```C++
  vi.push_back(2);             // append integer 2 to the vector vi;
  vi.push_back(2);             // append another integer
  vi.push_back(5);             // append yet another integer
  int a = vi.pop_back();       // pop up the last element and delete it
                               // from vi. Now a = 3;
  int b = vi.at(0);            // returns the 0th element of vi to b
  int s = vi.size();           // returns the number of elements of vi
  vi.clear();                  // clear vi
  bool e = vi.empty();         // If vi is empty, then return true.
  ```

  - `vector` methods generally has safe-checking mechanism (such as avoid access violation).

### 4.13 Array alternatives 2: the `array` template class

- The `vector` class has more capabilities than the built-in array type, but this comes at a cost of slightly less runtime efficiency. If all you need is a fixed-size array, you might very well use built-in array types. However, as you can imagine, that has its own cost of lessened convenience and safety, which is why C++ introduced `vector` class in the first place. C++11 responded to this situation by adding the `array` template, in an attempt to eradicate the usage of built-in array types.

- An `array` object has fixed size specified during compile time, hence it uses the stack (or else static memory allocation) instead of the heap. So it shares the efficiency of  built-in array types, and convenience and safety are also guaranteed.

- The definition of `array` is included in header `<array>` and the `array` identifier is part of the `std` namespace, as are other template classes/objects identifiers.

- Declare `array` objects of a certain type and size (fixed)

  ```C++
  std::array<int, 5> ai;
  std::array<char, 4> ac = {'a', 'b', 'k', 'q'};
  std::array<Car,6> aCar;
  std::array<bool, 3> ab {true, false, true}; // list-initialization
  ```

- Methods `array` class provides include `at()`, `front()`, `back()`, `size()`, `empty` and so forth.

  - `vector` methods generally has safe-checking mechanism (such as avoid access violation).

### 4.14 Pointer arithmetic

- Adding a integer to a pointer is allowed. The result of adding one equals the original address value plus a value equal to the number of bytes in the pointed-to object.
- Subtracting an integer from a pointer is also allowed. The result of subtracting one equals the original address value minus a value equal to the number of bytes in the pointed-to object.
- Subtracting a pointer from a pointer is allowed. The result of taking the difference between two pointers is the difference between the original address values divided by a value equal to the number of bytes in the pointed-to object.

### 4.15 Pointers and arrays mixed together

- There are two concepts that are sometimes confusing: pointer-to-array (*array pointer*) and array-of-pointer (*pointer-array*).

  - <u>An *array pointer* (pointer-to-array) is a pointer</u>  that points to an array (not the first element of the array, but the whole array - though the address value is the same). The pointer is of type '`type (*)[size]`'. For instance,

    ```C++
    int a[3];
    int (*p)[3] = &a;
    ```

    Note that assigning `&a[0]` to `pa` generates an error, because the types do not match, though the value of `&a[0]` equals to the value of `&a`, as is discussed in 4.1.

  - <u>An *pointer array* (array-of-pointer) is an array</u> that hold one or more pointers. The array is of type '`type *[size]`'. For instance,

    ```C++
    int a, b, c;
    int *p[3] = {&a, &b, &c}; // the '=' can be dropped, of course
    ```

- From the two examples given above we may note that `int (*p)[3]` and `int *p[3]` have different meaning. Because brackets `[]` has higher precedence rank than asterisk `*`, the identifier `p` is associated to `[]` first unless there is a pair of parenthesis `()` to separate `p` and `[]`.

  - How about a pointer-to-pointer-to-array (ppa)? 

    ```C++
    // array(a): 'int [3]'
    int a[3];
    // pointer-to-array(pa): 'int (*)[3]'
    int (*q)[3] = &a;
    // pointer-to-pointer-to-array(ppa): 'int (**)[3]'
    int (**w)[3] = &q;
    ```

  - How about an array-of-pointer-to-array (apa), a pointer-to-pointer-to-array (ppa), and a pointer-to-array-of-pointer-to-array (papa)?

    ```C++
    // array(a): 'int [2]'
    int s[2], d[2], f[2];
    // pointer-to-array(pa): 'int (*)[2]'
    int (*e)[2] = &s, (*r)[2] = &d, (*t)[2] = &f;
    // (1) array-of-pointer-to-array(apa): 'int (*[3])[2]'
    int (*g[3])[2] = {e, r, t};
    // (2) pointer-to-pointer-to-array(ppa): 'int (**)[2]'
    int(**y)[2] = &e;
    // (3) pointer-to-array-of-pointer-to-array(papa): int (*(*)[3])[2]'
    int (*(*u)[3])[2] = &g;
    ```

  - How about more?

    ```C++
    // pointer(p): 'int *'
    int *i=NULL, *o=NULL, *p=NULL, *m=NULL, *n=NULL, *b=NULL;
    // array-of-pointer(ap): 'int *[2]'
    int *h[2] = {i, o}, *j[2] = {p, m}, *k[2] = {n, b};
    // pointer-to-array-of-pointer(pap): 'int *(*)[2]'
    int *(*v)[2] = &h, *(*c)[2] = &j, *(*x)[2] = &k;
    // (1) array-of-pointer-to-array-of-pointer(apap): 'int *(*[3])[2]'
    int *(*l[3])[2] = {v, c, x};
    // (2) pointer-to-pointer-to-array-of-pointer(ppap): 'int *(**)[2]'
    int *(**z)[2] = &v;
    ```

  - How about dynamic allocation? -- *Tremble, earthlings, at Pointer's mighty power.*

    ```C++
    // ppap = new pap
    int *(**zz)[2] = new (int *(*)[2]);
    // apap = {pap, pap, pap}
    int *(*ll[3])[2] {new (int *(*)[2]), new (int *(*)[2]), 
                      new (int *(*)[2])}; // list-initialization
    // DO NOT forget to free the memory, of course
    delete zz;
    for(int i=0; i<2 ;i++) delete ll[i];
    delete[]ll;
    ```

- How to navigate yourself through the "mess" of the pointers and arrays?

  ![Alt text](D:\Notebooks\C++ Supplementary Notes\Markdown\Fig 4.1.jpg "Fig 4.1 A tree representation")


- **The golden rule** is that the precedence level of `[]` and `()` is the higher than `*`, and the associativity of these three operators is left-to-right. 


- I MYSELF (July 21, 2016) have innovated an algorithm to find the correct type:

  - Suppose `g` is to be declared a variable of type c~1~c~2~...c~n~, where c~i~ is either a 'p' or an 'a^Xi^' (Xi is the number of array elements).

  - **STEP1:** Set arithmetic expression K = `g`, and set symbol string L = c~1~c~2~...c~n~.

  - **STEP2:** Read in the first (leftmost) symbol from L and delete it from L. If this symbol is 'p', then set arithmetic expression K = p $$+$$ K; if 'a^X^', then set K = K $$\times$$ a^X^. Note that the position of 'p' or 'a^X^' cannot be interchanged. Since both '$$+$$' and '$$\times$$' are ordinary arithmetic operators, so <u>parentheses may be needed to keep the original order of operation</u>. Repeat STEP2 until L is empty.

  - **STEP3:** Now we obtain the final version of arithmetic expression K. Replace 'p$$+$$' with '*', and '$$\times$$a^X^' with '[X]'. Leave parentheses unchanged.

  - **STEP4:** Insert the lowest-level type name, such as `int` or `MyClass`, to the left. Done.

    > This approach utilized the parallel comparison of the operator precedence relationships: '`[]`' > '`*`', '$$\times$$' > '$$+$$'. The reverse algorithm can be applied to deduce the type of a compound-type variable from its declaration.

(i) Suppose you want to declare a variable of pattern *a*^X^ (array). Then

​	K = `g`, L = a^X^ $$\to$$ K = K$$\times$$a^X^ = `g`$$\times$$a^X^, L =$$\varnothing$$ $$\to$$ K = `g[X]` $$\to$$ K = `int g[X]`. Done.  $$\to$$ `int g[X];` or if you would like to use comma declaration, `int g[X], h[Y];`

(ii) Suppose you want to declare a variable of pattern *p* (pointer). Then

​	K = `g`, L = p $$\to$$ K = p$$+$$K = p+`g`, L = $$\varnothing$$ $$\to$$ K = `*g` $$\to$$ K = `int *g`. Done. $$\to$$ `int *g, *h;`

(iii) Suppose you want to declare a variable of pattern *a*^X2^ *ppa*^X1^ (array-of-pointer-to-pointer-to-array). Then

​	K =`g`, L = a^X2^ ppa^X1^ $$\to$$ K = K$$\times$$a^X2^ = `g`$$\times$$a^X2^, L = ppa^X1^ $$\to$$ K = p$$+$$K = p+`g`$$\times$$a^X2^, L = pa^X1^ $$\to$$ K = p$$+$$K = p$$+$$p$$+$$`g`$$\times$$a^X2^, L = a^X1^ $$\to$$ K = K$$\times$$a^X2^ = (p$$+$$p$$+$$`g`$$\times$$a^X2^)$$\times$$a^X1^, L = $$\varnothing$$ $$\to$$ K = `(**g[X2])[X1]` $$\to$$ K = `int (**g[X2])[X1]`. Done. $$\to$$ `int (**g[X2])[X1], (**h[X2])[X1];`

(iv) Suppose you want to declare a variable of pattern *pa*^X2^ *a*^X1^ *p* (pointer-to-array-of-array-of-pointer). Then

​	K = `g`, L = pa^X2^a^X1^p $$\to$$ K = p$$+$$K = p$$+$$`g`, L = a^X2^a^X1^p $$\to$$ K = K$$\times$$a^X2^ = (p$$+$$`g`)$$\times$$a^X2^, L = a^X1^p $$\to$$ K = K$$\times$$a^X1^ =  (p$$+$$`g`)$$\times$$a^X2^$$\times$$a^X1^, L = p $$\to$$ K = p$$+$$K = p$$+$$ (p$$+$$`g`)$$\times$$a^X2^$$\times$$a^X1^, L = $$\varnothing$$ $$\to$$ K = `*(*g)[X2][X1]` $$\to$$ K = `int *(*g)[X2][X1]`. Done. $$\to$$ `int *(*g)[X2][X1], *(*h)[Y2][Y1];`

(v) Likewise, 

​	a. pa*^X^ (pointer-to-array) $$\to$$ (p$$+$$`g`)$$\times$$a^X^ $$\to$$ `int (*g)[X];`.

​	b. *a*^X^ *p* (array-of-pointer) $$\to$$ p$$+$$`g`$$\times$$a^X^ $$\to$$ `int *g[X];`.

​	c. *a*^X2^ *a*^X1^ (array-of-array, i.e. 2-D array) $$\to$$ `g`$$\times$$a^X2^$$\times$$a^X1^ $$\to$$ `int g[X2][X1];`.

​	d. *a*^X2^ *pa*^X1^ *p* $$\to$$ p$$+$$(p$$+$$ `g`$$\times$$a^X2^)$$\times$$a^X1^ $$\to$$ `int *(*g[X2])[X1];`.

​	e. *pa*^X2^ *pa*^X1^ $$\to$$ (p$$+$$(p$$+$$`g`)$$\times$$a^X2^)$$\times$$a^X1^ $$\to$$ `int (*(*g)[X2])[X1];`.

​	Utilizing C++11's automatic type deduction feature `auto`, the drudgery above can be saved...

```C++
// pointer(p): 'int *'
int *i=NULL, *o=NULL, *p=NULL, *m=NULL, *n=NULL, *b=NULL;
// array-of-pointer(ap): 'int *[2]'
int *h[2] = {i, o}, *j[2] = {p, m}, *k[2] = {n, b};
// pointer-to-array-of-pointer(pap): 'int *(*)[2]'
int *(*v)[2] = &h, *(*c)[2] = &j, *(*x)[2] = &k;
// pointer-to-pointer-to-array-of-pointer(ppap): 'int *(**)[2]'
int *(**z)[2] = &v;           // the "upright" way
auto z = &v;                  // the "cheating" way -- since C++11
```

  ... up to a point.

```C++
int *(*l[3])[2] = {v, c, x};  // the "upright" way
auto l[3] = {v,c,x};          // Want to cheat? Not allowed.
```

  In short, keyword `auto` can be used to deduce the type of variable you initialize, using the information of the initializer. However, `auto` work for a single initializer, not for multiple initializers (at least in C++11).

  The type-deduction feature provided by `auto` is compile-time deduction. It is not to be confused with *runtime type identification* (RTTI) (Chapter 15).

> A Glimpse into the Power of `auto`:
>
> The keyword `auto` is smart enough to deduce the type of a to-be-declared variable, provided the declaration has one (not multiple) initializer. That makes things easier. The code snippets below demonstrated three usages of `auto` (among other usages):
>
> ```C++
> // 1. deduce a variable's type (since C++11)
> auto i = 1, *pi = &i, j = i;
> ```
>
> ```C++
> // 2. deduce a function's return type (since C++14)
> auto function(...);
> auto function(...) {...}  
>      // the declaration and the header of
>      // the definition must use auto TOGETHER.
> ```
>
> ```C++
> // 3. serve in the trailing return type syntax (does NOT
> //    deduce the type - just a placeholder) (since C++11)
> auto function(...) -> returnType;
> auto function(...) -> returnType {...}
>      // the declaration and the header of
>      // the definition must use auto TOGETHER.
> ```
>
> It is a philosophical shift for C++ in that in the past the compiler already possessed the ability of type deduction but it used this ability to complain when codes are wrong, whereas from now on it uses this knowledge to help you.
>
> However, `auto` might cause problems - if your code is syntactically correct but is not what you actually desire, `auto` may mask the bug. For instance,
>
> ```C++
> int n {1}; int *pn = &n;
> // suppose your intention is: auto q = *pn; where the variable
> // q should be of type 'int'
> auto q = &pn;  // You are wrong, but the compiler keeps silent.
> int q = &n;    // You are wrong, and the compiler complains.
> ```
>
> Another thing about `auto` is that it may look familiar to you. Yes, it should. In C and previous C++ standards, `auto` has an entirely different meaning - it denotes "automatic storage". But programmers rarely bother using it because `auto` can only be used to specify variables declared inside a function, yet they are automatic by default. Therefore the C++11 standard committee decided to repurpose `auto` - use it to denote type-deduction and serve in the trailing return type syntax. They chose to reuse an old keyword in that a new keyword may invalidate old codes where the word is used for other purposes.

### 4.16 Pointers to functions

- Functions, like data items, have addresses. A function's address is the memory address at which the machine code (0101...) for the function begins. When a function is invoked, CPU's program counter (PC) is set to the value of its address.

- Normally, it is neither important nor useful to know function's address, but it can be useful in some special occasions. For example, it is possible to write a function that takes the address of another function as an argument - enabling the first function to find the second function call and run it.

  > This approach seems more awkward than simply having the first function call the second one directly inside the first one's body, but it leaves open the possibility of passing different function addresses at different times.
  >
  > This feature may remind you of *functional*s in mathematics.

- A simple example of having functions to take other functions' address as its argument is a function used to estimate the space/time complexity of other functions.

  > Q: What is space/time complexity?
  >
  > A: Review your algorithm course notes. (Simply put, it measures the space/time efficiency of algorithms and functions - algorithms' "embodiments").

- Obtaining the address of a function: just use the <u>function name</u> WITHOUT trialing parentheses. In the example below, the `test()` call enables the `test()` function to invoke the `testee()` function within `test()`, whereas `check()` call first invokes the `testee()` function and then passes the return value of `test()` to the `chaeck()` function.

  ```C++
  // suppose testee() is a function
  test(testee);             // pass the adress of testee().
  check(testee());          // pass the return address of testee.
  ```

- Declaring a pointer to a function: the core issue is, what is the type? The answer is, the type of a function is determined by the function's *signature*.

  > Function signature: the types of its arguments and, if the function is a class member, the `cons`/`volatile` qualifiers (if any) on the function itself and the class in which the member function is declared.
  >
  > (the word "signature" is a little bit confusing, especially for non-native English speakers. The word here means "trait" or "identification", not the handwritten name of a person.)

  Syntax: suppose you want to declare `pf` as a pointer to function `freak()`, and the prototype of `freak()` is: `double freak(int, int);`. Then `pf` should be declare in this way: `double (*pf)(int, int);`. The pointer `pf` is of type `int (*)(int, int)`.

  > Replace the function name with `*ptrName`. It is simple as that.

  ```C++
  // function prototypes
  double bark(int, double);
  double scream(int, double);
  double jump(int, int);
  ```

  ```C++
  // pointer-to-function declaration and assignment
  double (*pf)(int, double); 
  double (*pf)(int, double) = bark; // you can initialize it (or not).
  auto pg = scream;        // use auto to declare another function pointer
  double *pf(int, double); // This is NOT declaring a pointer. In stead,
                           // it is declaring a function whose return
                           // type is 'double *'. Precedence: () > *.
  ```

  ```C++
  pf = scream;      // the pointer now points to a function
  pf = jump;        // error. Mismatched function signature.
  pf = scream();    // error. You can not assign 'double' to
                    // 'double (*)(int, double)'
  ```

- Using a pointer-function to invoke a function

  - Similar to using dereference operator `*` to obtain the value held in a block of memory a pointer points to, a function can be invoked by applying `*` to the pointer which points to the memory block where the function instructions are stored, as is shown below.

    ```C++
    // function declaration
    double evaluate(double *arr, int size);
    ...
    int (*pf)(double *, int) {evaluate}; // list-initialization
    double result, myArr[2] = {4, 4.5};
    result = evaluate(myArr, 2); // call the function by name
    result = (*pf)(myArr, 2);    // call the function by pointer
    result = *pf(myArr, 2);   // error. () has higher precedence than *.  
    ```

  - C++ (not C) allows you to invoke the function in a neater way, in which the pointer `pf` acts as if it were a function name.

    ```C++
    result = (*pf)(myArr, 2);    // ordinary way
    result = pf(myArr, 2);       // neater
    ```

    The ordinary way is uglier, but it provides a strong visual reminder that the code is using a function pointer.

    > Q: How can `pf` and `(*pf)` be equivalent when used to invoke a function?
    >
    > A: One school of thought maintains that because `pf` is a pointer to a function, `*pf` is a function; hence, you should use `(*pf)` as a function call. Another school of thought maintains that because the name of a function acts like a pointer to that function (that is why the name is used to represent the function address), a pointer to that function should in turn act like the name of a function; so you should use `pf` as a function call. C++ takes the compromise view that both forms are correct, or at least can be allowed, even though they are logically inconsistent with each other. 
    >
    > *"The ability to hold views that are not logically self-consistent is a hallmark of the human mental process"* :)

  - Little trick:  the signatures of these three functions are the same, and can be used interchangeably.

    ```c++
    const double *f1(const double ar[], int n);
    const double *f2(const double [], int);
    const double *f3(const double *, int);               
    ```

  - Little trick: array of pointer-to-functions.

    ```c++
    const double *(*p[3])(const double *, int) = {f1, f2, f3}; // ok
    auto p[3] = {f1, f2, f3};   // NOT allowed at least in C++11.
    ```

    Be a little playful - how about a pointer-to-array-of-pointer-to-functions? First, you may very well use `auto`. But what if we want to do it ourselves?

    - Recall the algorithm set forth in 4.15. This variable `g` is essentially a pointer-to-array-of-pointer (pap). Therefore the algorithm yields `*(*g)[X]`. 

    - The tricky part is to add the lowest-level type. Using the example above, the final statement should be

      ```C++
      const double *(*(*g)[3])(const double *, int) = &pa;
      // the parenthes around *(*g)[3] is needed due to operator
      // precedence law. Beacause it needs to be saparated from (...)
      // after it and the '... *' ahead of it.
      ```

### 4.17 Simplifying with `typedef`

- Apart from `auto`, `typedef` is also a useful tool to simplify declarations by setting type aliases. It also makes the program easier to understand. The following snippets demonstrate its usage.

  ```C++
  // original:
  double i = 1;
  // becomes:
  typedef double DB;
  DB i = 1;
  ```

  ```C++
  // original:
  struct MP3 {std::string vendor; float price;};
  MP3 myMP3;
  // becomes (er... seems more complicated):
  typedef struct {std::string vendor; float price;} MP3;
  MP3 myMP3;
  ```

  ```C++
  // original:
  short double arr[10];
  // becomes:
  typedef short double DBArr[10];
  DBArr arr;
  ```

  ```C++
  int fun1(int, int *); int fun2(int, int *);
  // original:
  int (*pf1)(int, int *) = fun1, (*pf2)(int, int *) = fun2;
  int (*pfArr[2])(int, int *) = {fun1, fun2};
  int (*(*g)[2])(int, int *) = &pfArr;
  // becomes:
  typedef int (*pFun)(int, int *);
  pFun pf1 = fun1, pf2 = fun2;
  pFun pfArr[2] = {fun1, fun2};
  pFun (*g)[2] = &pfArr;
  ```

  The rule of declaring a type alias is straightforward: (1) declare a variable of that type; (2) insert the keyword `typedef` ahead of it; (3) use the variable name as the new type name.

  $$\square$$

