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

## Priority Queues: Introduction

- 優先權佇列(Priority Queues)
    - 在眾多語言中都有實作的一項資料結構，例如：C++、Java、Python。
    - 借用組合的觀念能夠將priority queue中的資料內容存放於完整二元樹(Complete Binary Tree)中，最終只會以一個簡單陣列的形式表示。
    - 在時間與空間的使用上都相當有效率。
    - 只會需要用上少量行數的程式碼。
    - 優先權佇列是標準佇列一般化的一種形式。
    - 其支援兩項主要操作(Recalling)：
        - PushBack\(e\)
        - PopFront()
- 非正式定義(概念)：
    - 在優先權佇列中沒有所謂前方或後方的分別，而是以優先權這樣的概念標記佇列中的各個元素。
    - 當新的資料或元素出現時，僅使用Insert方法將元素任意置入Queue中。
    - 然而當需要處理或提取Queue中的元素時，則使用ExtractMax找出其中優先權最高者。

- Priority Queue的典型使用範例
    - Scheduling Jobs
        - 若想根據優先順序，降序地處理手邊的工作，且正在處理的工作還未完成，可能就有新工作指派來。
        - 新指派的工作以Insert(job)來加入原有的工作佇列中(通常稱為Pool)。
        - 為了快速從工作佇列中(Pool)選取適當的下一份工作處理，便必須參考該工作在佇列中的優先順序級。

- 優先權佇列(Priority Queue)的正式定義
    - 為一抽象資料結構，支援下列主要的操作行為：
        - Insert\(p\): add a new element with priority.
        - ExtractMax(): extracts an element with maximum priority.
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
        - 一種針對字串或檔案的最佳化編碼方式，其特色在於無前綴(prefix-free)，例如MP3的檔案格式編碼演算法。
    - **Heap sort**: sorting a given sequence.
        - In-place方法排序，不須額外記憶體空間(No extra memory)。
    - <font color=green>上述幾個演算法在本門課程都會再進一步詳加介紹，尤其Heap sort接下來四段影片便會提及。</font>

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

## 二元樹(Binary Tree)

