# Data Structures Week 3

## Objective
    - Describe how heaps and priority queues work
    - Describe how disjoint set union data structure works
    - Analyze the running time of operations with heaps
    - List the heuristics that speedup disjoint set union
    - Apply priority queues to schedule jobs on processors
    - Apply disjoint set union to merge tables in a database

- 學習目標
    - 描述堆積(Heap)與優先權佇列(Priority Queue)如何運作。
    - 描述併查集(Disjoint Set Union)這類資料結構。
    - 藉堆積分析操作的運行時間。
    - 表列加速併查集使用上的啟發方法。(???)
    - 將優先權佇列應用於處理器上的工作時間安排。
    - 將併查集應用於資料庫的表格合併(Merge)中。

## Introduction

- 優先權佇列(Priority Queues)
    - 在眾多語言中都有實作的一項資料結構，例如：C++、Java、Python。
    - 借用組合的觀念能夠將priority queue中的資料內容存放於完美二元樹(Complete Binary Tree)中，最終只會以一個簡單陣列的形式表示。
    - 在時間與空間的使用上都相當有效率。
    - 只會需要用上少量行數的程式碼。
    - 優先權佇列是標準佇列一般化的一種形式。
    - 其支援兩項主要操作(Recalling)：
        - PushBack(e)
        - PopFront()
        - 
- 非正式定義(概念)：
    - 在優先權佇列中沒有所謂前方或後方的分別，而是以優先權這樣的概念標記佇列中的各個元素。
    - 當新的資料或元素出現時，僅使用Insert方法將元素任意置入Queue中。
    - 然而當需要處理或提取Queue中的元素時，則使用ExtractMax找出其中優先權最高者。

- Priority Queue的典型使用範例
    - Scheduling Jobs
        - 若想根據優先順序，降續地處理手邊的工作，且正在處理的工作還未完成，可能就有新工作指派來。
        - 新指派的工作以Insert(job)來加入原有的工作佇列中(通常稱為Pool)。
        - 為了快速從工作佇列中(Pool)選取適當的下一份工作處理，便必須參考該工作在佇列中的優先順序級。

- 優先權佇列(Priority Queue)的正式定義
    - 為一抽象資料結構，支援下列主要的操作行為：
        - Insert(p): add a new element with priority.
        - ExtractMac(): extracts an element with maximum priority.
    - 其他額外操作(可具備但非必要)：
        - Remove($it$): removes an element pointed by an iterator $it$.
        - GetMax(): returns an element with maximum priority(without changing the set of element).
        - ChangePriority($it$, p): changes the priority of an element pointed by $it$ to p.
    - 範例
        - What will be the output of the following program?<br>
        ```=
        create an empty priority queue
        Insert(18)
        Insert(12)
        Insert(14)
        print(ExtractMax())
        print(ExtractMax())
        Insert(15)
        print(ExtractMax())
        Insert(10)
        print(ExtractMax())
        print(ExtractMax())
        ```
        As an answer, provide a sequence of integers separated by spaces.
        ans: 答案見留言
        
- 會使用到優先權佇列的常見演算法
    - **Dijsktra's algorithm**: finding a shortest path in a graph.
    - **Prim's algorithm**: constructing a minimum spanning tree of a graph.
        - 假設有若干台電腦，並想將它們互相連接形成適當的網路，除此之外希望能達到最低成本，例如以連接的線路數量、線路使用的總長，或每單位長度線路的花費作為成本的基本單位。
    - **Huffman's algorithm**: constructing an optimum prefix-free encoding of a string.
        - 一種針對字串或檔案的最佳化編碼方式，其特色在於前綴不重複(prefix-free)，例如MP3的檔案格式編碼演算法。
    - **Heap sort**: sorting a given sequence.
        - In-place方法排序，不須額外記憶體空間(No extra memory)。
    - <font color=green>上述幾個演算法在本門課程都會再進一步詳加介紹，尤其Heap sort接下來第四部影片便會提及。</font>

## 優先權佇列的初步實作(Naive)

- 從舉例切入
    - 未排序的陣列/連結串列<br>![PriorityQ_UnsortedALL](https://i.imgur.com/q9v7TBn.png)
        - 對於這樣的資料結構來說，要插入(新增)新的元素是非常容易的，拿Array來說，如果新元素"7"要放入，只要根據index將其置於Array的末端，所花費時間為$\mathcal{O}(1)$，對於Linked-List 來說也是差異不大的(Singly, Doubly皆然)。
        - 但反過來如果想要實行前一段影片提到，在Priority Queue中的ExtractMax()方法呢？很不幸地必須要Traverse整個資料結構才能夠找到優先權最大的元素。
        - Insert(e)
            - add e to the end
            - running time: $\mathcal{O}(1)$
        - ExtractMax()
            - scan the array/list
            - running time: $\mathcal{O}(n)$
    - 已排序的陣列<br>![PriorityQ_SortedArray](https://i.imgur.com/4jzT8eU.png)
        - ExtractMax()
            - extract the last element
            - running time: $\mathcal{O}(1)$
        - Insert(e)
            - 用binary search找到適合e的位置需要$\mathcal{O}(log(n))$，在插入e之前位移其後的所有元素必須要花費$\mathcal{O}(n)$，而實際insert元素e則是$\mathcal{O}(1)$。<!-- binary serach的時間為log(n)是因為每次將整個陣列減半，只取其中一半繼續比較尋找，直到剩下一個元素時就找到了，所以Input隨著搜尋次數來到k次而減少到剩下n/2^k，當input <= 1的時候達成目標，所以移項得 2^k <= n, k <= log(n) -->
            - running time: $\mathcal{O}(n)$

    - 已排序的連結串列(Doubly)<br>![PriorityQ_SortedDLL](https://i.imgur.com/s7K9daf.png)
        - ExtractMax()
            - extract the last element
            - running time: $\mathcal{O}(1)$
        - Insert(e)
            - 找到適合e的位置需要$\mathcal{O}(n)$，因為沒有index無法用binary search，insert元素e所需的時間為$\mathcal{O}(1)$
            - running time: $\mathcal{O}(n)$
    - 範例
        - Assume that you know in advance that in your application there will be <font color=blue>$n$</font> calls to **Insert** and <font color=blue>$\sqrt{n}$</font> calls to **ExtractMax**. Which of the following two implementations of the priority queue is preferable in this case?
            - Array
            - Sorted array
        ans: 答案見留言

- 結論

    || Insert | ExtractMax |
    | :-------- | :--------: | :--------: |
    | Unsorted Array/List | $\mathcal{O}(1)$ | $\mathcal{O}(n)$ |
    | Sorted Array/List | $\mathcal{O}(n)$ | $\mathcal{1}(n)$ |
    | <font color=red>Binary Heap</font> | <font color=red>$\mathcal{O}(log(n))$</font> | <font color=red>$\mathcal{O}(log(n))$</font> |

    - Binary Heap能夠實作Priority Queue並且達到上述的操作成效。