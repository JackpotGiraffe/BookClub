# Data Structures Week 1

## Objective
    - List the basic data structures.
    - Analyze operations with data structures.
    - Choose appropriate basic data structure for a task at hand.
    - Apply basic data structures in programming challenges.
    - Develop a program that simulates network packet processing.

- 學習目標
    - 表列基本資料結構。
    - 針對上述資料結構的操作行為分析。
    - 對於面臨的問題或工作選擇適當的基本資料結構解決問題。
    - 在程式撰寫與設計的挑戰過程中應用資料結構。
    - 開發用於模擬網路數據封包發送處理的程式。

## 矩陣(Arrays)

- Can be on a stack(堆疊), in a heap(堆積)

- 定義
    - 記憶體上的連續空間(區域)、一大(區)塊記憶體(One chunk of memory)。
    - 該連續空間被劃分為大小相等的若干個元素、元件。
    - 每個元素都依序被標上連續的正整數作為指標(index, 索引值)。<br>![Array_sample](https://i.imgur.com/5bsv37v.png)
    - 上圖為一矩陣範例，其大小(長度)為7，索引值自1標至7，在有些語言中索引值如上從1開始(one-based indexing)，有些語言則會從0開始(zero-based indexing)，甚至有些語言能讓使用者自行定義起始索引值。

- 特性
    - Constant-time($\mathcal{O}(1)$) access to any particular element in the array.
    - 包含讀(read) 、寫(write) 皆為$\mathcal{O}(1)$。
    - 從矩陣的記憶體位置著手切入該特性：
$$
addr_{i}=addr_{A}+size_{A}\times(i-addr_{first}), i:\text{The index of any element in array A}
$$
因為各個元素大小相等，所以能透過上式簡單完成乘法運算，取得記憶體位置的相關資訊。
    - One-based indexing 或 zero-based indexing 不影響數學式的精神與計算結果。

- 範例
    - Given an array whose:<br>address is 1000,<br>element size is 8<br>first index is 0<br>What is the address of the element at index 6?

    <ol>
        <li>48</li>
        <li>1048</li>
        <li>1040</li>
        <li>40</li>
        <li>1005</li>
        <li>1006</li>
    </ol>

    ans: 答案見留言

- 多維矩陣
    - 二維矩陣範例<br>![Array_2D_sample](https://i.imgur.com/0P1QbUg.png)<br>該二維矩陣同樣能透過前述計算式獲得記憶體位置資訊。
    - 唯一必須注意的是，一般通用邏輯為先 row 再 column。

- 藉由上述的記憶體位置說明矩陣實際在記憶體中配置的狀態<br>![Array_MemoryAllocation](https://i.imgur.com/9GRiISZ.png)<br>分為 Row-major 及 Column-major 兩種做法，以二維矩陣為例：
    - Row-major
        - 在同一列(Row)中遍歷完所有元素後，才移至下一列繼續操作。
        - 就記憶體上的連續性來看，row 相較於 column 來說索引值會較常保持不變。
    - Column-major
        - 在同一行(Column)中遍歷完所有元素後，才移至下一行繼續操作。
        - 就記憶體上的連續性來看，column 相較於 row 來說索引值會較常保持不變。

- 範例
    - Assume you have a three-dimensional array laid out in column-major order with the first element at indices (1, 1, 1). What are the indices of the next element in memory?

    <ol>
        <li>(2, 1, 1)</li>
        <li>(1, 1, 2)</li>
        <li>(1, 2, 1)</li>
    </ol>
        
    ans: 答案見留言

- 矩陣的基本操作
    - 見下圖說明<br>![Array_BasicOpTime](https://i.imgur.com/3hWGvly.png)
        - 對矩陣做增加或刪除元素的操作行為，皆不影響時間複雜度(Time Complexity)，僅討論操作時的起始位置(索引值)。
        - 若從矩陣起始位置著手操作，所需時間為$\mathcal{O}(n)$，因為除了主要目的以外，還必須將整個矩陣的其他元素依序位移。
        - 若從矩陣末端位置著手操作，所需時間僅為$\mathcal{O}(1)$，因為可以透過索引值直接取得操作對象的位置。
        - 從矩陣中央位置著手操作，因矩陣大小為$n$，所需時間為$n/2$，就操作時間複雜度而言仍為$\mathcal{O}(n)$。

- 結論
    - 矩陣(Arrays)：記憶體上由若干個佔據同樣大小空間的元素組成的連續區塊，且以連續正整數標記作為索引值。
    - 訪問任一元素的時間複雜度被bond在常數時間內。
    - 從矩陣末端做基本操作(新增、刪除)所需時間為常數$\mathcal{O}(1)$。
    - 從矩陣任意位置(非末端)做基本操作所需時間為線性$\mathcal{O}(n)$。

## 單向連結串列(Singly-Linked Lists)

- 定義
    - 其具有一個起始指標(Head Pointer) 指向串列中的首個元素。
    - 每個元素都具備一個指向下一個元素的指標以構成串連的結構。
    - 每個元素存放於節點 (Node)中，稱為 element，即為 Linked List 所存的 data，因 Linked List 中存放的 data 即為整數值，所以通常也能視為鍵值(Key)。
    - 節點具備另一空間存放指向下一個節點的指標 (Next Pointer)。
    - 串列末端的節點只有使用到存放元素的空間，存放 Next Pointer 的空間為空(Nil)。
    - 元素可為整數、浮點數…等基本資料型別，也可以是其他複雜資料結構(非典型用法，視其必要性使用)。
    - 示意圖<br>![LinkedList_SLL](https://i.imgur.com/2onq97f.png)


- 常用基本操作
    |Operations|Description|
    |:----|:----|
    |PushFront(Key)|add to front|
    |TopFront()|return front item|
    |PopFront()|remove front item|
    |PushBack(Key)|add to back|
    |TopBack()|return back item|
    |PopBack()|remove back item|
    |Find(Key)|is item in list(T/F)?|
    |Erase(Key)|remove item from list|
    |Empty()|empty list(T/F)?|
    
- 範例
    - You have an empty list, and then do the following operations:
    ```=
    PushBack(a)
    PushFront(b)
    PushBack(d)
    PushFront(c)
    PopBack()
    ```
    <ol>
        <li>c, b, a, d</li>
        <li>a, b, c</li>
        <li>c, b, a</li>
    </ol>
    
    ans: 答案見留言
    
- 演算法及操作時間分析
    - PushFront<br>![LinkedList_SLL_PushFront](https://i.imgur.com/Ekp2uY5.gif)
    - PopFront<br>![LinkedList_SLL_PopFront](https://i.imgur.com/MjeYrNp.gif)
    - PushBack(without tail pointer)<br>![LinkedList_SLL_PushBack(ntp)](https://i.imgur.com/AD7w21e.png)
    - PopBack(without tail pointer)<br>![LinkedList_SLL_PopBack(ntp)](https://i.imgur.com/CVebJKi.png)
    - PushBack(with tail pointer)<br>![LinkedList_SLL_PushBack(wtp)](https://i.imgur.com/14Gd0L9.gif)
    - PopBack(with tail pointer)<br>![LinkedList_SLL_PopBack(wtp)](https://i.imgur.com/EUwGHJ3.gif)
<!-- 2021.4.8 完成至此 -->

- 操作虛擬碼
    - PushFront(key)
    ```=
    node < new node
    node.key <-- key
    node.next <-- head
    head <-- node
    if tail = nil:
        tail <-- head
    ```
    - PopFront()
    ```=
    if head = nil:
        ERROR: empty list
    head <-- head.next
    if head = nil:
    tail <-- nil
    ```
    - PushBack(key)
    ```=
    node <-- new node
    node.key <-- key
    node.next = nil
    if tail = nil:
        head <-- tail <-- node
    else:
        tail.next <-- node
        tail <-- node
    ```
    - PopBack()
    ```=
    if head = nil:
        ERROR: empty list
    if head = tail:
        head <-- tail <-- nil
    else:
        p <-- head
        while: p.next.next ~= nil:
            p <-- p.next
        p.next <-- nil
        tail <-- p
    ```
    - AddAfter(node, key)
    ```=
    node2 <-- new node
    node2.key <-- key
    node2.next = node.next
    node.next = node2
    if tail = node:
        tail <-- node2
    ```

- 時間複雜度總結
    |Singly-Linked List Op|no tail|with tail|
    |----:|:----|:----|
    |PushFront(Key)|$\mathcal{O}(1)$||
    |TopFront()|$\mathcal{O}(1)$||
    |PopFront()|$\mathcal{O}(1)$||
    |PushBack(Key)|$\mathcal{O}(n)$|$\mathcal{O}(1)$|
    |TopBack()|$\mathcal{O}(n)$|$\mathcal{O}(1)$|
    |PopBack()|$\mathcal{O}(n)$||
    |Find(Key)|$\mathcal{O}(n)$||
    |Erase(Key)|$\mathcal{O}(n)$||
    |Empty()|$\mathcal{O}(1)$||
    |AddBefore(Node, Key)|$\mathcal{O}(n)$||
    |AddAfter(Node, Key)|$\mathcal{O}(1)$||

## 雙向連結串列(Doubly-Linked Lists)

- 定義
    - 節點中增加另一個存放pointer的空間。
    - 除了有指向下一個節點的next pointer以外，也有指向上一個節點的previous pointer(prev pointer)。
    - 首個節點不含prev pointer(不會指向head pointer)。
    - 尾端節點不含next pointer(不會指向tail pointer)。
    - 相對於head pointer便有對應的tail pointer指向尾端節點。
    - 示意圖<br>![LinkedList_DLL](https://i.imgur.com/6G5LMNA.png)

- 基本操作演算法
    - PushBack(key)<br>![DLL_PushBackAlg](https://i.imgur.com/Zz7Suc5.png)
    - PopBack(key)<br>![DLL_PopBackAlg](https://i.imgur.com/zCjhOqb.png)
    - AddAfter(node, key)![DLL_AddAfterAlg](https://i.imgur.com/dfaCfay.png)
    - AddBefore(node, key)![DLL_AddBeforeAlg](https://i.imgur.com/9I4anR9.png)

- 時間複雜度總結
    |Doubly-Linked List Op|no tail|with tail|
    |----:|:----|:----|
    |PushFront(Key)|$\mathcal{O}(1)$||
    |TopFront()|$\mathcal{O}(1)$||
    |PopFront()|$\mathcal{O}(1)$||
    |PushBack(Key)|$\mathcal{O}(n)$|$\mathcal{O}(1)$|
    |TopBack()|$\mathcal{O}(n)$|$\mathcal{O}(1)$|
    |PopBack()|<font color=red>$\mathcal{O}(1)$</font>||
    |Find(Key)|$\mathcal{O}(n)$||
    |Erase(Key)|$\mathcal{O}(n)$||
    |Empty()|$\mathcal{O}(1)$||
    |AddBefore(Node, Key)|<font color=red>$\mathcal{O}(1)$</font>||
    |AddAfter(Node, Key)|$\mathcal{O}(1)$||

- 連結串列總結(包含單向、雙向)
    - 從首個元素做插入或刪除所花時間為常數。
    - 若為雙向連結串列，或具備tail pointer的單向連結串列，從末端元素插入或刪除的時間可達常數。
    - 尋找隨機任一串列中的元素所需時間為$\mathcal{O}(n)$，無論單/雙向。
    - 串列中的元素不需要連續(配置於記憶體中)。
    - 對雙向連結串列中任意位置(非首/末)插入或刪除節點的時間花費為常數。

## 堆疊(Stacks)

- 定義
    - 為一抽象資料類型，並具備(能運行)以下幾項操作行為。
        - Push(Key): add key to the collection.
        - Top(): returns the most recently-added key.
        - Pop(): removes and returns the <font color=red>MOST</font> recently-added key
    - 操作行為遵守LIFO(Last in First out)原則。
        
- 括號匹配問題
    - Input: 一完整字串，全部由『(』, 『)』, 『[』, 『]』等字元符號(各類括號)組成。(甚至也可包含更多類型如『{』, 『}』, 『<』, 『>』…等等)
    - Output: Boolean，表示該輸入字串中的各類型括號是否有達到"平衡"狀態。
    - 判定是否平衡的範例
        - 平衡(Balanced)
            - " ( [ ] ) [ ] ( ) "
            - " ( ( ( [ ( [ ] ) ] ) ) ( ) ) "
        - 未平衡(Unbalanced)
            - " ( [ ] ] ( ) "
            - " ] [ "
    - 判定平衡的Pseudocode
        ```=
        IsBalanced(str)
        Stack stack
        for char in str:
            if char in ['(', '[']:
                stack.Push(char)
            else:
                if stack.Empty():
                    return False
                top <-- stack.Pop()
                if (top = '[' and char != ']') or (top = '(' and char != ')'):
                    return False
        return stack.Empty()
        ```
    - 範例
        - Given the unbalanced string "()([]", what character is on the top of the stack when the for loop is finished?<br>ans: 答案見留言

- 以矩陣(Array) 實作堆疊(Stack)
    - 必須先準備一固定大小的矩陣，也因此實作的堆疊空間大小受此矩陣限制。<br>![Stack_Array2Stack_pre](https://i.imgur.com/NEsCgGh.png)
    - 對矩陣實施一系列的操作以實作堆疊<br>![Stack_ops_Array](https://i.imgur.com/fWwEcZY.gif)
        - Push(a)
        - Push(b)
        - Top() $\rightarrow$ b
        - Push\(c\)
        - Pop() $\rightarrow$ c
        - Push(d)
        - Push(e)
        - Push(f)
        - Push(g) $\rightarrow$ ERROR
        - Empty() $\rightarrow$ False
        - Pop() $\rightarrow$ f
        - Pop() $\rightarrow$ e
        - Pop() $\rightarrow$ d
        - Pop() $\rightarrow$ b
        - Pop() $\rightarrow$ a
        - Empty() $\rightarrow$ True

- 以連結串列(Linked List) 實作堆疊(Stack)
    - 因為串列特性，只要記憶體仍有足夠空間，堆疊的大小就能夠持續延伸。初始狀態只需要有一串列的head pointer即可。<br>![Stack_LL2Stack_pre](https://i.imgur.com/xeBAPj2.png)
    - 對連結串列實施一系列的操作以實作堆疊<br>![Stack_ops_LL](https://i.imgur.com/GpA1J3D.gif)
        - Push(a)
        - Push(b)
        - Top() $\rightarrow$ b
        - Push\(c\)
        - Pop() $\rightarrow$ c
        - Push(d)
        - Push(e)
        - Push(f)
        - Empty() $\rightarrow$ False
        - Pop() $\rightarrow$ f
        - Pop() $\rightarrow$ e
        - Pop() $\rightarrow$ d
        - Pop() $\rightarrow$ b
        - Pop() $\rightarrow$ a
        - Empty() $\rightarrow$ True

- 結論
    - 無論使用矩陣或連結串列，都能夠實作堆疊。
    - 無論何種堆疊操作其所需時間皆為$\mathcal{O}(1)$，包含：Push、Pop、Top、Empty。
    - 堆疊也偶爾被稱做『後進先出佇列』(LIFO Queue)。

## 佇列(Queues)

- 定義
    - 為一抽象資料類型，並具備(能運行)以下幾項操作行為。
        - Enqueue(Key): add key to the collection.
        - Dequeue():removes and returns the <font color=red>LEAST</font> recently-added key
    - 操作行為遵守FIFO(First in First out)原則，或可稱為FCFS(First come First service)。

- 以連結串列(Linked List, with tail pointer) 實作佇列
    - Enqueue: 用List.PushBack
    - Dequeue: 用List.TopFront配合List.PopFront
    - 操作實例<br>![Queue_ops_LL](https://i.imgur.com/dsXLOEi.gif)
        - Enqueue(a)
        - Enqueue(b)
        - Empty() $\rightarrow$ False
        - Enqueue\(c\)
        - Dequeue() $\rightarrow$ a
        - Enqueue(d)
        - Enqueue(e)
        - Enqueue(f)
        - Dequeue() $\rightarrow$ b
        - Dequeue() $\rightarrow$ c
        - Dequeue() $\rightarrow$ d
        - Dequeue() $\rightarrow$ e
        - Dequeue() $\rightarrow$ f
        - Empty() $\rightarrow$ True

- 範例
    - What would happen if we used List.PushFront to implement Enqueue and List.TopBack and List.PopBack to implement Dequeue?
        - The queue would work correctly if the list were singly-linked with a tail pointer.
        - The queue would work correctly, but Dequeue would be O(n) time if the list were doubly-linked with a tail pointer.
        - The queue would work correctly if the list were doubly-linked with a tail pointer.
        - The queue wouldn't work right
        - The queue would work correctly.
        - The queue would work correctly, but Dequeue would be O(n) time if the list were singly-linked with a tail pointer.<br>
    ans: 答案見留言

- 以矩陣(Array) 實作佇列
    - 該實作方式又稱為環形佇列(Circular Queue)
    - 先行準備好的固定陣列。
    - 為了充分利用記憶體空間，用以輔助的read pointer、write pointer。
    - 當運行Enqueue之後write pointer會遞增，運行Dequeue之後則read pointer會遞增，遞增時會以取餘數(mod)運算輔助判斷。
    - Empty的判定標準為read pointer存的index是否等於write pointer存的index
    - 操作實例<br>![Queue_ops_Array](https://i.imgur.com/efQTmMD.gif)
        - Enqueue(a), mod(write++, arr_size) $\rightarrow$ write = 1
        - Enqueue(b), mod(write++, arr_size) $\rightarrow$ write = 2
        - Empty() $\rightarrow$ False, $\because$ read $\neq$ write
        - Enqueue\(c\), mod(write++, arr_size) $\rightarrow$ 3
        - Dequeue() $\rightarrow$ a, mod(read++, arr_size) $\rightarrow$ read = 1
        - Dequeue() $\rightarrow$ b, mod(read++, arr_size) $\rightarrow$ read = 2
        - Enqueue(d), mod(write++, arr_size) $\rightarrow$ write = 4
        - Enqueue(e), mod(write++, arr_size) $\rightarrow$ write = 0
        - Enqueue(f), mod(write++, arr_size) $\rightarrow$ write = 1
        - Enqueue(g) $\rightarrow$ ERROR
        - Dequeue() $\rightarrow$ c, mod(read++, arr_size) $\rightarrow$ read = 3
        - Dequeue() $\rightarrow$ d, mod(read++, arr_size) $\rightarrow$ read = 4
        - Dequeue() $\rightarrow$ e, mod(read++, arr_size) $\rightarrow$ read = 0
        - Dequeue() $\rightarrow$ f, mod(read++, arr_size) $\rightarrow$ read = 1
        - Empty() $\rightarrow$ True, $\because$ read = write
    - 從上例可以看到，因為佇列是否為空的判定標準在於read、write是否相等，所以為了避免邏輯上無法判定到底為『空』或為『滿』，當read、write只差1時若想要Enqueue(g)會被阻擋；這樣的實作方法本身就自帶缺陷，無法100%利用所有矩陣空間(最多只能填滿n-1個矩陣空位，n為矩陣長度)。
    - 更詳細的矩陣實作佇列方法可參考[網頁](http://alrightchiu.github.io/SecondRound/queue-yi-arrayshi-zuo-queue.html)，本課程(及筆記)僅針對最有效率且常用的Circular Queue做說明。

- 結論
    - 以矩陣或連結串列(with tail pointer)皆能夠實作佇列。
    - 就空間使用充分率而言，連結串列較矩陣來得佳。
    - 對佇列的各項操作皆花費$\mathcal{O}(1)$時間，包含Enqueue、Dequeue、Empty。

## 樹(Trees)

- 範例
    - 文句語法<br>![Tree_SyntaxTree_Sentence](https://i.imgur.com/3kXVyh2.png)
    - 數學表示式<br>![Tree_SyntaxTree_Expression](https://i.imgur.com/yh2k3q5.png)
    - 地理等級<br>![Tree_GeographyHierachy](https://i.imgur.com/eRMvO4E.png)
    - 程式碼<br>![Tree_SyntaxTree_Code](https://i.imgur.com/0WP1fWy.png)

- 二元搜尋樹(Binary Search Tree)
    - 結構如前述的範例由節點(Node)連接所構成。
    - 整棵樹的結構頂端的節點稱為根(Root, Top node)，並向下階層式地延伸，根的階層通常為1(也有些使用0為起始階層)。
    - 從根開始每個擁有子節點(Child, Children)的節點(稱為父節點，Parent)最多含有兩個子節點(二元結構)。
    - 每個節點擁有的子節點劃分為左子節點(Left Child)及右子節點(Right Child)。
    - 以任一節點i為根，其左子節點為根的子樹集合稱為左子樹，右子節點為根的子樹集合稱為右子樹。
    - 左子樹的所有節點其鍵值必定小於等於該子樹的根
    - 右子樹的所有節點其鍵值必定大於等於該子樹的根

- 樹的定義
    - 可為空樹，或非空樹則滿足下列條件
    - 具有若干節點，且每個節點皆具備：
        -  鍵值
        -  子樹的集合(清單、列表，課程影片的slide原文用的是"list")
    - 簡單的樹(Simple Tree)
        - 空樹
        - 一個節點<br>![Tree_One_Node](https://i.imgur.com/OgcEaXg.png)
        - 兩個節點<br>![Tree_Two_Nodes](https://i.imgur.com/Ts8DwCz.png)
    - 常用術(樹)語<br>![Tree_Terminology](https://i.imgur.com/isZYFwn.png)
        - 根(Root)：Top node in the tree, e.g. Fred
        - 父母(Parent) vs. 小孩(Child)： A child has a line down directly from a parent. E.g. Kate is a parent of Sam, Sam is a child of Kate
        - 祖先(Ancestor)：Parent, parent of parent, etc.
        - 後代(Descendant)：Child, child of child, etc.
        - 兄弟姊妹(Sibling)：Two(or more) nodes sharing the same parent, e.g. Kate, Sally and Jim are siblings.
        - 葉子(Leaf)：Node with no children, e.g. Sam.
        - 非樹葉(Interior node, non-leaf)
        - 階層(Level)：(1+# edges) between root and node. E.g. Level of Jim is 2, level of Hugh is 3.
        - 樹高(Height)：Maximum depth of sub tree node and farthest leaf. E.g. Height of Sam is 3, height of Kate is 2, height of Fred is 1.
        - 森林(Forest)：Collection of trees.
    - 範例
        - Is Hugh a child of Fred?<br>ans: 答案見留言

- Node contains:
    - key
    - children: list of children nodes.
    - (optional) parent

- For Binary Tree, Node contains:
    - key
    - left
    - right
    - (optional) parent

- Height(tree)
```=
if tree = nil:
    return 0
return 1 + Max(Height(tree.left), Height(tree.right))
```

- Size(tree)
```=
if tree = nil:
    return 0
return 1 + Size(tree.left) + Size(tree.right)
```

## 樹的遍歷(追蹤，Traversal)

- 對於一樹來說，通常我們會希望以特定的順序走訪該樹的節點，例如：Print the nodes of the tree.
    - Depth-first：走訪完其中一個子節點為根的子樹，才會移至該子節點的其他Sibling為根的子樹。
    - Breadth-first：走訪完同一階層所有的節點才往下一個階層運行。

- Depth-First Traversal
    - 基本原則
        - 以任一根節點為基準，有三種遍歷方式，分別代表根節點位於中序(In-Order)、前序(Pre-Order)、後序(Post-Order)，同時代表印出順序。
    - 中序表示，InOrderTraversal(tree)
        - 中序表示以節點i為根，先遍歷(印出、print)i的左子樹所有節點後，再印出根節點i，最後遍歷(印出、print)i的右子樹所有節點；依序印出後，任一節點i之前印出的所有節點均屬於i的左子樹，其後印出的所有節點則均屬於i的右子樹。
            ```=
            if tree = nil:
                return
            InOrderTraversal(tree.left)
            Print(tree.key)
            InOrderTraversal(tree.right)
            ```
            ![Tree_InOrderTraversal](https://i.imgur.com/pTbTtUH.gif)
    - 前序表示，PreOrderTraversal(tree)
        - 前序表示以節點i為根，先印出根節點i，再遍歷i的左子樹所有節點後，最後遍歷i的右子樹所有節點；依序印出後，任一節點i<font color=red>之後</font>印出的所有節點，可切分為前半部i的左子樹，與後半部i的右子樹。
            ```=
            if tree = nil:
                return
            Print(tree.key)
            PreOrderTraversal(tree.left)
            PreOrderTraversal(tree.right)
            ```
            ![Tree_PreOrderTraversal](https://i.imgur.com/FIS5WgG.gif)
    - 後序表示，PostOrderTraversal(tree)
        - 後序表示以節點i為根，先遍歷i的左子樹所有節點後，再遍歷i的右子樹所有節點，最後印出根節點i；依序印出後，任一節點i<font color=red>之前</font>印出的所有節點，可切分為前半部i的左子樹，與後半部i的右子樹。
            ```=
            if tree = nil:
                return
            PostOrderTraversal(tree.left)
            PostOrderTraversal(tree.right)
            Print(tree.key)
            ```
            ![Tree_PostOrderTraversal](https://i.imgur.com/nPHbGa1.gif)

- Breadth-First Traversal
    - 從整棵樹的根節點開始，沒有例外，一層(Level)的所有節點遍歷完後才會往下至下一階。
    - 層序表示，LevelTraversal(tree)
        ```=
        if tree = nil:
            return
        Queue q
        q.Enqueue(tree)
        while not q.Empty():
            node <-- q.Dequeue()
            Print(node)
            if node.left ~= nil:
                q.Enqueue(node.left)
            if node.right ~= nil:
                q.Enqueue(node.right)
        ```
        
- 結論
    - 很多不同且性質迥異的事物都可能可以用樹的結構表示。
    - 樹的結構中，節點含有鍵值(Key)與子節點(Children)。
    - 樹的遍歷(追蹤)包含：DFS(pre-order、in-order、post-order)與BFS。
    - 遞迴演算法(或程式設計方法)在樹的遍歷上為常見應用手段。