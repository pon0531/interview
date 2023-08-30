# C TEST
## 名詞解釋
<details>
<summary>Q1: Explain “#error”</summary>

可以用來強制中斷編譯器並且輸出訊息，這是寫給編譯器看的，通常搭配 Cflag,CXXflag 使用，在編譯時中斷錯誤的流程。#error預處理程序指令用於指示錯誤。如果找到#error指令編譯器將發出致命錯誤，並且跳過進一步的編譯過程。
以下是在 glibc 中可見的範例
<pre><code>#if !defined(__cplusplus)
#error C++ compiler required.
#endif
</code></pre>
</details>

<details>
<summary>Q2: Explain “struct” and “union”</summary>
struct =
在 C 上
自訂一種資料型態裡面可以包含多種資料型態，並且會按照程式中定義時擺放的順序在內存中排序，按照宣告的順序擺放。編譯器會自動為struct的成員分配空間。
在 C++ 上 struct 被擴展到可以內建 method ，跟 class 的意義非常類似，只是繼承與存取權限預設皆為 public 相反的 class 預設為 private，以及 class 有 template 的擴充 struct 沒有

union 一種特殊的所有內容都從內存開頭開始存取的資料型態，並且他的大小由內部最大的那個資料型態來定義。會用同一個存儲空間，只能存儲最後一個成員訊息。只給其中一個成員給值而使用其他值就會壞掉

補充：
typedef:保留字。可以為資料型態建立別名。ex: typedef unsigned uint; typedef int (*CMP)(int,int)
enum: C語言提供自定型態為enum，是一組由識別字所代表的整數常數。除非特別指定，不然都是由0開始，接下來遞增1。ex: enum week{Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday};

</details>

<details>
<summary>Q3: Explain “volatile”. Can we use “const” and “volatile” in the same variable? Can we use “volatile” in a pointer?</summary>
volatile 加在變數前面。是在指示編譯器每次對該變數進行存取時都要「立即更新」，不應該對其做任何最佳化。
因為有時候變數會先放在CPU register中沒被寫到mem裡，若有其他人要透過他反應便會壞掉。用volatile就可確保變數可以馬上反應
可以跟 const 合用，假設你要(監看)一個 CPU flag 時就是不錯的時機。


Extend

一個定義為 volatile 的變量是說這變量可能會被意想不到地改變（尤其在嵌入式系統），因此編譯器不會去假設這個變量的值了。精確地說就是，優化器不會將其優化，而是在用到這個變量時必須每次都重新讀取這個變量的值，不是使用保存在暫存器裡的備份。
下面是 volatile 變量的幾個例子︰
1. 並行設備的硬體暫存器 (如︰狀態暫存器)
2. 一個中斷服務子程序中會訪問到的非自動變數(Non-automatic variables)
3. 多執行緒中共享的變數
是的。一個例子是只讀的狀態寄存器（不應該被程式修改，但有可能被硬體修改，比如interrupt routine）。它是 volatile 因為它可能被意想不到地改變。它是 const 因為程序不應該試圖去修改它。
是的。比如當一個中斷服務的子程序修改一個指向buffer的pointer時。
</details>

<details>
<summary>Q4: What is “const” ? and what is the meaning below</summary>

const int a;

int const a;

const int *a;

int * const a;

int const * a const;

const 代表只可讀不可改。
1. 一個常數型整數
2. 同 1，一個常數型整數
3. 一個指向常數型整數的指標 (整型數不可修改，但指標可以)
4. 一個指向整數的常數型指標 (指標指向的整數可以修改，但指標不可修改)
5. 一個指向常數型整數的常數型指標 (指標指向的整數不可修改，同時指標也不可修改)
</details>

<details>
<summary>Q5: What is the difference between “Inline Function” and “Macro” ?</summary>
Macro是在預處理時直接單純的文字替換，inline function是在compile階段時，直接取代function。比較下面兩個例子：

inline寫法：
<pre><code>
inline int square(int x) {
    return x * x;
}
</code></pre>
output: SQUARE(3 + 2) → (3 + 2) * (3 + 2) = 25

Macro寫法：
<pre><code>
#define SQUARE(x) (x * x)
</code></pre>
output: SQUARE(3 + 2) → 3 + 2 * 3 + 2 = 11
</details>

