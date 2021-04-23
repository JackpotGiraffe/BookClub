# Data Structures Week 4

## Objective
    - List applications of hashing
    - Apply direct addressing to retrieve names by phone numbers
    - Develop a hash table based on chaining scheme
    - Apply hashing to find patterns in text
    - Describe how Dropbox, Google Drive and Yandex Disk save space
    - Describe the principles on which distributed hash tables are built

- 學習目標
    - 雜湊的應用。
    - 運用直接定址(Direct Addressing)，藉電話號碼來尋找對應的名字。
    - 以Chaining Scheme做為機主建立雜湊表。
    - 運用雜湊尋找字串中的模式。
    - 描述解釋Dropbox、Google Drive以及Yandex Disk如何節省空間。
    - 描述解釋分散式雜湊表的建立有哪些原則。

## Applications of Hashing

- Programming Language
    - 許多語言內建的資料結構本身就是建基於Hash table。例如Python中的dict、Java中的HashMap等等。
    - 程式語言中的key word運作的需要，就必須利用Hashing的方式讓compiler能夠將其與其他識別字分開判定。

- File System
    - 儲存檔案本身與其路徑，以及物理位置之間的對應性，就是依靠map完成，而map通常就是實作成Hash table的形式。

- Password Verification
    - 常見於網頁服務中，好的網頁處理不會將你鍵入的密碼以明碼方式送至伺服器端驗證，因為若遇到任何人從中擷取，那麼私人資料很容易就暴露洩露。通常密碼會在客戶端被計算成Hash value在遞交給伺服器，伺服器僅將是先存好的密碼算出的Hash value比對，若完全吻合則身分驗證即成功。
    - 就Hash function的性質而言，因為幾乎不可能找到不同字串具有相同的Hash value(此性質後面會提)，所以便能用這樣的方是達到加密的目的。

- Storage Optimization
    - 例如Dropbox、Google Drive或Yandex Disk，都會用到極大量的空間來儲存使用者的檔案，在這樣的情況下優化空間分配、組織就必須要借用hashing的方法。

## Analyzing Service Access Logs

