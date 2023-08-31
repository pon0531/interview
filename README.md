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

<details>
<summary>Q9: What is the difference between variable declaration and definition ?</summary>
A declaration provides basic attributes of a symbol: its type and name. 
    
A definition provides all of the details of that symbol.
</details>

<details>
<summary>Q10: Explain lvalue and rvalue.</summary>
value 指 等號的左值
rvalue 指 等號的右值

lvalue 是一個物件的保存在單一運算式之外的物件
rvalue 是一個物件在某個時間單位的結果

++c 是lvalue
c++ 是rvalue
</details>
<details>
<summary>Q11: 以下Unix命令分別是做什麼用的?</summary>

    1. chmod: 更改存取權限

    2. who: 显示当前登录系统的用户

    3. which: 查看可执行文件的位置

    4. echo: 顯示訊息

    5. whereis - 查看文件的位置

    6. locate - 配合数据库查看文件位置
  
    7. find - 实际搜寻硬盘查询文件名称

</details>

<details>
<summary>Q12: sizeof 不是函式</summary>
    
sizeof 不是函式，不會在執行時計算變數或型別的值，而是在編譯時，所有的 sizeof 都被具體的值替換。

<pre><code>
double f(){
  printf("Come into Double f...\n");
  return 0.0;
}

int main(){
  int var=0;
  int size=sizeof(var++);  //sizeof(var++)在編譯時會直接被替換++不會執行
  printf("var = %d, size = %d\n", var, size);
  size = sizeof(f());
  printf("size = %d\n", size);
  return 0;
}

</code></pre>

##output
var = 0, size = 4
size = 8


常見考題

    64bit
    sizeof(string)          = 8
    sizeof(char)         = 1
    sizeof(p)            = 8
    sizeof(short)        = 2
    sizeof(int)          = 4
    sizeof(long)         = 8
    sizeof(long long)    = 8
    sizeof(size_t)       = 8
    sizeof(double)       = 8
    sizeof(long double)  = 16
    32bit
    sizeof(string)          = 4
    sizeof(char)         = 1
    sizeof(p)            = 4  //指標
    sizeof(short)        = 2
    sizeof(int)          = 4  //怕因環境影響程式,絕大多數64,32的編譯器是一樣大
    sizeof(long)         = 4      
    sizeof(long long)    = 8
    sizeof(size_t)       = 4
    sizeof(double)       = 8
    sizeof(long double)  = 12    //看作long+double = 4 + 8 =12
    
</details>

## 程式題
<details>
<summary>Q1: bitwise operation</summary>
unsigned long v1 = 0x 00001111;
  
unsigned long v2 = 0x 00001202;

unsigned long v;

v = v1&(~v2); ......1

v = v | v2;.........2

ask: the value of v?

記得先轉成2進位再做 bit operation

ANS. (1) v = 0x00000111 (2) v = 0x00001313

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

Modify by define:

#define setBit(x,n) (x|= (1<<n))
                              
#define clearBit(x,n) (x&= ~(1<<n))
                              
#define inverseBit(x,n) (x^= (1<<n))

Modify by function:

1. void setBit(int &a, b) { a |= (0x1<<b); }

2. void clearBit3(int &a, b) { a &= ~(0x1<<b); }

3. void inverseBit(int &a, b) { a ^= (0x1<<b); }
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

<details>
<summary>Q6: Is the program below correct ?</summary>
unsigned int zero = 0;

unsigned int compzero = 0xFFFF; /*1’s complement of zero */

對于一個整數型不是16位元的處理器為說，上面的程式碼是不正確的。應編寫如下︰ unsigned int compzero = ~0;
</details>

<details>
<summary>Q7: What is the output of the following program ?</summary>
<pre><code>
void foo(void) {
unsigned int a = 6;
    int b = -20;
    (a + b > 6) ? puts(“> 6”) : puts(“<= 6”);
}
</code></pre>

當表達式中存在有符號類型和無符號類型（unsigned）時所有的操作數都自動轉換為無符號類型。因此-20變成了一個非常大的正整數，所以該表達式計算出的結果大于6。
</details>
<details>
<summary>Q8: The faster way to an integer multiply by 7 ?</summary>
i = (i << 2) + (i << 1) + (i << 0)

i = (i << 3)-i
</details>

<details>
<summary>Q9: Reverse a string</summary>
<pre><code>
void reverseStr(string& str) {
    int n = str.length();
    for (int i = 0; i < n / 2; i++)
        swap(str[i], str[n - i - 1]);
}
</code>
</pre>
</details>

<details>
<summary>Q10: Write functions isUpper() and toUpper()</summary>
<pre><code>
bool isUpper(int ch) {
    return (ch >= 'A' && ch <= 'Z');
}
int toUpper(int ch) { 
    if (isUpper(ch))
        return ch;
    else 
        return (ch + 'A' - 'a');
}
</code></pre>
</details>

