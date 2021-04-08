
# Data Structure (Note)

- 參考課程：[Data Structures (Coursera)](https://www.coursera.org/learn/data-structures)
    - 課程為期6週
    - 課程由俄羅斯名校HSE University設計
    - 該課程有下列幾項注意事項
        - Assignments 非常重要，實作是了解資料結構及其運作原理的重要一環。
        - 強烈建議將 [Algorithmic Toolbox](https://www.coursera.org/learn/algorithmic-toolbox) 的第一週課程課程先行瀏覽過一遍，以熟悉本課程的 assignments 實作、測試、除錯、提交等流程。
    - 下面是本課程含括的內容中的幾個範例 (其他細節可參考 [syllabus](https://www.coursera.org/learn/data-structures#syllabus))
        1. 針對動態矩陣(Dynamic Array)調整大小(Resize) 時的優良策略。
        2. 如何在不同的程式語言中實作優先權佇列(Priority Queues) ，包括C++、Java、Python(低階→高階)。
        3. 雜湊表(Hash Table)該如何實作，才能在各項基本操作(新增、刪除、修改、插入等等)的平攤執行時間(Amortized Running Time) 上達到$\mathcal{O}(1)$的平均結果。
        4. 什麼樣的策略能夠維持二元樹(Binary Tree) 較佳的平衡狀態。

# 矩陣(Arrays)

- Can be on a stack(堆疊), in a heap(堆積)

- 定義
    - 記憶體上的連續空間(區域)、一大(區)塊記憶體(One chunk of memory)。
    - 該連續空間被劃分為大小相等的若干個元素、元件。
    - 每個元素都依序被標上連續的正整數作為指標(index, 索引值)。<br>![矩陣範例](https://i.imgur.com/5bsv37v.png)
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
        
        ans:<font color=white>2. 1048</font> ←反白

- 多維矩陣
    - 二維矩陣範例![二維矩陣範例](https://i.imgur.com/0P1QbUg.png)<br>該二維矩陣同樣能透過前述計算式獲得記憶體位置資訊。
    - 唯一必須注意的是，一般通用邏輯為先 row 再 column。

- 藉由上述的記憶體位置說明矩陣實際在記憶體中配置的狀態<br>![Memory Allocation](https://i.imgur.com/9GRiISZ.png)
<br>分為 Row-major 及 Column-major 兩種做法，以二維矩陣為例：
    - Row-major
        - 先遍歷行(Column)，再遍歷列(Row)。
        - 就記憶體上的連續性來看，row 相較於 column 來說索引值會較常保持不變。
    - Column-major
        - 先遍歷列(Row)，再遍歷行(Column)。
        - 就記憶體上的連續性來看，column 相較於 row 來說索引值會較常保持不變。

- 範例
    - Assume you have a three-dimensional array laid out in column-major order with the first element at indices (1, 1, 1). What are the indices of the next element in memory?

        <ol>
            <li>(2, 1, 1)</li>
            <li>(1, 1, 2)</li>
            <li>(1, 2, 1)</li>
        </ol>
        
        ans:<font color=white>1. (2, 1, 1)</font> ←反白

- 矩陣的基本操作
    - 見下圖說明<br>![矩陣基本操作](https://i.imgur.com/3hWGvly.png)
        - 對矩陣做增加或刪除元素的操作行為，皆不影響時間複雜度(Time Complexity)，僅討論操作時的起始位置(索引值)。
        - 若從矩陣起始位置著手操作，所需時間為$\mathcal{O}(n)$，因為除了主要目的以外，還必須將整個矩陣的其他元素依序位移。
        - 若從矩陣末端位置著手操作，所需時間僅為$\mathcal{O}(1)$，因為可以透過索引值直接取得操作對象的位置。
        - 從矩陣中央位置著手操作，因矩陣大小為$n$，所需時間為$n/2$，就操作時間複雜度而言仍為$\mathcal{O}(1)$。

- 結論
    - 矩陣(Arrays)：記憶體上由若干個佔據同樣大小空間的元素組成的連續區塊，且以連續正整數標記作為索引值。
    - 訪問任一元素的時間複雜度被bond在常數時間內。
    - 從矩陣末端做基本操作(新增、刪除)所需時間為常數$\mathcal{O}(1)$。
    - 從矩陣任意位置(非末端)做基本操作所需時間為線性$\mathcal{O}(n)$。