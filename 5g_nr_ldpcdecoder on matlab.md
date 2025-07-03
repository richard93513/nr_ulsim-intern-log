# 5G NR LDPC Decoder — From OAI to MATLAB

## 研究背景

為深入理解 **3GPP 5G NR** 規範中的 LDPC 解碼流程，我從 OpenAirInterface (OAI) 開源專案中的 `nrLDPC_decoder.c` 進行分析，並嘗試用 MATLAB 重建解碼流程，以驗證與學習演算法細節。

---

## 一、OAI 原始碼分析重點

- 主要檔案：
  - `nrLDPC_decoder.c`：LDPC 解碼主程式
  - `ldpctest.c`：測試程式，含隨機資料產生、編碼、調變、AWGN 通道、解碼及錯誤率計算

- 解碼核心流程：
  1. 初始化（選擇 BaseGraph BG1/BG2、lifting size Z、碼率等）
  2. Min-Sum 迭代解碼
     - CN（Check Node）訊息更新
     - BN（Bit Node）訊息更新
     - Parity check 驗證
     - 硬判決（hard decision）
     - 停止條件判斷（CRC 或最大迭代數）
  3. SIMD 加速（AVX512 / AVX2 / baseline）

---

## 二、從 OAI 流程推導 MATLAB 實作

### 1. 隨機輸入產生

```matlab
N = 8448;  % 範例 Transport Block Size
input_bits = randi([0 1], N, 1);
```
### 2. 選擇 BaseGraph (BG1 / BG2)
- 根據 3GPP TS 38.212 規定，以資訊長度與碼率判定：

  - BG1 適用於資訊長度大於或等於 3840 bits

  - BG2 適用於較短資訊長度

### 3. 建構 H_base 矩陣
- H_base 為 LDPC 矩陣的基礎結構，尺寸例：

  - BG1：46 x 68 base matrix

  - 每個元素為 lifting size Z 的右循環移位量，-1 表示零矩陣（空白）

- 依照 3GPP 標準表格，將各 row 與 column index 對應的偏移值填入，形成整體解碼骨架。
- 我成功完成了 BG1 第 0 行的 MATLAB 程式碼建構，範例如下：
```matlab
clear; clc;
% 所有 column index（row=0 資料中有資料的那些）
col_index_list = [0, 1, 2, 3, 5, 6, 9, 10, 11, 12, 13, 15, 16, 18, 19, 20, 21, 22, 23];

% 每個 column index 對應的 set index（shift 數值，8 個一組）
col_shift_values = [
    250, 307,  73, 223, 211, 294,   0, 135;   % col 0
     69,  19,  15,  16, 198, 118,   0, 227;   % col 1
    226,  50, 103,  94, 188, 167,   0, 126;   % col 2
    159, 369,  49,  91, 186, 330,   0, 134;   % col 3
    100, 181, 240,  74, 219, 207,   0,  84;   % col 5
     10, 216,  39,  10,   4, 165,   0,  83;   % col 6
     59, 317,  15,   0,  29, 243,   0,  53;   % col 9
    229, 288, 162, 205, 144, 250,   0, 225;   % col 10
    110, 109, 215, 216, 116,   1,   0, 205;   % col 11
    191,  17, 164,  21, 216, 339,   0, 128;   % col 12
      9, 357, 133, 215, 115, 201,   0,  75;   % col 13
    195, 215, 298,  14, 233,  53,   0, 135;   % col 15
     23, 106, 110,  70, 144, 347,   0, 217;   % col 16
    190, 242, 113, 141,  95, 304,   0, 220;   % col 18
     35, 180,  16, 198, 216, 167,   0,  90;   % col 19
    239, 330, 189, 104,  73,  47,   0, 105;   % col 20
     31, 346,  32,  81, 261, 188,   0, 137;   % col 21
      1,   1,   1,   1,   1,   1,   0,   1;   % col 22
      0,   0,   0,   0,   0,   0,   0,   0    % col 23
];

% 初始化 H_base_row 為 -1
H_base_row = -1 * ones(1, 544);  % 68*8

% 將有定義的 shift 填入對應位置
for k = 1:length(col_index_list)
    col = col_index_list(k);
    shifts = col_shift_values(k, :);  % 對應的 shift 數值
    for i = 0:7
        idx = col * 8 + i + 1;  % MATLAB index 起始為 1
        H_base_row(idx) = shifts(i+1);
    end
end
```
- col_index_list：只列出這行中有定義的 column index（非空白）

- col_shift_values：每個 column 下，8 個 set index 的 shift 值（對應 5G NR LDPC base graph 裡的 cyclic shift）

- H_base_row：代表該行對應完整 68 個 column，每個 column 有 8 個 set index，所以長度是 68*8=544
沒定義的位置用 -1 表示空白（無邊連接）

