# Data Structures Week 2

## Objective
    - Describe how dynamic arrays work.
    - Calculate amortized running time of operations.
    - List the methods for amortized analysis.

- 學習目標
    - 描述動態矩陣的運作細節。
    - 計算資料結構中基本操作的平攤運行時間。
    - 表列平攤分析(Amortized Analysis)中常會使用到的方法。

## 動態矩陣(Dynamic Array)

- 矩陣為靜態基本資料結構，一旦編譯前決定大小，在編譯之後便無法改變。
    - 動態配置矩陣是其中一種解決辦法，能夠在執行程式期間調整矩陣的空間，動態地配置在記憶體上。
    - 但這樣的做法另一面帶來問題，如果配置時，並不知道矩陣的最大空間需要多少該如何？
    - 我們用間接的方法解決上述問題，與其直接儲存靜態或動態矩陣的參照值(Reference)，不如將動態矩陣的指標(Pointer)保存，並在需要擴充矩陣大小時，即時改變指標至新配置的動態矩陣，將舊的矩陣拋棄，釋放佔有記憶替空間。
    - 此方法即為動態矩陣，或有些人稱其為可調整大小的矩陣(Resizable Arrays)，且與「動態地配置位置之矩陣」(Dynamically Allocated Arrays)有所區別。

- 定義
    - 一種抽象資料結構，並且(最少)能對其做以下操作：
        - Get(i): returns element at location i.
        - Set(i, val): sets element i to val.
        - PushBack(val): adds val to the end.
        - Remove(i): Removes element at location i.
        - Size(): the numbers of element.