<details>
<summary>Q11: What is the meaning of int(*a)(int) and int(*a[10])(int) ?</summary>
A pointer to a function that takes an integer argument and returns an integer.

An array of 10 pointers to functions that take an integer argument and return an integer.
</details>

<details>
<summary>Q12: Re-write void(*(*papf)[3])(char *);</summary>
Re-write void(*(*papf)[3])(char *);
    
typedef__________;
    
pf(*papf)[3];

Ans:

void (*pf) (char *)

-> typedef void(*pf)(char *);
</details>
<details>
<summary>Q13: write a code that check the input is a multiple of 3 or not without using division or mod</summary>
<pre><code>
//這個是剛好用 3 的特性來解
int divide3(int a){
    int ans = 0;
    while(a){
        ans += a&1;
        a>>=1;
        ans -= a&1;
        a>>=1;
    }
    return !(ans);
}

///通用算法(This can be used for any number)
/*
set Number = abcde(2進位)
abcde = abc<<2 + de
      = abc*(4) + de
      = abc(*3) + (abc + de)
任何數字都可以用類似的方式拆出用 &跟<<完成的判斷 
*/    
      
int isMult3(unsigned int n)
{
    if ( n == 0 || n == 3 )    
        return 1;
    if ( n < 3 ) 
        return 0;
    n = (n >> 2) + (n & 3);
    /* Recursive call till you get nor a special terminate condition */
    return(isMult3(n));
}
</code></pre>
</details>
<details>
<summary>Q14: pointer 萬年題</summary>
<pre><code>
int a[5]={1,2,3,4,5};
int *p=a;
*(p++)+=123;
*(++p)+=123;
</code></code></pre>

請問a陣列的每個值為何?

Ans:
萬年面試題目

124 2 126 4 5
</details>

<details>
<summary>Q15: function pointer</summary>
<pre><code>
extern void func1(void);
extern void func2(void);
extern void func3(void);
extern void func4(void);
extern void func5(void);

void main(int n)
{
   if n==1 execute func1;
   if n==2 execute func2;
   if n==3 execute func3;
   if n==4 execute func4;
   if n==5 execute func5;
}
</code></pre>

保證 n 一定是上面五個數字之一

不能用if 和 switch case , 請用你認為最快的方法實作main
<pre><code>
void (*fp[5]) ();
fp[0] = func1;
fp[1] = func2;
fp[2] = func3;
fp[3] = func4;
fp[4] = func5;
fp[n-1]();
</code></pre>
</details>

<details>
<summary>Q6: 寫一個“標準”巨集MIN ，這個巨集輸入兩個參數並返回較小的一個</summary>
#define min(a,b) ((a)<(b)?(a):(b))
</details>

<details>
<summary>Q17: 寫出一個function判斷輸入的數是2的次方</summary>
<pre><code>
inline int define2N(int n){
    return (!(n&n-1) && n!=0)
}
</code></pre>
</details>
<details>
<summary>Q18: Write a MACRO to calculate the square of integer a.</summary>
#define sqare(x) ((x)*(x))
</details>

<details>
<summary>Q19: 使用bitwise operation實作swap function</summary>
<pre><code>
void swap(int *a, int *b)
{
    *a = *a ^ *b;
    *b = *a ^ *b;
    *a = *a ^ *b;
}
</code></pre>
</details>

<details>
<summary>Q20: 用一行程式碼判斷是否為2的冪次方</summary>

Ans: return N>0 && (N&(N-1)) == 0
</details>

<details>
<summary>Q21:  the content of array a?</summary>
Int a[] = {6, 7, 8, 9, 10};

Int *p=a;

*(p++)+=123;

*(++p)+=123;
Ans:a[] = {129, 7, 131, 9, 10} (這題考運算子的優先順序)
</details>
<details>
<summary>Q22: Output the result</summary>
<pre><code>
int fun(int x)
{
    int count = 0;
    while(x){
        count++;
        x = x & (x-1)
    }
    return count
}

fun(456) + fun(123) + fun(789) = ? (15)
</code></pre>
Ans: 4 + 6 + 5 = 15 (計算輸入進來的數字，其二進位表示有幾個1)
</details>

<details>
<summary>Q23: Output the result</summary>
<pre><code>
#define INC(x) x*=2; x+=1

int main()
{
    int i, j;
    for (i = 0, j = 1; i < 5; i++)
    INC(j);
    printf("j = %d\n", j);
}
</code></pre>
求J輸出值是多少?
Ans: 2 (玄機在for迴圈沒有括號)
</details>

# OS TEST
## 名詞解釋
<details>
<summary>Q1: interrupt</summary>

</details>
