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
    - Set(O, v)
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
    - 如果c是A中的最長鏈的長度，則HashKey、Get、Set三者花費的時間會是$\mathcal{\Theta}(c+1)$
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
    - 第一種：Set等同於從$S$到$V=\{true, false\}$的Map。
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
            - unordered_map in C++
            - HashMap in Java
            - dict in Python

- 結論
    - Chaining為一項用在實作Hash Table的技術。
    - Hash table的記憶體花費為$\mathcal{O}(n+m)$
    - Operations work in time $\mathcal{O}(c+1)$
    - 衍伸思考：如何讓$m$跟$c$變得更小？

## Phone Book Problem

- 前面學到了Hash function以及Hash table各式性質以及實作的概念，但這樣的資料結構是否達到高效率的運作，最大還是取決於Hash function的選用。

- Phone Book
    - 設計一種資料結構能夠存取聯絡人資訊，包含聯絡人的名字及對應的電話號碼，且這樣的資料結構應該於以下操作能夠節省時間：
        - 新增或刪除聯絡人資訊
        - 根據聯絡人名字查詢對應的電話號碼
        - 根據來電號碼找到其所屬的聯絡人
    - 必須的Maps：
        - Phone number → name
        - Name → phone number
    - 將這樣的對應關係實作成雜湊表