<details>
<summary>Q6: Explain “static” ?</summary>
1. static 出現在變數前，且該變數宣告於函式中 (C/C++)：
局部變數加上 static 修飾後便不會因為離開可視範圍而消失。
2. static 出現在變數前，且該變數不是宣告於函式中(C/C++)：
讓變數只限定在該檔案內，而不是整個程式中（解決編譯時連結多個檔案造成相同變數名衝突）。
3. static 出現在類別的成員變數前 (C++ only)：
表示該變數不屬於某個類別實例，他屬於這個類別，所有以此類別生成出來的實例都共用這個變數。
4. static 出現在類別的成員函式之前 (C++ only)：
表示該函式不屬於某個類別實例，他屬於這個類別，所有以此類別生成出來的實例都共用這個函式（即便我們沒有產生實例出來，我們也隨時可以取用這個函式）

[Reference](https://medium.com/@alan81920/c-c-%E4%B8%AD%E7%9A%84-static-extern-%E7%9A%84%E8%AE%8A%E6%95%B8-9b42d000688f)
</details>

<details>
<summary>Q7: What is stack and heap when talking about memory ?</summary>
Stack: allocated by compiler, store function parameters and local variables
    
1. very fast access
2. don't have to explicitly de-allocate variables
3. space is managed efficiently by CPU, memory will not become fragmented
4. local variables only
5. limit on stack size (OS-dependent)
6. variables cannot be resized

   
Heap: allocated by programmer
1. variables can be accessed globally
2. no limit on memory size
3. (relatively) slower access
4. no guaranteed efficient use of space, memory may become fragmented
5. you must manage memory (you're in charge of allocating and freeing variables) or memory leakage will happen
6. variables can be resized using realloc()

   
Static: store global variable and static variable (initialized when process starts)
Literal Constant: store comments (text)
</details>

<details>
<summary>Q8: Explain “thread” and “process”, and what is the difference ? </summary>
Process: an executing instance of an program. Process takes more time to terminate and it is isolated means it does not share memory with any other process.

Thread: a path of execution within a process. Thread takes less time to terminate as compared to process and like process threads do not isolate. They share memory with other threads.


The typical difference is that threads (of the same process) run in a shared memory space, while processes run in separate memory spaces.
[Reference](https://pediaa.com/difference-between-process-and-thread/)
</details>

## 程式題
<details>
<summary>Q1: bitwise operation</summary>
unsigned long v1 = 0x 00001111;
  
unsigned long v2 = 0x 00001202;

unsigned long v;

v = v1&(~v2);

v = v | v2;

ask: the value of v?

ANS:0x00001313

</details>

<details>
<summary>Q2: pointer</summary>
int a[5] ={1,2,3,4,5};
  
int *p = (int *)(&a+1);

ask: the value of *(a+1), (*p-1)?

ANS:

(&a+1) = undefine

*(a+1) = 2

(*p-1) = 0

(這邊答案似乎有誤, (*p-1)應為undefined
</details>
<details>
<summary>Q3: bit operation</summary>
  
a) set the specific bit
  
b) clear the specific bit

c) inverse the specific bit (0->1; 1->0)

Ans:

#define setBit(x,n) (x|= (1<<n))
                              
#define clearBit(x,n) (x&= ~(1<<n))
                              
#define inverseBit(x,n) (x^= (1<<n))

</details>

<details>
<summary>Q4: Define pointer</summary>

1. An integer
2. A pointer to an integer
3. A pointer to a pointer to an integer
4. An array of 10 integers
5. An array of 10 pointers to integers
6. A pointer to an array of 10 integers
7. A pointer to a function that takes an integer as an argument and returns an integer
8. An array of 10 pointers to integers



An array of ten pointers to functions that take an integer argument and return an integer

1. int a
2. int *a
3. int **a
4. int a[10]
5. int *a[10]
6. int (*a)[10]
7. int (*a)(int)
8. int (*a[10])(int)
</details>

<details>
<summary>Q5: What is the content of array a ?</summary>
int a[] = {6, 7, 8, 9, 10};

int *p = a;

*(p++) += 123;

*(++p) += 123;

ANS: {129, 7, 131, 9, 10}

</details>

# OS TEST
## 名詞解釋
<details>
<summary>Q1: interrupt</summary>

</details>
