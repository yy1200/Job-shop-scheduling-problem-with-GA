## Problem Set：Demirkol’s instances.
https://optimizizer.com/DMU.php

**題目描述**：
第一行分別為**工作數**及**機器數**，接下來每一列代表一個工作的排程順序，其中每兩個數字為一組，表示該工作在特定機器上的處理時間。

例如，第一列 "3 64 9 24 10 51 4 114 1 80 12 110 2 63 11 136 0 53 8 182 5 134 14 125 7 62 6 58 13 177" 可以解讀為：

工作 0：機器 3（處理時間 64） → 機器 9（處理時間 24） → 機器 10（處理時間 51） → 機器 4（處理時間 114） → 機器 1（處理時間 80） → 機器 12（處理時間 110） → 機器 2（處理時間 63） → 機器 11（處理時間 136） → 機器 0（處理時間 53） → 機器 8（處理時間 182） → 機器 5（處理時間 134） → 機器 14（處理時間 125） → 機器 7（處理時間 62） → 機器 6（處理時間 58） → 機器 13（處理時間 177）

以此類推，每一列的數字對應不同工作在各個機器上的處理時間。

## 基因演算法(GA)
首先藉由我們設計的編碼方式得出具隨機性質的初始解，以加速GA的收斂速度，接著透過二元競爭式選擇法挑選父代，以單點交配法產生子代，突變則是隨機選擇兩點進行交換。經過python套件—optunity優化最佳參數(交配率及突變率)，最後生成一組代表Job加工先後順序的最佳解。

## Flexsim建模
The Optimal Solution：將GA求出的解轉換為GA_solution，表依每台Workstation的Job加工順序排序。在各Buffer設定優先順序，並於各Workstation設計開關控制工件能依正確的加工順序處理。
首先新增兩個table：
1.	GA_solution (20x15) -- 列出Job在每個Workstation的加工順序。
2.	Sequence (1x15) -- 用來存取各個Workstation分別加工到第幾個Job。
    1. 在Workstation的Onreset設定一開始為關。
    2. 若Buffer裡SendMessenge條件成立，表示GA_solution中的下一個該做工件已在等候，進而觸發OnEntry讓Sequence的該儲存格加1，使模型能繼續判斷下一個加工順序。
    3. 當離開Workstation時，OnExit會判斷Buffer中最高rank的工件是否是下一個該加工的工件，如果是就打開Workstation，反之則關起來。
    4. Buffer裡若有多個工件，以setrank函數排序，使工件依加工順序正確排列。

## 排程結果之分析
The Optimal Solution：為GA算出的最佳解，經過排除模擬模型中的各種錯誤後可得出題目最佳Makespan：3004。
觀察rcmax的前三組數據可發現20_15_8與目標GAP值相差最小，從大小排序與機台利用率排序得知兩者並無關連，因GAP值為Workstation間的利用情況，而機台利用率為單一Workstation中的利用情況。即便兩者沒有直接的因果關係，但要得出距離目標更近的GAP值仍需讓機台利用率平均分配。
另外，因cscmax問題較困難，故所求makespan較不理想，未來期望嘗試使用不同方法以縮短總完工時間。

![image](https://github.com/yy1200/Job-shop-scheduling-problem-with-GA/assets/92247082/e6cee0fd-7e45-45e6-960f-b296f6d263a8)
![image](https://github.com/yy1200/Job-shop-scheduling-problem-with-GA/assets/92247082/e3de3300-867d-4a7d-8c0b-8e3666996c65)
