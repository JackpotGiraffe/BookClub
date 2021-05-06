# Data Structures Week 5

## Objective
    - Describe how balanced binary search trees work
    - Analyze the running time of operations with binary search trees
    - List the capabilities of binary search trees
    - Compare balanced binary search trees with arrays and lists

- 學習目標
    - 描述平衡二元搜尋樹的運作細節
    - 分析BST各項操作的花費時間
    - 表列BST能夠帶來的功能及效益
    - 將BST與基本的Arrays及Lists做比較

# Introduction

- Local Search
    - Dictionary Search: 給定字串做為條件，查找以該字串為開頭的一定數量(或全部)單字。
    - Data Ranges: 根據時間區間做為條件過濾信件。
    - Closest Height: 在一群人中(例如班級)找出身高與自己最接近的對象。
    - Definition:
        - Local Search Datastructure儲存一定量的排序資料並且對應各自鍵值，其能夠支援以下操作：
            - RangeSearch(x, y): 回傳鍵值介於x與y之間的所有資料。
            - NearestNeighbors(z): 根據鍵值z回傳最靠近其的兩側資料(前、後)。
            - Example<br>![](https://i.imgur.com/FlyYcBW.png)
        - 實務上我們期望這樣的資料結構是動態的，所以事實上更佳的設想是包含以下兩項操作：
            - Insert(x): 新增鍵值為x的元素。
            - Delete(x): 根據鍵值x移除對應的元素。
            - Example<br>![](https://i.imgur.com/A1ArIEp.png)
        - Exercise<br>Which number(s) are returned by the sequence of queries below?<br>Insert(3)<br>Insert(8)<br>Insert(5)<br>Insert(10)<br>Delete(8)<br>Insert(12)<br>NearestNeighbors(7)
            ans. 答案

- Attempts
    - 用已知的不同資料結構實作：
        - **Hash Table**<br>![](https://i.imgur.com/5E3wdiQ.png)
            - RangeSearch: Impossible (幹！)
            - NearestNeighbors: Impossible (幹！)
            - Insert: $\mathcal{O}(1)$ (讚！)
            - Delete: $\mathcal{O}(1)$ (讚！)
        - **Array**<br>![](https://i.imgur.com/ieFTXrT.png)
            - RangeSearch: $\mathcal{O}(n)$ (爛！)
            - NearestNeighbors: $\mathcal{O}(n)$(　爛！)
            - Insert: $\mathcal{O}(1)$ (讚！)
            - Delete: $\mathcal{O}(1)$ (讚！)
        - **Sorted Array**
            - RangeSearch: $\mathcal{O}(\text{log}(n))$ (超讚！)
            - NearestNeighbors: $\mathcal{O}(\text{log}(n))$ (超讚！)
            - Insert: $\mathcal{O}(n)$ (爛！)
            - Delete: $\mathcal{O}(n)$ (爛！)
        - **Linked List**
            - RangeSearch: $\mathcal{O}(n)$ (爛！)
            - NearestNeighbors: $\mathcal{O}(n)$ (爛！)
            - Insert: $\mathcal{O}(1)$ (讚！)
            - Delete: $\mathcal{O}(1)$ (讚！)
    - Problem
        - 上述資料結構都不充具備用來實作local search的價值。<br>**We need something new!**

## (Binary)Search Tree

- 本節目標
    - 描述BST的結構如何建立。
    - 確認一棵樹是否有適當排序。

- 前節回顧
    - 我們需要適當的資料結構以實作local search。
    - 大部分已知的資料結構都不組以完美地滿足需求。
    - 其中sorted array雖然能夠相當容易地做搜尋，卻無法輕鬆更新內容。