- for 迴圈：依照 column 和 set index 計算位置，放入對應的 shift 值
### 4. Min-Sum 解碼架構
```matlab
% 初始化訊息
LLR = received_llr;  % 從通道輸入的 log-likelihood ratio

for iter = 1:max_iterations
    % CN 更新
    % BN 更新
    % 硬判決並 parity check
    if parity_check_passed
        break;
    end
end
```
## 三、H_base 建構細節
- 原始資料以 row 與 column 為索引，對應多個 set index (0~7)

- 透過 MATLAB 將每個 row 與 column 下的 set index 及偏移量映射至完整的 parity check matrix

- 利用 lifting size Z 將 base matrix 擴展成完整大小

## 四、整體解碼流程圖

![Untitled diagram _ Mermaid Chart-2025-07-01-181634](https://github.com/user-attachments/assets/a6e66335-e354-471e-b0df-d113e55a3106)

## 五、心得與未來規劃
- H_base 是理解與實作 LDPC 解碼最關鍵的部分

- 由 3GPP 標準細節整理 base graph，搭配 MATLAB 可還原整體解碼流程

- 後續計畫：

  - 完整展開 lifting 矩陣並驗證解碼正確性

  - 加入 CRC 早停機制

  - 實作 BER 測試並與 OAI 結果比對

  - 探索 SIMD 加速在 MATLAB 的模擬可能性

# 5G NR LDPC Base Graph 1 擴展筆記
## 1. Base Graph BG1 結構
- 5G NR 中的 LDPC 使用兩種 Base Graph（BG1 與 BG2）。

- 本次研究的是 BG1，其矩陣大小為：
  46 × 68
  表示有 46 個 check node 區塊、68 個 variable node 區塊。

## 2. Set Index 與位移量的理解
- 在 3GPP 規格（如 TS 38.212）中，BG1 的每個元素最多包含 8 條邊（Set Index 0~7）。

- 每個 set index 對應一個位移量，為一個整數：

  - -1 表示該邊不存在

  - 0 ~ Z−1 表示右移多少單位

- 每個位置可以有多個 shift 值，實際代表多條 check-variable 邊。

## 3. 壓縮矩陣建立與展開原理
壓縮矩陣建立（程式）
- OAI 原始碼將 BG1 拆為 8 個矩陣（BG1_I0 ~ BG1_I7），每個都是 46×68

- 程式中將這 8 個矩陣逐個依欄位拼接，變成一個 46×544 的大矩陣：

``matlab

for row = 1:46
    for col = 1:68
        for block = 0:7
            varName = sprintf('BG1_I%d', block);         % 組出變數名 BG1_Ix
            H_col = (col - 1)*8 + block + 1;              % 計算壓縮矩陣的欄位位置
            H(row, H_col) = eval([varName '(' num2str(row) ',' num2str(col) ')']);
        end
    end
end
```
- 結果矩陣 H 即為壓縮格式，大小為：
46 × 544  = 46 × (68×8)
展開原理
- 每個 BG1(𝑖,𝑗) 位置實際包含 8 個 set index 對應的 shift 值

- 展開時，對每個 shift 值建立一個大小為 Z×Z 的單位矩陣，右循環位移指定次數

- 多個 shift 結果相加（mod 2）後，放入大矩陣的對應區塊中

- 最終生成的 parity check matrix 大小為：(46 × Z) × (68 × Z)

## 4. 展開程式解釋（expand_ldpc_H_multi_shift）
函式輸入：

- H_compact：46×544 的壓縮 shift 矩陣

- Z：lifting size（如 Z = 384）

函式輸出：

- H_expanded：實際解碼用的 LDPC parity check matrix，大小為 (46×Z) × (68×Z)

主要流程：

1.對每個 base graph 元素提取其 8 個 shift 值

2.每個 shift 值產生一個右移的單位矩陣

3.多個 shift 結果進行 XOR 疊加

4.放入展開後矩陣的對應 Z×Z 區塊位置

5.全部以 sparse 格式儲存，節省記憶體

## 5. 檢查與驗證
- 展開後的矩陣大小為：
17664 × 26112（對應 Z = 384）

- 可使用以下方式確認正確性：

  - size(H_expanded)

  - nnz(H_expanded) 查看非零元素個數

  - spy(H_expanded) 觀察整體稀疏結構

  - full(H_expanded(1:Z,1:Z)) 查看細節區塊

## 6. 結論
透過此方法，可以從 3GPP 的壓縮 shift 矩陣準確展開出 LDPC 解碼所需的 parity check 矩陣 H_expanded，
可用於後續 Min-Sum、BP 等解碼流程，符合 5G NR 實際實作需求。