- 二元堆積(Binary Heap)
    - 最常用來實作優先權佇列的資料結構。
    - 二元最大堆積(Bnary Max-Heap)
        - 為一二元樹
        - 每個節點都擁有0、1或2個子節點。
        - 每個父節點都必須大於/等於其所擁有的子節點。換句話說任一edge的上端節點必須大於等於下端節點。
        - 合法的Binary Max-Heap<br>![PriorityQ_BMH](https://i.imgur.com/y9ZUHwC.png)
        - 非Binary Max-Heap<br>![PriorityQ_notBMH](https://i.imgur.com/GWBf4yt.png)

- 樹高(Tree Height)的延伸探討
    - 前面課程：Longest path from the root to a leaf.
    - 自此開始：Edges on the longest path from the root to a leaf.
    - Both definitions of height are used in practice frequently, so it is always a matter of context. If there is no definition in the text, you should look at the examples discussed to understand which of the two definitions is used.

## 基本操作

- GetMax：根節點一定是整棵樹的最大值。

- Insert：先將節點安置在任一個仍未塞滿子節點的樹葉上，如此一來很有可能Binary Max-Heap的結構因此被破壞(違反)，所以必須經過調整使其再次保有性質。
    - SiftUp：單純將節點置入Tree中違反了Binary Max-Heap的基本性質，所以採用交換的方式讓該節點逐次往上提升至合理的高度。
    - Running time: $\mathcal{O}(tree~height)$

- ExtractMax：雖然已經知道根結點即為整棵樹的最大值，但直接取走會破壞樹的結構，所以取走後必須要用適當的方法整理結構讓樹在結構改變最小的情況下，重新符合Binary Max-Heap的性質。
    - SiftDown：首先取走root之後，會隨意選擇一樹葉取代根結點原本的位置成為新的根，但如此一來必定違反Binary Max-Heap的結構性質(因為原本為樹葉的節點並定位於樹中相對底部的區域，也表示其鍵值必定相對較小)。自此開始，將根結點與其左右子節點，比他大的節點中較大者交換位置，直到根節點下移至適當的位置。
    - Running time: $\mathcal{O}(tree~height)$

- ChangePriority：若改變priority的節點是提高priority的話，運行SiftUp，反之則運行SiftDown
    - Running time: $\mathcal{O}(tree~height)$

- Remove：先將欲移除的節點其priority改為$\infty$，然後運行SiftUp至根節點的位置，再做ExtractMax。
    - Running time: $\mathcal{O}(tree~height)$

- 結論
    - GetMax需要$\mathcal{O}(1)$，其他則皆花費$\mathcal{O}(tree~height)$
    - 我們期望樹的結構越單純越好(影片用shallow描述，粗淺？)。

## 完整二元樹(Complete Binary Tree)

- 動機及目的
    - 在表示Binary Max-Heap，我們希望其結構盡可能淺略(Shallow)，也就是說樹高能多小就多小。
    - 每個level的節點數量都要能填滿，才往下一個level增加。
    - 基於上述目標，於是便能考慮用Complete Binary Tree的概念實行。

- Binary Tree called Complete
    - 當一二元樹其每一個level都達到最大節點數量除了最後一個level，且最後一個level的所有節點滿足由左至右填滿的形式，便稱其為complete。
    - Binary Tree(Complete)<br>![PriorityQ_BTcomplete_1](https://i.imgur.com/fLGANhv.png)<br>![PriorityQ_BTcomplete_2](https://i.imgur.com/fXBpQso.png)<br>![PriorityQ_BTcomplete_3](https://i.imgur.com/Ctjhlrr.png)
    - Binary Tree(Not Complete)<br>![PriorityQ_BTnc_1](https://i.imgur.com/FZKhGzM.png)<br>![PriorityQ_BTnc_2](https://i.imgur.com/gXJtxbh.png)<br>![PriorityQ_BTnc_3](https://i.imgur.com/pmCjRu4.png)

- 樹高較小的優點其一
    - ***Lemma***: A complete binary tree with n nodes has height at most $\mathcal{O}(log(n))$
    - 證明：
        - 若為了使Binary Tree具備Full性質，而用$n'$個節點填滿至二元樹的第$l$個level，$n' \ge n$
        - $n' \le 2n$
            - $n=2^0+2^1+...+2^{l-1}+k$
            - $n+1=2^l+k\implies 2^l=n-k+1$ ($k\ge 1$)
            - $n'=n+2^l-k$ Q.E.D.
        - $\therefore n' = 2^l-1 \rightarrow l = log_{2}(n'+1) = log_{2}(2n+1)=\mathcal{O}(log(n))$
    - 看不懂上面寫尛也聽不懂影片公尛，自己證證看…
        - $n'$表示高度為$l$的Full Binary Tree其所含有的節點總數，也就是$2^l-1$
        - $n$表示高度為$l$的Complete Binary Tree其可能含有的節點總數，可知：
            - $n \le n'$
            - $n \gt 2^{l-1}-1$，換句話說 $n \ge 2^{l-1}-1+1=2^{l-1}\therefore 2n \ge 2^{l-1} \times 2 = 2^l$
            - $\because n' = 2^l-1 \lt 2^l = 2n$
            - 可得 $l=log_{2}(n'+1)\le log_{2}(2n+1)=\mathcal{O}(log(n))$

- 樹高較小的優點其二
    - Store as Array
    - 為了讓樹高能多低就多低，我們選用了Complete Binary Tree做為Binary Max Heap的實作方法，於是使得用Array存取該類資料結構變得十分容易。
    - Example<br>![PriorityQ_MHeap2Array_tree](https://i.imgur.com/VjvfqkX.png)
        - parent(i) = $\lfloor \frac{i}{2} \rfloor$
        - leftchild(i) = $2i$
        - rightchild(i) = $2i+1$
    ![PriorityQ_MHeap2Array_array](https://i.imgur.com/M4UHoIl.png)
    
- 代價
    - What do we pay for these advantages?
        - Keep the tree complete at any moment.
    - Which binary heap operation modify the shape of the tree?
        - Insert
        - ExtractMax
        - (Remove changes the shape by calling ExtractMax)

- 保持Complete Binary Tree的方法
    - Insert
        - 當新增新元素的時候不再置於任意leaf的空子結點的位置，而是一致地擺放在leftmost vacant position in the last level，如下圖示。<br>![PriorityQ_KeepComplete_insert](https://i.imgur.com/sbC9d4c.png)
    - ExtractMax
        - 當取出最大值之後，不再取任意leaf取代空下的root node，而是一致地取the last leaf做為取代root node的節點，如下圖示。<br>![PriorityQ_KeepComplete_eMAX](https://i.imgur.com/1HMjnyU.png)

## 虛擬碼

- General Setting
    - **maxSize**: the maximum number of elements in the heap.
    - **size**: the size of the heap.
    - **H[1, ..., maxSize]**: an array of length **maxSize** where the heap occupies the first **size** elements.
    - Example<br>![PriorityQ_HeapEXP](https://i.imgur.com/vdlnBZ1.png)

- 基本操作
    - Parent(i)
        - return $\lfloor \frac{i}{2} \rfloor$
    - LeftChild(i)
        - return $2i$
    - RightChild(i)
        - return $2i+1$
    - SiftUp(i)
    ```=
    while i > 1 and H[Parent(i)] < H[i]:
        swap H[Parent(i)] and H[i]
        i <-- Parent(i)
    ```
    - SiftDown(i)
    ```=
    maxIndex <-- i
    l <-- LeftChild(i)
    if l <= size and H[l] > H[maxIndex]:
        maxIndex <-- l
    r <-- RightChild(i)
    if r <= size and H[r] > H[maxIndex]:
        maxIndex <-- r
    if i ~= maxIndex:
        swap H[i] and H[maxIndex]
        SiftDown(maxIndex)
    ```
    - Insert\(p\)
    ```=
    if size = maxSize:
        return ERROR
    size <-- size + 1
    H[size] <-- p
    SiftUp(size)
    ```
    - ExtractMax()
    ```=
    result <-- H[1]
    H[1] <-- H[size]
    size <-- size - 1
    SiftDown(1)
    return result
    ```
    - Remove(i)
    ```=
    H[i] <-- inf
    SiftUp(i)
    ExtractMax()
    ```
    - ChangePriority(i, p)
    ```=
    oldp <-- H[i]
    H[i] <-- p
    if p > oldp:
        SiftUp(i)
    else:
        SiftDown(i)
        
- 結論
    - 用Binary Max Heap實作Priority Queue能得到：
        - Fast: all operations work in time $\mathcal{O}(log(n))$(GetMax even works in $\mathcal{O}(1)$).
        - Space efficient: store priorities in an array; parent-child connections are not stored, but are computed on the fly.
        - Easy to implement: all operations are implemented in just a few lines of code.