- 實作
    - arr: Dynamically-allocated array.
    - capacity: size of the dynamically-allocated array.
    - size: number of elements currently in the array.<br>![DynamicArray_example](https://i.imgur.com/Ih4ZnP2.gif)

- 各項操作方法
    - Get(i)
    ```
    if i < 0 or i >= size:
        ERROR: index out of range
    return arrapi[]
    ```
    - Set(i, val)
    ```
    if i < 0 or i >= size:
        ERROR: index out of ragne
    arr[i] = val
    ```
    - PushBack(val)
    ```
    if size = capacitey:
        allocate new_arr[2 * capacity]
        for i from 0 to size - 1:
              new_arr[i] <-- arr[i]
        free arr
        arr <-- new_arr; 
        capacity <-- 2 * capacity
    arr[size] <-- val
    size <-- size + 1
    ```
    - Remove(i)
    ```
    if i <0 or i >= size:
        ERROR; index out of reange
    for j from i to size-2:
        arr[j] <--arr[j + 1]
    size <-- size - 1
    ```
    - Size()
    ```
    reutrn siez()
    ```
    
- 時間複雜度分析

| Operations | Runtimes |
| -------: | :------- |
| Get(i) | $\mathcal{O}(1)$ |
| Set(i, val) | $\mathcal{O}(1)$ |
| PushBack(val) | $\mathcal{O}(n)$ |
| Remove(i) | $\mathcal{O}(n)$ |
| Size|$\mathcal{O}(1)$|

- 結論
    - 不同於靜態矩陣，動態矩陣能夠動態地調整大小。
    - 新增資料到一個動態矩陣中通常只需花費常數時間，但也有可能用掉線性時間。
    - 動態矩陣中的部分空間會被浪費掉無法使用。

## 平攤分析(Amortized Analysis)

- 平攤分析(Amortized Analysis)
    - 在操作的分析中，通常我們會針對獨立的最糟情況討論，但這樣有時候會過於嚴苛，尤其是當我們透過一系列的操作分析最糟的情況，而其中某些部分的操作行為成本相對低很多時，如果我們將每個步驟都以最糟情況看待，再將結論總和，對於時間總花費可能會誇大解釋。
    - 以動態矩陣為例(Adding element)
        - 我們僅在操作過程中的部分情況下才會調整大小。
        - 在大多數情況下要增加新元素，只需要花費常數時間。
        - 數個$\mathcal{O}(1)$的操作中才會出現一次$\mathcal{O}(n)$的操作
        - 這樣的情況下，新增元素的總花費時間到底為多少？
    - 定義
        - 給定一系列的操作行為，且各操作的時間花費為n(Worst case)，平攤時間為：
        $$
        \frac{Cost(n~operations)}{n}
        $$
        - 舉例來說今天你要購買一輛總價為6000美金的車子，且該車輛能夠保持性能直到5年後你必須再買新車。
            - 第一種方法你可以馬上拿出6000美金買下車子，等到5年之後需要換新，再度花費6000美金。
            - 第二種方法你能先擁有這台車，並且將費用分成每個月100美金，再接下來5年(60個月)期間按月繳費。
            - 上述說明中，最壞的情況下一個月要繳清6000美金，剩下59個月不需要繳費，但實際上卻可以採用後者的方法讓每個月負擔不致於太重。

- 聚合分析(Aggregate Method)
    - 動態矩陣：n次呼叫PushBack
    Let $c_{i}$ = cost of $i$-th insertion.
    $$
    \begin{align}
    &c_{i}=1+\left\{
        \begin{array}{ll}
            i-1 &\text{if }i-1\text{ is a power of } 2\\
            0 &otherwise
        \end{array}
              \right.\\
    &\frac{\sum^{n}_{i=1}c_{i}}{n}=\frac{n+\sum^{\lfloor\log_{2}(n-1)\rfloor}_{j=1}2^{j}}{n}=\frac{\mathcal{O}(n)}{n}=\mathcal{O}(1)
    \end{align}
    $$
    
- 銀行員記帳法(Banker's Method)
    - 特色
        - 對相對低成本的操作行為額外計費。
        - 將額外計費的成本以代幣形式保存於資料結構中(概念上)。
        - 將代幣用來抵用相對高成本的操作行為所需的花費。
        - 類似平攤貸款(Amortizing Loan)
    - 動態矩陣：n次呼叫PushBack
        - 每次insertion的花費為3：insertion所需的原始成本為1枚代幣，並且將其他2枚代幣留存使用。
        - 當遭遇到矩陣空間不足，必須要在insertion時運行額外(成本)操作時，便能使用每次insertion留存的代幣。
        - 搬移後，固定原始成本2枚代幣中的一枚置於新插入的元素，另一枚則置於$\frac{capacity}{2}$的元素上(用capacity似乎不甚準確)。
        - 操作範例<br>![AmortizedAnalysis_Banker](https://i.imgur.com/P5xsvr8.gif)
        - $\mathcal{O}(1)$

- 物理學家方法(Physicist's Method， 又稱位能法，Potential Method)
    - 特色
        - 利用位能方程式(Potential Function)，將資料結構的各種狀態各自對應至一個整數。
        - 相似於高中物理中，位能的精神概念。當我們將一顆球帶到山頂上，便能提高該球的位能，此時若於山頂釋放這顆球，便能夠將儲存的位能轉換成動能。
        - 定義位能方程式$\Phi$，使其能對應資料結構的狀態與整數
        $\Phi(h_{0})=0$
        $\Phi(h_{t})\ge0$
        - 操作t (operation t)的平攤成本：
        $c_{t} + \Phi(h_{t}) - \Phi(h_{t-1})$
        - 選用適當的$\Phi$使得位能會：以相同的比例規模
            - 若$c_{t}$較小時，下降。
            - 若$c_{t}$較大時，上升。
        - n個operation所需的總成本為：
        $$
        \sum^{n}_{i=1}c_{i}
        $$
        - 平攤成本加總則為：
        $$
        \begin{align}
            \sum^{n}_{i=1}(&c_{i}+\Phi(h_{i})-\Phi(h_{i-1}))\\
            =~&c_{1}+\Phi(h_{1})-\Phi(h_{0})+\\
            &c_{2}+\Phi(h_{2})-\Phi(h_{1})+\\
            &c_{3}+\Phi(h_{3})-\Phi(h_{2})+\\
            &\cdots+\\
            &c_{n}+\Phi(h_{n})-\Phi(h_{n-1})\\
            =~&\Phi(h_{n})-\Phi(h_{0})+\sum^{n}_{i=1}c_{i}\ge\sum^{n}_{i=1}c_{i}
        \end{align}
        $$
    - 動態矩陣：n次呼叫PushBack
    Let $\Phi(h)=2\times\text{size}-\text{capacity}$
        - $\Phi(h_{0})=2\times0-0$
        - $\Phi(h_{i})=2\times\text{size}-\text{capacity}\gt0(\because\text{size}\gt\frac{\text{capacity}}{2})$
    - 在沒有調整矩陣大小的情況下新增元素i
        - 平攤成本：
        $$
        \begin{align}
            c_{i}+&\Phi(h_{i})-\Phi(h_{i-1})\\
            &=1+2\times\text{size}_{i}-\text{cap}_{i}-(2\times\text{size}_{i-1}-\text{cap}_{i-1})\\
            &=1+2\times(\text{size}_{i}-\text{size}_{i-1})\\
            &=3
        \end{align}
        $$
    - 在必須調整矩陣大小的情況下新增元素i
        - 平攤成本：
        $$
        \begin{align}
            \text{Let }k=&\text{ size}_{i-1}=\text{ cap}_{i-1}\\
            \Phi(h_{i-1})&=2\times\text{size}_{i-1}-\text{cap}_{i-1}\\
            &=2k-k\\
            &=k\\
            \Phi(h_{i})&=2\times\text{size}_{i}-\text{cap}_{i}\\
            &=2(k+1)-2k\\
            &=2\\
            c_{i}+\Phi&(h_{i})-\Phi(h_{i-1})\\
            &=(\text{size}_{i})+2-k\\
            &=(k+1)+2-k\\
            &=3
        \end{align}
        $$
    - 範例
        - Will the same argument work if we replace the potential function and set $\Phi(h) = 3 \times size - capacity$?<br>ans: 答案見留言
        
- 延伸思考
    - 在前述內容中，我們的假設前提是每次resize矩陣時，都將大小乘以2倍，也就是說以等比級數的方式成長。
    - 若將該成長係數(Growth Factor)改為其他正數(例如1.5、3、15)，上方一系列的分析是否還能成立？ans: 答案見留言
    - 若將resize矩陣的方式改為常數增加，例如每次resize增加10個位置，上方一系列的分析是否還能成立？ans: 答案見留言
        - If we expend by 10 each time, then:
        Let $c_{i}$ = cost of $i$-th insertion.
        $$
        \begin{align}
        c_{i}=1+&\left\{
            \begin{array}{ll}
                i-1 &\text{if }i-1\text{ is a multiple of } 10\\
                0 &otherwise
            \end{array}
                  \right.\\
            \frac{\sum^{i=1}_{n}c_{i}}{n}&=\frac{n+\sum^{(n-1)/10}_{j=1}10j}{n}\\
            &=\frac{n+10\sum^{(n-1)/10}_{j=1}j}{n}\\
            &=\frac{n+10\mathcal{O}(n^2)}{n}\\
            &=\frac{\mathcal{O}(n^2)}{n}\\
            &=\mathcal{O}(n)
        \end{align}
        $$

- 結論
    - 根據一系列操作行為的情況及結構，我們能夠推算出單一操作的平攤成本。
    - 計算平攤成本常用的三種方法
        - Aggregate Method(Brute-Force Sum)
        - Banker's Method(Tokens)
        - Physicist's Method(Potential Function, $\Phi$)
    - 在程式實作及分析上與本週課程並無出入或相異，僅僅針對執行時間做分析而已。