- 首先應該著重於phone number → name
    - Direct Addressing(又從他開始解說起)
        - int(AAA-BB-CC) = AAABBCC
        - 準備一稱為Name的array，size $=10^{L}$，$L$ 為電話號碼可能具有的最大長度
        - 將電話號碼*P*經過轉換變為int(*P*)儲存於Name[int(*P*)]中
        - 若*P*沒有與其對應的聯絡人，則Name[int(*P*)] = N/A
        - 示意圖<br>![](https://i.imgur.com/IxoydfK.png)

- 操作分析
    - Operations run in $\mathcal{O}(1)$
    - Memory usage: $\mathcal{O}(10^{L})$, L is the maximum length of a phone number.
    - 當遇到不同國家、區域等電話號碼規則，則需要更多memory維護新的陣列，舊的陣列將不再適用。

## Phone Book Problem (趴兔)

- 將前述的Chaining方法用上
    - 選定cardinality為$m$的hash function $h$
    - 用size為$m$的陣列(Name)做存取
    - 將串列(鏈)存於Name的各個位置空間
    - Name[h(int\(P\))]這樣的一個串列包含電話號碼與名字之間的對應關係
    - 示意圖<br>![](https://i.imgur.com/BxAmJib.png)

- 參數表列
    - n: 被存在Name中的電話號碼數量
    - m: hash function 的 cardinality
    - c: 最長鏈的長度
    - 記憶體占用：$\mathcal{O}(n+m)$
    - $\alpha = \frac{n}{m}$ 稱為load factor
    - 操作時間花費：$\mathcal{O}(c+1)$
    - <font color=red>回顧：我們希望m與c能夠越小越好</font>
        - Good Example<br>![](https://i.imgur.com/OxH0ueB.png)
        - Bad Example<br>![](https://i.imgur.com/cVtQfZ5.png)

- 嘗試與選擇
    - First Digits
        - m = 1000 for the map from phone numbers to names.
        - Hash function: phone number的前三碼. E.g. h(800-123-45-67) = 800
        - <font color=red>Problem: area code</font>
            - 相同區域的號碼會得到同樣的hash value，導致chain length變長。<br>h(425-234-55-67) =<br>h(425-123-45-67) =<br>h(425-223-23-23) = ... = 425
    - Last Digits
        - m同上
        - Hash function: phone number的末三碼. E.g. h(800-123-45-67) = 567
        - <font color=red>Problem: many phone numbers end with three zeros.</font>(應該是要表達末三碼相同的電話號碼也是很多很常見?)
    - Random Value
        - m同上
        - Hash function: random number between 0 and 999
        - <font color=green>Uniform distribution of hash value</font>
        - <font color=red>當再次用一樣的input時，hash function會吐出不同的結果，導致根本無法找到任何正確的電話號碼或對應的名字。</font>
        - Hash functtion must be deterministic.

- Good Hash function
    - Deterministic
    - Fast to compute
    - Distributes keys well into different cells
    - Few collisions

- 沒有萬能的(universal) Hash function
    - ***Lemma***<br>若一hash function其可能產生的key非常多($|U| \gg  m$)，then there is a bad input resulting in  many collisions.
    - e.g.<br>![](https://i.imgur.com/MQq8g0k.png)

## Universal Family

- Quick Sort
    - 從$\mathcal{O}(n^{2})$出發
    - 進一步證明random pivot能夠使其在平均$\mathcal{O}(n\text{log}n)$完成
    - 實際上，其實大多數情況下Quick sort能夠運作地比其他排序演算法快。
    - 在雜湊上運用randomization的概念(當然不是指以隨機方式運算雜湊值)。

- Randomization的概念
    - 定義一個由hash functions形成的集合稱為family。
    - 從family中隨機挑選雜湊函數使用。

- Definition
    - Let $U$ be the universe: the set of all possible keys.
    - A set of functions $\mathcal{H} = \{h:U \rightarrow \{0, 1, 2, ..., m-1\}\}$ is called a universal family.
    - For any two keys $x, y \in U, x \neq y$ the probability of collision:<br>$Pr[h(x)=h(y)] \leq \frac{1}{m}$
    - This means that a collision $h(x)=h(y)$ on selected keys $x$ and $y$, $x \neq y$ happens for no more than $\frac{1}{m}$ of all hash functions $h \in \mathcal{H}$

- How Randomization Works
    - $h(x)=\text{random}(\{0, 1, 2, ..., m-1\})$ gives probability of collision exactly $\frac{1}{m}$.
    - All hash function in $\mathcal{H}$ are deterministic.
    - Select a random function $h$ from $\mathcal{H}$
    - Fixed $h$ is used throughout the algorithm.

- Running Time
    - ***Lemma***<br>If $h$ is chosen randomly from a universal family, the average length of the longest chain $c$ is $\mathcal{O}(1+\alpha)$, where $\alpha=\frac{n}{m}$ is the load factor of the hash table.
    - **Corollary**<br>If $h$ is from universal family, operations with hash table run on average in time $\mathcal{O}(1+\alpha)$

- 雜湊表的大小選擇
    - 用cardinality m做為記憶體使用量的控制指標。
    - 理想上load factor介於0.5到1之間$(0.5\lt \alpha \lt 1)$。
    - 儲存的keys數量為n，則記憶體使用量bond在$\mathcal{O}(m)=\mathcal{O}(\frac{n}{\alpha})=\mathcal{O}(n)$。
    - 操作時間則bond在$\mathcal{O}(1+\alpha)=\mathcal{O}(1)$

- 動態雜湊表(Dynamic Hash Table)<br>如果keys的數量未知？<br>該先準備一個極大的hash table嗎？
    - 必定會浪費大量未充分利用的記憶體空間。
    - 應效法dynamic array的概念。
    - 當$\alpha$太大的時候才Resize hash table。
    - 從新的universal family選出新的hash function，並更新所有表中物件。
    - 將load factor保持在0.9以下：
    ```=
    Rehash(T)
    loadFactor <-- T.numberOfKeys / T.size
    if loadFactor > 0.9:
        Create T_new of size 2*T
        Choose h_new with cardinality T_new.size
        For each object O in T:
            Insert O in T_new using h_new
        T <-- T_new, h <-- h_new
    ```
    - Rehash time: 在每次對table的operation之後就該被執行一次，單次時間需要$\mathcal{O}(n)$，但因為rehash理論上來講鮮少被觸發，所以平攤結果仍為$\mathcal{O}(1)$。

## Hashing Integers

- 再次回到Phone number problem
    - 假設電話號碼長度最多為7，例如：148-25-67
    - 將電話號碼轉換成整數$0$到$10^{7}-1=9999999$：<br>148-25-67 $\rightarrow$ 1482567
    - 選定一個比$10^{7}$還大的質數$p$，例如：10000019
    - 確立hash table size，例如：$m=1000$
    - ***Lemma***<br>$\mathcal{H}_{p}=\{h_{p}^{a,b}(x)=((ax+b)\text{ mod }p)\text{ mod }m\}$<br>$\forall a, b: 1 \le a \le p-1, 0 \le b \le p-1$ is an universal family.
    - a, b為任意常數，基本上固定a跟b即為選定特定的hash function
    - x是欲做雜湊運算的input integer，從電話號碼轉換而得
    - 為了這樣的雜湊運算機制與過程，先用線性轉換處理x，再依序對p及m取餘數
    - 所有被a, b數對indexed的hash function會有相同的cardinaly m
    - 範例：<br>What is the size of this hash family (the number of different hash functions in the set $\mathcal{H}$? ans. 答案見留言
    - 原口述內容：The Lemma states that it really will be a universal family for integers between 0 and p minus 1. (這個lemma告訴我們對0~p-1的input來說，這樣的$\mathcal{H}$「真的」是一個universal family，證明在optional video...)

- Hashing Phone Number Example
    - 若 $p=10000019$，選定 $a=34, b=2$ 則 $h=h^{34, 2}_{p}$ ，phone number 148-25-67轉換而得的對應整數 $x=1482567$
    - $(34\times 1482567+2)\text{ mod } 10000019=407185$
    - $407185\text{ mod }1000=185$
    - $h(x)=185$

- General Case
    - 先行確立input的最大長度 (max length of phone number $L$)
    - 將所有可能的key值轉換成 $0\text{ ~ }10^{L}-1$ 的integer
    - 選定質數 $p \gt 10^{L}$
    - 確定table size $m$
    - 藉隨機選擇的 $a, b$ 對應universal family $\mathcal{H}_{p}$ 中的hash function

## Hashing Strings

- Lookup Phone Numbers by Name
    - 實作名字to電話的對應關係
    - Chaining的方法仍然適用
    - Hash function的作用變成針對人名運算
    - 必須要能夠對隨機字串做雜湊
    - You wil learn how strings hashing is implemented in Java!

- 定義
    - Denote by $|S|$ the length of string $S$
    - Examples<br>|"a"|=1<br>|"ab"|=2<br>|"abcde"|=5
    - 細項如下：
        - 給定字串 $S$ ，計算其雜湊值。
        - $S=S[0]S[1]...S[|S|-1]$, where $S[i]$ is individual characters.
        - 在雜湊函數中，運算應當使用到所有的字元，否則將有可能發生大量collisions：
            - $S[0]$ is not used $\to h(\text{"aa"})=h(\text{"ba"})=...=h(\text{"za"})$

- 事前準備
    - 以常用或通用的編碼方式將字元$S[i]$轉換成對應的integer。
    - 選定足夠大的質數 $p$

- 多像式雜湊函數 (Polynomial Hashing)
    - Family of hash functions<br>$\mathcal{P}_{p}=\{h_{p}^{x}(S)=\sum^{|S|-1}_{i=0}S[i]x^{i} \text{ mod } p\}$ with a fixed prime $p$ and all $1\le x\le p-1$ called polynomial.
    - Psuedocode
    ```=
    PolyHash(S, p, x)
    hash <-- 0
    for i from |S|-1 down to 0:
        hash <-- (hash * x + S[i]) mod p
    return hash
    ```
    - Example: $|S|=3$
        - hash = 0
        - hash = $S[2]$ mod $p$
        - hash = $S[1]+x*S[2]$ mod $p$
        - hash = $S[0]+x*S[1]+x^2*S[2]$ mod $p$

- Java Implemetion<br>![](https://i.imgur.com/pSCSXlH.png)

- ***Lemma***<br>對任兩最大長度為$L+1$的相異字串 $s_{1}$ 及 $s_{2}$ ，若從 $\mathcal{P}_{p}$ 中隨機挑出雜湊函數 $h~(\text{by selecting a radom x} \in [1, p-1])$ ，collision發生的機率：<br>$Pr[h(s_{1})=h(s_{2})]$<br>最多不會超過$\frac{L}{p}$
    - Proof idea<br>對固定質數 $p$ 而言，數學式 $a_{0}+a_{1}x+a_{2}x^{2}+...+a_{L}x^{L}=0\text{ (mod }p\text{)}$ 所能得到的 $x$ 相異解最多不會超過 $L$ 組。
    - 我們對 $L$ 沒有決定權，但是可以選擇夠大的 $p$ 使得 $\frac{L}{p}$ 變得非常小。
    - 運行時間受字串的最大長度影響，而不會因選擇的質數 $p$ 有所改變。

## Hashing Strings - Cardinality Fix

- Proposed method
    - 選定cardinality $m$ 以及足夠大的質數 $p$，且 $p>m$。
    - 從 polynomial hash family $\mathcal{P}_{p}$ 中隨機選擇 hash function $h$
    - 再從 universal family $\mathcal{H}_{p}$ 隨機挑選 hash function $h_{\text{int}}$ 用來對 $0 \sim p-1$ 的整數運算
    - 用hash function $h_{m}(x)=h_{\text{int}}(h(x))$表示整個字串的雜湊運算結果

- ***Lemma***<br>對任兩最大長度為$L+1$的相異字串 $s_{1}$ 及 $s_{2}$ ，若從 $\mathcal{P}_{p}$ 中隨機挑出雜湊函數 $h~(\text{by selecting a radom x} \in [1, p-1])$ ，collision發生的機率：$Pr[h_{m}(s_{1})=h_{m}(s_{2})]$ 最多不會超過$\frac{1}{m}+\frac{L}{p}$
    - Corollary<br>如果 $p\gt mL$, 對任兩最大長度為$L+1$的相異字串 $s_{1}$ 及 $s_{2}$collision發生的機率：$Pr[h_{m}(s_{1})=h_{m}(s_{2})]$為 $\mathcal{O}(\frac{1}{m})$
    - Proof
        - $\frac{1}{m}+\frac{L}{p}\lt \frac{1}{m}+\frac{L}{mL}=\frac{1}{m}+\frac{1}{m}=\frac{2}{m}=\mathcal{O}(\frac{1}{m})$

- Running Time
    - 當$p$足夠大時，(最長鏈的長度) $c=\mathcal{O}(1+\alpha)$
    - 計算PolyHash(S)的花費時間是 $\mathcal{O}(|S|)$
    - 如果名字的長度有被限制在常數$L$之內，則 $h(S)$ 僅花費 $\mathcal{O}(L)=\mathcal{O}(1)$

- 結論
    - 我們學會對整數及字串做雜湊運算了！
    - 一本電話簿可以用兩個hash table完成實作！
    - 需要將名字對應到號碼，也需要反過來根據號碼對應名字
    - 尋找(Searching)及更改(Modification)資料需要的平均時間是$\mathcal{O}(1)$

## Search Pattern in Text

- Description
    - Given a text T (book, website, facebook profile) and a pattern P (word phrase), find all occurrences of P in T.
    - Examples
        - Someone's name on a website
        - Twitter messages about a company
        - Detect file infected by virus (code patterns)

- 定義
    - Denote by $S[i..j]$ the substring of string $S$ starting in position $i$ and ending in position $j$.
    - Examples
        - If $S$ = "abcde", then
            - $S[0..4]$ = "abcde"
            - $S[0..3]$ = "bcd"
            - $S[2..2]$ = "c"
    - Input: String $T$ and $P$
    - Output: All such positions $i$ in $T$, $0\le i \le |T|-|P|$ that $T[i..i+|P|-1]=P$

- 初步實作
    - 對於每一個從$0$到$|T|-|P|$的position $i$，逐一檢驗$T[i..i+|P|-1]$是否成立，如果是便將$i$加入至結果中。
    - AreEqual($S_{1}$, $S_{2}$)
    ```=
    if |S_1| ~= |S_2|
        return False:
    for i from 0 to |S_1| - 1:
        if S_1[i] ~= S_2[i]:
            return Fasle
    return True
    ```
    - FindPatternNaive($T$, $P$)
    ```=
    result <-- empty list
    for i from 0 to |T| - |P|:
        if areEqual(T[i..i + |P| - 1], P):
            result.Append(i)
    return result
    ```
    
- Running time
    - ***Lemma***<br>Running time of FindPatternNaive($T$, $P$) is $\mathcal{O}(|T||P|)$
    - Proof
        - 每次執行AreEqual花費$\mathcal{O}(|P|)$
        - $|T|-|P|+1$次執行AreEqual則總共需要$\mathcal{O}((|T|-|P|+1)|P|)=\mathcal{O}(|T||P|)$
    - 糟糕的情況
        - 當$T$與$P$僅只有末一字元相異時，AreEqual仍然會被執行$|P|$次逐一比對，但兩者間差異僅在末端字元，因此這樣的實作演算法其時間變為$\mathcal{\Theta}(|T||P|)$

## Rabin-Karp's Algorithm

- 概念
    - Need to compare $P$ with all substrings $S$ of $T$ of length $|P|$
    - 理想上利用雜湊運算快速比較$P$與$T$的substrings

- 演算法
    - If $h(P) \neq h(S)$, then definitely $P \neq S$
    - If $h(P) = h(S)$, call AreEqual($P$, $S$)
    - Use Polynomial hash family $\mathcal{P}_{p}$ with prime number $p$
    - If $P \neq S$, the probability $Pr[h(P)=h(S)]$ is at most $\frac{|P|}{p}$ for polynomial hashing
    - RabinKarp($T$, $P$)
    ```=
    p <-- big prime, x <-- random(1, p-1)
    result <-- empty list
    pHash <-- PolyHash (P, p, x)
    for i from 0 to |T| - |P|:
        tHash <-- PolyHash(T[i..i + |P| - 1], p, x)
        if pHash ~= tHash:
            continue
        if AreEqual(T[i..i + |P| - 1], P):
            result.Append(i)
    return result
    ```

- False Alarms
    - The event when $P$ is compared with $T[i..i+|P|-1]$ but $P \neq T[i..i+|P|-1]$
    - 這樣的機率最高$\frac{|P|}{p}$
    - 平均而言Fasle alarm的總次數應為$(|T|-|P|+1)\frac{|P|}{p}$，而且能夠因為選用的質數$p\gg|T||P|$而變得更小

- Running Time without AreEqual
    - $h(P)$ 計算上需要 $\mathcal{O}(|P|)$
    - $h(T[i..i+|P|-1])$ 需要 $\mathcal{O}(|P|)$，實際運算 $|T|-|P|+1$ 次
    - $\mathcal{O}(|P|)+\mathcal{O}((|T|-|P|+1)|P|)=\mathcal{O}(|T||P|)$

- Running Time of AreEqual
    - 運算花費時間 $\mathcal{O}(|P|)$
    - 只有當$h(P)=h(T[i..i+|P|-1])$時才會執行，也就是說要嘛找到一次pattern $P$，要嘛出現false alarm
    - 透過選用$p\gg|T||P|$使得false alarm變得微不足道

- Total Running Time
    - if $P$ is found $q$ times in $T$, then total time spent in AreEqual is $\mathcal{O}((q+\frac{(|T|-|P|+1)|P|}{p})|P|)=\mathcal{O}(q|P|)$ for $p\gg|T||P|$
    - Total running time is $\mathcal{O}(|T||P|)+\mathcal{O}(|T||P|)$ as $q\le |T|$
    - 跟Naive比起來沒有好太多，但仍有進步空間。

## Optimization: Precomputation(Searching Patterns)

- 對String做雜湊運算
    $$
    h(S)=\sum_{i=0}^{|S|-1}S[i]x^{i} \text{ mod }p
    $$
    - 也就是說$h(T[i..i+|P|-1])$的表示如下：
    $$
    h(T[i..i+|P|-1])=\sum_{j=i}^{i+|P|-1}T[j]x^{j-i} \text{ mod }p
    $$
        - Idea: 對$T$中兩連續的substrings做polynomial hashing能得到非常相似的結果。
        - 對應各個$i$，將$h(T[i..i+|P|-1])$以$H[i]$表示
    - 範例<br>![](https://i.imgur.com/JdOg8pf.png)
    - 所以得到下列表示式：
    $$
    \begin{align}
    H[i+1]&=\sum_{j=i+1}^{i+|P|}T[j]x^{j-i-1} \text{ mod }p\\
        H[i]&=\sum_{j=i}^{i+|P|-1}T[j]x^{j-i} \text{ mod }p\\
        &=\sum_{j=i+1}^{i+|P|}T[j]x^{j-i}+T[i]-T[i+|P|]x^{|P|}\text{ mod } p\\
        &=x\sum_{j=i+1}^{i+|P|}T[j]x^{j-i-1}+(T[i]-T[i+|P|]x^{|P|})\text{ mod } p\\
        H[i]&=xH[i+1]+(T[i]-T[i+|P|]x^{|P|})\text{ mod } p
    \end{align}
    $$
    
- PrecomputeHashes($T$, $|P|$, $p$, $x$):
```=
H <-- array of length |T| - |P| + 1
S <-- T[|T| - |P|..|T| - 1]
H[|T| - |P|] <-- PolyHash(S, p, x)
y <-- 1
for i from 1 to |P|:
    y <-- (y * x) mod p
for i from |T| - |P| - 1 down to 0:
    H[i] <-- (xH[i+1] + T[i] - yT[i + |P|]) mod p
return H
```

- Asymptotic
    - PolyHash被呼叫一次：$\mathcal{O}(|P|)$
    - 第一個for loop：$\mathcal{O}(|P|)$
    - 第二個for loop：$\mathcal{O}(|T|-|P|)$
    - Total PrecomputeHashes: $\mathcal{O}(|P|+|P|+|T|-|P|)=\mathcal{O}(|P|+|T|)$

## Optimization: Implementation and Analysis(Searching Patterns)

- RabinKarp($T$, $P$)
```=
p <-- big prime, x <-- random(1, p-1)
result <-- empty list
pHash <-- PolyHash (P, p, x)
H <-- PrecomputeHashes(T, |P|, p, x)
for i from 0 to |T| - |P|:
    if pHash ~= H[i]:
        continue
    if AreEqual(T[i..i + |P| - 1], P):
        result.Append(i)
return result
```

- Improved Running Time
    - $h(P)$ is computed in $\mathcal{O}(|P|)$
    - PrecomputeHashes runs in $\mathcal{O}(|T|+|P|)$
    - Total time spent in Are Equal is $\mathcal{O}(q|P|)$ on average where q is the number of occurrences of $P$ in $T$
    - Average running time $\mathcal{O}(|T|+(q+1)|P|)$
    - Usually q is small, so this is much less than $\mathcal{O}(|T||P|)$

- 結論
    - 對於存取Sets及Maps而言，Hash table相當實用
    - 對Hash table的search與modify能夠在平均上只花費$\mathcal{O}(1)$
    - Hash function的選用上必須從好的family中隨機選取
    - 對於字串的處理Hash也有良好表現
    - 在分散式系統以及資料科學的領域中，Hash有更多的應用