- IP Addressing
    - 以這樣的議題能夠闡明接下來課程中介紹的各類方法。
    - 以圖為例，上為Server，下為Clients。<br>![](https://i.imgur.com/SNofuJS.png)<br>在Internet中系統會分配獨特的地址給每台電腦，稱為IP address(IPs)。IP由4個被dots分隔的integers組成，每個integer範圍都是0~255，所以能夠被存於8 bits的記憶體中，而整個IP便能夠在C++或Java這類程式語言中以integer type的形式儲存於32 bits的記憶體。總體而言能夠有$2^{32}$種不同的IPs，約為40億。
    - 近期因為網路結構變得越來越大，而導致40億個不同的IP位址已經不再足夠應付其中的所有使用者的需求，所以IPv6這樣新的定址系統被設計出來，其能夠應付的IPs數量提升至$2^{128}$換句話說就是39個digits的IP位址，如此一來IP位址的供應便能有很長一段時間保持充足。
    - 當有人訪問你所擁有的網頁服務，你便能夠透過IPs知道他/她的身分，且這類資訊會存到一個特殊檔案稱為access log。<br>![](https://i.imgur.com/tiqXwMU.png)
    - 對於access log的分析至關重要，可能影響網頁服務運作的效益、受到攻擊時是否能即時反應採取行動等等。

- Log Processing
    - 單位時間內的紀錄可能可以高達上百萬條。
    - 要逐一查詢檢驗太耗費時間。
    - Keep count: 若要處理這類分析，必須要有適當的計數器持續對access log上不同的IP做累計計數。
    - 另外用適當的資料結構\(`C`\)儲存IPs與計數器之間的「對應關係」。
    - Main Loop
    ```=
    log: array of log lines (time, IP)
    C: mapping from IPs to counters
    i: first unprocessed log line
    j: first line in current 1hr window
    i <-- 0
    j <-- 0
    C <-- nil
    Each second
        UpdateAccessList(log, i, j, C)
    ```
    - UpdateAccessList(log, i, j, C)
    ```=
    while log[i].time <= Now():
        C[log[i].IP] <-- C[log[i].IP] + 1
        i <-- i + 1
    while log[j].time <= Now() - 3600:
        C[log[j].IP] <-- C[log[j].IP] - 1
        j <-- j + 1
    ```
    - 示意圖<br>![](https://i.imgur.com/bEHA8Db.png)
    - AccessdLastHour(IP, C)
    ```=
    return C[IP] > 0
    ```
    - How to implement the mapping C?

## Direct Addressing

- How to implement the mapping C?
    - 需要data structure for C。
    - 我們已經知道IP(v4)共有$2^{32}$種不同的IP位址。
    - 將IP位址轉換成32-bit整數。
    - 準備一具有$2^{32}$位置的陣列。
    - 用 **A[int(IP)]** 表示 **C[IP]** 。
    - 示意圖<br>![](https://i.imgur.com/XeqqRKp.png)
        - int(0.0.0.1) = 1
        - int(172.16.254.1) = 2886794753
        - 範例
            - 69.171.230.68 = ?
- 重新改寫Operations
    - int(IP)
    ```=
    return IP[1]*2^24 + IP[2]*2^16 + IP[3]*2^8 + IP[4]
    ```
    - UpdateAccessList(log, i, j, A)
    ```=
    while log[i].time <= Now():
        A[int(log[i].IP)] <-- A[int(log[i].IP)] + 1
        i <-- i + 1
    while log[j].time <= Now() - 3600:
        A[int(log[j].IP)] <-- A[int(log[j].IP)] - 1
        j <-- j + 1
    ```
    - AccessdLastHour(IP, A)
    ```=
    return A[int(IP)] > 0
    ```
    - Asymptotic
        - UpdateAccessList is $\mathcal{O}(1)$ per log line
        - AccessedLastHour is $\mathcal{O}(1)$
            - Drawback: $2^{23}$ memory even for few IPs.
            - Drawback: IPv6($2^{128}$) won't fit in memory.
        - In general: $\mathcal{O}(N)$ memory, $N = |S|$. (S是size of the universe, 公尛)

## List-based Mapping

- 問題
    - 直接定址需要太多記憶體空間。
    - 且過多空間浪費，因為只儲存相對活躍的IP資料。

- 解決方法
    - 根據IP訪問時間依序存在list中

- Operations
    - UpdateAccessList(log, i, L)
    ```=
    while log[i].time <= Now():
        L.Append(log[i])
        i <-- i + 1
    while L.Top().time <= Now() - 3600:
        L.Pop()
    ```
    - AccessdLastHour(IP, L)
    ```=
    return L.FindByIP(IP) ~= NULL
    ```
    - AccessCountLastHour(IP, L)
    ```=
    return L.CountIP(IP)
    ```
    - Asymptotic
        - $n$ 表示活躍IPs
        - Memory usage is $\mathcal{O}(n)$
        - L.Append, L.Top, L.Pop are $\mathcal{O}(1)$
        - UpdateAccessList is $\mathcal{O}(1)$ per log line
        - L.FindByIP and L.CountIP are $\mathcal{O}(n)$
        - AccessedLastHour and AccessCountLastHour are $\mathcal{O}(n)$
        - 最後訪問時間與訪問次數兩者的時間較不樂觀，因為即使不考量使用任何特定的資料結構，單純將log file從頭到尾掃一次，都可以得到完全一樣的時間花費，可見該方法與前面的作法比起來沒有聰明到哪裡去。
        - Is it overall a failure?(金派餒)
        - 接下來即將結合直接定址的scheme以及list based的方法，誕生出在記憶體消耗以及操作時間花費上兼顧的方法，Hash Function重磅登場！！！！！！

## Hash Functions

- Overview
    - Hash Function 是什麼鬼
    - 如何把它用來解決IP位址的問題
    - 為啥用起來這麼困難

- Encoding IPs
    - 將IP編碼儲存，需要大量記憶體，因為有時編碼後的數字大到需要32個bits才能儲存。
    - 如果能夠將IPs只編碼成較小的數字，例如0~999。
    - 仍然會需要辨認活躍度IP的相關程式碼，因為對於IP個別計數的需求仍然存在。

- 定義
    - 對於任何物件而成的集合*S*以及任何整數$m>0$，函式*h*: *S* → {0, 1, …, m-1}即稱為雜湊函式(Hash Function)
    - $m$稱為hash function *h*的cardinality(基數)。

- 性質
    - *h*應要能夠快速運算
    - 對於集合*S*中不同的物件應要能夠運算出不同的值
    - 希望能達到類似直接定址的效果，但只需花用$\mathcal{O}(m)$的記憶體
    - Cardinality $m$ 能夠越小越好
    - 當$|S|$比$m$大時，則*h*計算出的值則不可能全數相異
    - 產生Collisions
        - When $h(o_{1})=h(o_{2})$ and $o_{1} \neq o_{2}$ calls a collision.

## Chaining Scheme

- One of the most frequently used techniques for using hashing to store mappings.

- Map
    - 目的是為了維護或儲存一群(類)物件與另一群(類)物件的對應關係。
        - Filename $\rightarrow$ file on disk
        - Student ID $\rightarrow$ student name
        - Contact name $\rightarrow$ contact phone number
    - 定義
        - 從集合$S$到集合$V$之間的對應關係，且支援HashKey($O$), Get($O$), Set($O, v$)操作的資料結構，稱為Map，where $O \in S, v \in V$。

- 藉由Hash Function實作Map
    - Chaining
        - 準備一陣列，size為$m$，$m$即hash function(h)的cardinality。<br>![](https://i.imgur.com/8QJruJ5.png)
        - 該陣列不存integer，而是存list
        - 每個list為一對資料組成，物件$O$及$v$值
        - 範例：<br>![](https://i.imgur.com/CbqM5I8.png)
        - 這是應用Hash function時能夠避免collisions的一種常見方法。

## Chaining Implementation and Analysis

- 實作面從基本操作出發
    $h: S \rightarrow {0, 1, ..., m-1}$
    $O, O' \in S$
    $v, v' \in V$
    $A \leftarrow \text{array of }m \text{ lists (chains) of }pairs(O, v)$
    - HashKey(O)
    ```=
    L <-- A[h(O)]
    for (O', v') in L:
        if O' == O
            return true
    return false
    ```
    - Get(O)
    ```=
    L <-- A[h(O)]
    for (O', v') in L:
        if O' == O:
            return v'
    return n/a (←第一次在pseudocode看到這種寫法)
    ```
    - Set(O)
    ```=
    L <-- A[h(O)]
    for p in L:
        if p.O == O
        p.v <-- v
        return
    L.append(O, v)
    ```
    
- ***Lemma***
    - Let c be the length of the longest chain A. Then the running time of HsahKey, Get, Set is $\mathcal{\Theta}(c+1)$
    - 如果c是A中的最常鍊的長度，則HashKey、Get、Set三者花費的時間會是$\mathcal{\Theta}(c+1)$
    - Proof
        - $\text{if } L = A[h(O)], \text{ len}(L) = c, O \notin L$, need to scan all c items.

- ***Lemma***(Memory consumption)
    - Let $n$ be the number of different keys $O$ currently in the map and $m$ be the cardinality of the hash function. Then the memory cosumption for chaining is $\mathcal{\Theta}(n+m)$
    - Proof
        - $\mathcal{\Theta}(n)$ to store $n$ pairs $(O, v)$
        - $\mathcal{\Theta}(m)$ to store array $A$ of size $m$

## Hash Tables

- Definition of Set
    - A data structure with at least Add(O), Remove(O), Find(O)
    - Example
        - IPs accessed during last hour.
        - Students on campus.
        - Keywords in a programming language.

- 用Chaining的概念實作Set的方法
    - 第一種：Set等同於從$S$到$V={true, false}$的Map。
        - not very efficient
        - 需要存物件與值的對應數兩倍的結果
        - 從map中remove物件比較困難，反而是將其對應的valeu設為false
    - 第二種：只存O而不存(O, v)pairs。
        $h: S \rightarrow {0, 1, ..., m-1}$
        $O, O' \in S$
        $A \leftarrow$ array of $m$ lists (chains) of objects $O$
        - Find(O)
        ```=
        L <-- A[h(O)]
        for O' in L:
            if O' == O:
                return true
        return false
        ```
        - Add(O)
        ```=
        L <-- A[h(O)]
        for O' in L:
            if O' == O:
                return
        L.Append(O)
        ```
        - Remove(O)
        ```=
        if not Find(O):
            return
        L <-- A[h(O)]
        L.Erase(O)
        ```
        
- Hash Table
    - 定義
        - 用Hashing實作的map或set則可稱為Hash Table。
    - 程式語言實例
        - Set
            - unordered_set in C++
            - HashSet in Java
            - set in Python
        - Map:
            - unordered_map in c++
            - HashMap in Java
            - dict in Python

- 結論
    - Chaining為一項用在實作Hash Table的技術。
    - Hash table的記憶體花費為$\mathcal{O}(n+m)$
    - Operations work in time $\mathcal{O}(c+1)$
    - 衍伸思考：如何讓$m$跟$c$變得更小？