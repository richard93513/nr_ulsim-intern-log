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

  - -1 表示不位移

  - 0 ~ Z−1 表示右移多少單位

- 每個位置可以有多個 shift 值，實際代表多條 check-variable 邊。

## 3. 壓縮矩陣建立與展開原理
### 壓縮矩陣建立
- OAI 原始碼將 BG1 拆為 8 個矩陣（BG1_I0 ~ BG1_I7），每個都是 46×68
  - 網址:https://gitlab.eurecom.fr/oai/openairinterface5g/-/tree/develop/openair1/PHY/CODING/nrLDPC_decoder_LYC/bgs
- 程式中將這 8 個矩陣逐個依欄位拼接，變成一個 46×544 的大矩陣：

```matlab

BG1_I0=[];
BG1_I1=[];
......
BG1_I7=[];
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
### 展開原理
- 每個 BG1(𝑖,𝑗) 位置實際包含 8 個 set index 對應的 shift 值

- 展開時，對每個 shift 值建立一個大小為 Z×Z 的單位矩陣，右循環位移指定次數

- 8個 shift 結果相加（mod 2）後，放入大矩陣的對應區塊中

- 最終生成的 parity check matrix 大小為：(46 × Z) × (68 × Z)

## 4. 展開程式解釋（expand_ldpc_H_multi_shift）
```matlab
function H_expanded = expand_ldpc_H_multi_shift(H_compact, Z)
% 展開 LDPC 壓縮矩陣（每個位置最多 8 個 shift）成完整 H 矩陣
%
% 輸入：
%   H_compact - 大小為 46×544 的矩陣（即 68×8，每個位置最多有 8 個 shift）
%   Z         - lifting size（如 Z = 384）
%
% 輸出：
%   H_expanded - 展開後的 (46×Z) × (68×Z) 的稀疏矩陣（全部由 0/1 組成）

    edges_per_pos = 8;  % 每個 base graph 位置最多儲存 8 個 shift（對應 8 條邊）

    [rows_bg, ~] = size(H_compact);  % rows_bg = 46，對應 base graph 的列數（檢查節點數）
    cols_bg = 68;  % 固定為 BG1 的欄數（變數節點區塊數）

    % 建立空的稀疏矩陣，大小為 (46×Z) × (68×Z)
    % 每個 base graph 的元素會被展開為一個 Z×Z 子矩陣
    H_expanded = sparse(rows_bg * Z, cols_bg * Z);

    % 外層迴圈：從第 1 列跑到第 46 列（對應 base graph 的行）
    for i = 1:rows_bg
        % 中層迴圈：從第 1 行跑到第 68 行（對應 base graph 的列）
        for j = 1:cols_bg
            % 取得這個位置上所有的 shift index（共最多 8 個）
            % H_compact(i, (j-1)*8+1 : j*8) 就是該位置的 8 個 shift 值
            shifts = H_compact(i, (j-1)*edges_per_pos + (1:edges_per_pos));

            % 初始化一個 Z×Z 的全零矩陣，準備把多個循環矩陣疊加起來
            submat = zeros(Z,Z);

            % 內層迴圈：最多 8 個 shift index，依序疊加對應的循環位移單位矩陣
            for k = 1:edges_per_pos
                shift_val = shifts(k);  % 取得第 k 條邊的位移量
                if shift_val >= 0 && shift_val < Z
                    % 建立一個右移 shift_val 的單位矩陣，並與 submat 做 XOR 疊加
                    submat = mod(submat + circshift(eye(Z), [0, shift_val]), 2);
                end
            end

            % 計算展開後這一塊 Z×Z 子矩陣在大矩陣中的 row 範圍
            row_idx = (i-1)*Z + (1:Z);
            % 計算展開後這一塊 Z×Z 子矩陣在大矩陣中的 column 範圍
            col_idx = (j-1)*Z + (1:Z);

            % 把組合好的 Z×Z 子矩陣放入對應的大矩陣區塊中
            H_expanded(row_idx, col_idx) = submat;
        end
    end
end

% 主程式開始
Z = 384;  % 設定 lifting size，必須 ≥ 最大 shift index 才合法（此例配合 5G NR BG1）

H = nr5g_ldpc_H_BG1_Z384();  % 載入 46×544 的 base graph shift index 矩陣（你自己寫的 function）

H_expanded = expand_ldpc_H_multi_shift(H, Z);  % 將壓縮的 shift index 矩陣展開為真正的 parity check matrix

% 顯示展開後矩陣的大小（應為 17664 × 26112）
fprintf("H_expanded size: %d x %d\n", size(H_expanded,1), size(H_expanded,2));

% 畫出展開後的稀疏矩陣結構圖
spy(H_expanded); 
title('展開後的 H 矩陣 (Z=384)');
```

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
    ```matlab
    H_expanded size: 17664 x 26112
    ```
  - nnz(H_expanded) 查看非零元素個數

  - spy(H_expanded) 觀察整體稀疏結構
     ![untitled](https://github.com/user-attachments/assets/d926ef89-ef2e-4594-8c39-05efe95adfae)

  - full(H_expanded(1:Z,1:Z)) 查看細節區塊

## 6. 結論
透過此方法，可以從 3GPP 的壓縮 shift 矩陣準確展開出 LDPC 解碼所需的 parity check 矩陣 H_expanded，
可用於後續 Min-Sum、BP 等解碼流程，符合 5G NR 實際實作需求。

# 2025/07/05 - LDPC 模擬流程嘗試紀錄

## 目標

建立一個模擬流程，驗證 5G NR LDPC Min-Sum 解碼器在 QPSK + AWGN 通道下的錯誤率表現，使用 Base Graph 1，固定 lifting size $Z = 384$。

---

## 一、模擬流程設計與初始嘗試

最初設計的流程包括：

1. 隨機產生資訊位元 `bits`；
2. 未實作編碼，直接補 0 模擬為 LDPC codeword；
3. 使用 QPSK 調變；
4. 添加 AWGN 雜訊；
5. 計算 soft LLR；
6. 使用展開後的檢查矩陣 `H` 執行 Min-Sum 解碼。

### 問題

該流程完全無法成功完成模擬。Min-Sum 解碼器無法收敗，無法通過 parity check，原因是輸入訊號未經 LDPC 編碼，因此與 `H` 不相容，造成解碼器無法有效運作。

---

## 二、嘗試加入 LDPC 編碼

進一步嘗試補上編碼流程，使用已展開之 `H_expanded_BG1_Z384`，進行下列操作：

* 手動撰寫 `ldpc_encode_simple()`，嘗試將資訊 bits 與 `H` 矩陣求 parity bits；
* 嘗試將 `sparse` 轉為 `full` 以使用 MATLAB `gf` 做行運算；
* 降低 SNR、傳輸區塊數、疊代次數。

### 問題

即使正確分離 `H = [H1 | H2]`，在進行 dense 矩陣運算求 parity bits 時，因 `H`為 17664×26112 的大矩陣，造成記憶體與速度環節，模擬程式執行數小時仍無法完成。

---

## 三、學習過程：QPSK LLR 與 Min-Sum 解碼器

儘管模擬流程上未成功執行，過程中釋清了下列關鍵模組運作方式：

### QPSK 調變與 LLR 計算

* 每兩個 bit 組成一個 symbol，使用 Gray Mapping 對應至 QPSK 星座點 {00→1+1j, 01→-1+1j, 10→1-1j, 11→-1-1j}，並除以 $\sqrt{2}$ 以正規化功率。

* 接收端收到一串複數符號 $r$，每個 symbol 對應兩個 bit，需針對每一個 bit 計算 soft LLR 值。

* 對於每個 bit，將所有 QPSK 星座點分為「該 bit = 0」與「該 bit = 1」的子集合，計算接收到的符號 $r$ 與各子集合內點之歐幾里得距離平方，分別取最小值：

  $$
  LLR_b = \min_{x: b=0} ||r - x||^2 - \min_{x: b=1} ||r - x||^2
  $$

* 此為 Max-Log approximation，提供每個 bit 的 soft decision 資訊供 LDPC 解碼使用。

### Min-Sum 解碼流程

* 首先以 channel LLR 初始化 bit node 對 check node 的輸出訊息（bit-to-check messages），矩陣大小與 parity-check matrix $H$ 相同。

* 每次疊代包含以下步驟：

  1. **check node 更新（CN）**：對每個 check node，針對其連接的每個 bit node，從其他相連的 bit node 接收的訊息中取「符號積」與「最小絕對值」，送出更新後的訊息給該 bit node。
  2. **bit node 更新（BN）**：對每個 bit node，將來自其他 check node 的訊息與原始 channel LLR 相加，更新送往各 check node 的訊息。
  3. **總 LLR 結合**：對每個 bit node，合併原始 LLR 與所有 check-to-bit 訊息，進行硬判斷（LLR < 0 判為 1，否則 0）。
  4. **檢查 parity check 是否滿足**：若所有檢查等式皆為 0，提早結束解碼。

* 此流程為典型 Min-Sum 解碼演算法，計算量較低但仍具備不錯的效能，適用於硬體與模擬實作。

---

## 四、重要結論與後續規劃

### 重要理解

* **LDPC 解碼器必須配合實際編碼過的 codeword 才能正確運作**。
* **隨機產生資料直接補 0 無法滿足 parity constraints，解碼器一定會失敗。**

### 後續方向

1. 改用 base graph 結構實作 encoder，避免操作展開矩陣；
2. 改用 lifting size $Z$ 較小的版本進行測試（如 $Z = 64$）；
3. 測試 OAI 中 `ldpctest` 的 codeword 產生方式，是否以標準流程驗證 LDPC 編碼器與解碼器功能；
4. 若無法成功實作 encoder，可考慮預先產生已知正確的 LDPC codeword 再行測試。

# 2025/07/07 - LDPC 模擬流程 實作與除錯紀錄

## 一、系統分工與模組化程式架構

整體實作分為兩個主要模組：發送端（Tx）與接收端（Rx），所有程序皆以 MATLAB 撰寫，並使用 NR Base Graph 1，lifting size 為 Z = 384。

### 1.1 發送端（Tx）流程

發送端包含以下步驟：

1. 產生隨機資訊位元 `tx_bits`，長度為 `K = 22 * Z`。
2. 使用 `ldpc_encode_neighbor()` 函數，透過鄰接表 `cn_to_vn` 對資訊進行編碼，產生長度為 `N = 68 * Z` 的 codeword。
3. 以 Gray 編碼方式進行 QPSK 調變，符號映射順序為：

   ```
   [1+1j, -1+1j, -1-1j, 1-1j] / sqrt(2)  對應 bit pair 為 00, 01, 11, 10
   ```

   使用 bi2de + Gray 索引法對應。
4. 通道加入 AWGN 噪聲，並以 SNR = 20 dB 模擬。
5. 接收端以最小距離近似法計算每個符號對應的 soft-bit LLR，轉為 `rx_llr`。
6. 儲存必要模擬資料至 `.mat` 檔：

   ```
   save(filename, 'rx_llr', 'tx_bits', 'snr_db');
   ```

### 1.2 接收端（Rx）流程

接收端包含以下步驟：

1. 載入通道輸出檔案 `ldpc_channel_output.mat`。
2. 使用鄰接表 `cn_to_vn`, `vn_to_cn` 建立解碼流程。
3. 執行 `ldpc_decode_min_sum_neighbor()` 以 Min-Sum 方法解碼。
4. 統計錯誤率與 parity check failure 數：

   ```
   n_err = sum(decoded(1:K) ~= tx_bits);
   ber = n_err / K;
   ```

---

## 二、轉用鄰接表的原因與動機

起初嘗試以展開後的 `H_expanded` 進行矩陣編碼，期望推導出 `H = [P | I]` 結構並導出系統式生成矩陣 `G = [I | Pᵗ]`。

但實作中遇到重大困難：

* `H_expanded` 為 17664×26112 的稀疏矩陣，對其進行矩陣分塊與秩判斷（如 `rank(P)`）時會出錯：

  ```
  Error using svd: SVD does not support sparse matrices.
  ```

* 強制轉為 full matrix 造成記憶體不足或效能過差。

因此決定**全面改用鄰接表方式**：

* 使用 `build_neighbor_tables(H)` 將 `H` 轉為 `cn_to_vn` 和 `vn_to_cn` 結構；
* 可節省大量記憶體，並利於建構解碼流程（例如 message passing）。

---

## 三、改用鄰接表後仍無法正確收斂

在轉用鄰接表後，整體 Min-Sum 解碼流程得以順利執行：

* 解碼函數 `ldpc_decode_min_sum_neighbor()` 可正常進行疊代（最大達 300 次）；
* 但觀察每輪的錯誤 bit 數與 parity check 數，皆呈現震盪且不收斂：

  ```
   Iteration 1: 8772
   Iteration 2: 8798
   Iteration 3: 8940
   ...
   Iteration 20: 8830
  ```

即使將 SNR 提升至 20dB，錯誤率仍然非常高（約 0.91），明顯與理論不符。

---

## 四、深入追查：發現是編碼錯誤而非解碼錯誤

為驗證編碼是否正確，使用下列方式檢查 codeword 是否滿足 parity：

```matlab
syndrome = mod(H * codeword, 2);
fprintf("\u2753 parity check failure: %d\n", sum(syndrome));
```

實際結果：

```
parity check failure: 8398
```

代表編碼產生之 codeword 並不合法，並未滿足 `H * cw = 0`。

---

## 五、嘗試修正與驗證方式

以下列出幾項曾經嘗試過但仍無效的方向：

### 5.1 檢查鄰接表正確性

使用 `check_neighbor_table_consistency()` 比對 `H_expanded` 與 `cn_to_vn`, `vn_to_cn`：

```
✅ 鄰接表與 H 非零元素數一致（798720 條邊）
✅ cn_to_vn 與 vn_to_cn 配對一致，鄰接表正確！
```

結論：鄰接表結構與 H 矩陣一致，問題非出於此。

### 5.2 Gray 編碼調變

原先 QPSK mapping 並非 Gray 順序，會使錯誤擴散。後來改為：

```matlab
symbol_map = [1+1j, -1+1j, -1-1j, 1-1j] / sqrt(2);
bit_decimal = bi2de(codeword_pairs, 'left-msb');
gray_index = bitxor(bit_decimal, floor(bit_decimal / 2)) + 1;
tx_symbols = symbol_map(gray_index).';
```

LLR 計算也需根據 gray\_bits 重新對應：

```matlab
gray_bits = [0 0; 0 1; 1 1; 1 0];
mask0 = gray_bits(:, b) == 0;
mask1 = gray_bits(:, b) == 1;
```

此改法提升解碼正確性，但無法解決根本問題（codeword 不合法）。

### 5.3 加入 Offset 與 Damping（解碼層面）

實驗中嘗試於 Min-Sum 加入 Offset 或 Damping，但 BER 仍維持在 0.91，說明問題出在傳送端。

---

## 六、分析 ldpc\_encode\_neighbor() 錯誤邏輯

```matlab
function codeword = ldpc_encode_neighbor(info_bits, cn_to_vn, N)
    K = length(info_bits);
    codeword = zeros(N, 1);
    codeword(1:K) = info_bits;

    for m = 1:length(cn_to_vn)
        idxs = cn_to_vn{m};
        parity = mod(sum(codeword(idxs)), 2);

        if parity ~= 0
            flipped = false;
            for j = 1:length(idxs)
                col = idxs(j);
                if col > K
                    codeword(col) = mod(codeword(col) + 1, 2);
                    flipped = true;
                    break;
                end
            end
            if ~flipped
                for j = 1:length(idxs)
                    col = idxs(j);
                    if col <= K
                        codeword(col) = mod(codeword(col) + 1, 2);
                        break;
                    end
                end
            end
        end
    end
end
```

此方法逐個 CN 校驗 parity，若不符則「反轉其中一個位元」。但該方法並無保證所有 CN 同時滿足，只是局部修正，導致整體仍不合法。

---

## 七、計畫改用系統式方式推導編碼器

後續預定採取以下方法：

1. 使用 `H_expanded` 建立 `H = [A | B]`，嘗試重排為 `H = [P | I]` 結構。
2. 若成功，則可導出 `G = [I | Pᵗ]`，並直接使用矩陣乘法產生合法 codeword。
3. 重新建立鄰接表，以對應重排後的 H。
4. 確保 parity check 完全正確後，再重新回到解碼模組驗證性能。

---

**結論：目前最大問題出在編碼端產生之 codeword 並不合法，應回歸原始 H 矩陣，以系統式方法設計 encoder，確保 parity check 正確。**

# 2025/07/09 - LDPC 模擬流程 實作與除錯紀錄

## 一、系統分工與模組化程式架構

本日延續 7/7 模擬流程，針對 5G NR LDPC Base Graph 1，lifting size Z=384，深入探討 H 矩陣結構，並優化編碼流程。模擬環境 MATLAB。  
本次重點為排查 H 矩陣結構錯誤、鄰接表編碼正確性及 parity 驗證。

---

## 二、今日詳細工作紀錄與心得

### 2.1 系統生成矩陣 G_sys 編碼嘗試與問題

- 初期嘗試用數學上傳統系統atic碼概念，將 H 拆成左半單位矩陣 I 與右半矩陣 P，利用  
  \[
  G_{sys} = [I, P^T]
  \]
  進行編碼。  
- 實務上因 P 非方陣且非一定可逆，且 5G NR LDPC Base Graph 的 H 矩陣稀疏且結構特殊，無法直接用此方法編碼。  
- 結果 parity bits 編碼不正確（非全 0），導致解碼失敗。  
- 這反映出 5G NR LDPC 的 H 矩陣結構與傳統 LDPC 不同，必須用更貼近規範的方式。

### 2.2 鄰接表編碼與點圖視覺化發現異常

- 使用鄰接表 `cn_to_vn` 及 `ldpc_encode_neighbor()` 函數進行編碼。  
- 利用 spy 或類似函數繪製展開後的 H 矩陣點圖（藍點代表 1）。  
- 與[linkedin.com/pulse/5g-nr-dl-sch-ldpc-channel-coding-base-graph-](https://www.linkedin.com/pulse/5g-nr-dl-sch-ldpc-channel-coding-base-graph-selection-chelikani)文章的標準點圖比較，發現右半部分（col 23Z~67Z）缺乏連接點。  
- 此問題意味著鄰接表中缺少某些 parity check 的約束邊，導致 parity 計算錯誤。

### 2.3 深入 H 矩陣結構分析與五部分區塊理解

- H 矩陣可拆分為五個區塊：  
  1. 左上資訊位元對應區（I 矩陣塊）  
  2. 核心 parity check 區（雙對角塊，主要控制 parity 約束）  
  3. 右下 parity bits 對應的單位矩陣（42 × Z）  
  4. 鄰接表連接情況（cn_to_vn 連線關係）  
  5. Lifting size Z 的一致性與行列匹配  
- 發現右下單位矩陣區塊缺失是導致錯誤的主因。

### 2.4 嘗試補齊右下單位矩陣區塊

- 補上右下角 42×Z 單位矩陣以解 parity bits，嘗試重新編碼。  
- Parity bits 仍非 0，代表尚未包含全部 parity 條件。

### 2.5 用矩陣分塊推導 parity bits 公式

- 按數學理論，令：  
  \[
  H = [H_1, H_2], \quad codeword = [info\_bits, parity\_bits]
  \]
- parity bits 可用：  
  \[
  parity\_bits^T = -H_2^{-1} H_1 info\_bits^T
  \]
- 但 H_2 是否可逆、數值條件是否穩定為疑問。  
- 實驗中 parity 計算仍不等於 0。

### 2.6 再次比對標準點圖，發現雙對角區塊遺漏

- 在官方及 LinkedIn 圖中，row 0~4Z 與 col 22~26Z 交界處有重要雙對角結構（shift=0 與 shift=45）  
- 本地展開矩陣中缺乏此結構，導致 parity 約束不完整。

### 2.7 補上雙對角區塊與最終驗證

- 將缺失雙對角區塊補齊，生成的 H_expanded 點圖與官方資料一致。  
---

## 三、學習心得與技術總結

- **5G NR LDPC H 矩陣結構複雜**，不能簡單用傳統系統atic編碼法。  
- **核心雙對角 parity check 結構不可忽略**，是 parity 約束完整性的關鍵。  
- **鄰接表結構須完整正確**，少一條邊就導致編碼錯誤。  
- **點圖視覺化是除錯重要工具**，幫助快速定位缺漏。  
- **矩陣分塊推導理論可行，但實務需確認矩陣可逆性與數值穩定性。**  
- **parity check bits 分布、H 矩陣 lifting 展開方式、雙對角結構意義**，對整體系統更深入理解。  
- 後續需繼續驗證 parity = 0 是否嚴格成立，並完成 Tx 端整合測試。

# 2025/07/11 - 5G NR LDPC 編碼與矩陣展開嘗試與問題排查

## 一、目標與背景  
延續前次 Base Graph 1 的 H 矩陣展開與 LDPC 編碼正確性驗證，lifting size Z=384。重點在確保 parity bits 計算正確與 syndrome 等於零。

---

## 二、步驟詳細紀錄

1. 發現 core parity 對角區塊存在重複填入  
檢視之前手動補上的 H 矩陣，發現雙對角 parity check 區域中部分元素數值為 2，mod 2 下等於 0，但代表重複累加。這可能導致 parity 約束錯亂及編碼錯誤。

2. 調整展開策略為先重疊 Base Graph 層，再展開  
不將 8 個 46×68 的 base graph 矩陣各自展開為 (46×384)×(68×384) 大矩陣後相加，改為先將這 8 個 base graph 位移量矩陣疊加合成一個 46×68 位移值矩陣，然後用此矩陣依 lifting size Z=384 生成大型循環展開矩陣。此方法生成的 H_expanded 與官方點圖一致，結構無誤。
<img width="676" height="526" alt="image" src="https://github.com/user-attachments/assets/33215b34-544d-4699-8912-60107e66f6ce" />

4. 編碼後 parity bits 仍非零  
利用展開矩陣乘以 codeword 進行 parity check，syndrome 不為零，說明 parity bits 計算仍有問題，編碼約束不完整。

5. 參考 OAI 編碼方式，避免直接使用展開矩陣  
決定嚴格沿用 OpenAirInterface 的 LDPC 編碼實作方式，不展開 H 矩陣為大型稀疏矩陣，而是直接使用鄰接表記錄邊連接關係及位移值，透過 bitxor 及 cyclic rotate 等邏輯運算直接計算 parity bits，不依賴矩陣乘法。

6. 展開矩陣僅用於 parity check 驗證  
展開的 H_expanded 矩陣只用來計算 syndrome 驗證 parity check 是否通過，實際編碼不使用該矩陣運算。

7. 重構符合 OAI 樣式的編碼函式，並整合位移值與鄰接表使用  

```matlab
function [codeword, c, d] = ldpc_encode_oai_style(input_bits, BG, Zc, ...
    no_shift_values, pointer_shift_values, Gen_shift_values, ...
    nrows, ncols, Kb, no_punctured_columns, removed_bit)

    %=== 初始化參數 ===%
    block_length = length(input_bits);
    c = zeros(Kb * Zc, 1);         % 資訊區（包含前導 2Z）
    d = zeros(nrows * Zc, 1);      % parity bits 區

    % 將 input_bits 填入 c (從第 2Z 開始)
    c_idx = (2 * Zc + 1):(2 * Zc + block_length);
    c(c_idx) = input_bits;

    %=== parity check 部分 ===%
    for i2 = 1:Zc
        % 對每一欄進行 cyclic rotate（輪轉）
        for i5 = 1:Kb
            temp = c((i5 - 1) * Zc + 1);
            c((i5 - 1) * Zc + 1 : i5 * Zc - 1) = c((i5 - 1) * Zc + 2 : i5 * Zc);
            c(i5 * Zc) = temp;
        end

        % 利用鄰接表及位移值計算 parity bits
        for i1 = 1:nrows - no_punctured_columns
            channel_temp = 0;

            % 每個 row 依鄰接表 no_shift_values 決定參與 bitxor 的 bits 數量
            for i3 = 1:Kb
                temp_prime = (i1 - 1) * ncols + (i3 - 1);
                for i4 = 1:no_shift_values(temp_prime + 1)
                    shift_idx = pointer_shift_values(temp_prime + 1) + i4 - 1;
                    shift_val = Gen_shift_values(shift_idx);
                    index = (i3 - 1) * Zc + mod(shift_val, Zc) + 1;
                    channel_temp = bitxor(channel_temp, c(index));
                end
            end

            d(i2 + (i1 - 1) * Zc) = channel_temp;
        end
    end

    % 輸出完整 codeword，包含資訊區（含 dummy bits）及 parity bits
    codeword = [
        c(1 : Kb * Zc);           % 全部 22Z，包括前導 dummy bits
        d(1 : nrows * Zc)         % 全部 46Z parity bits
    ];
    fprintf("✅ 最終輸出完整 codeword 長度: %d（預期應為 %d）
", length(codeword), (Kb + nrows) * Zc);
end
```

此函式自動將 input_bits（長度 K=20×Z）填入包含 2×Z 前導 dummy bits 的 c 陣列，然後依鄰接表與位移值做 cyclic rotate 與 bitxor 計算 parity bits，最後輸出長度為 68×Z 的完整 codeword。

目前整合主程式時：

- 確認傳入 `input_bits` 長度為 20×Z。
- `ldpc_encode_oai_style` 自動補 2×Z 0 的 dummy bits 並編碼。
- 輸出完整 codeword 長度為 68×Z。
- QPSK 調變、AWGN 通道與 LLR 計算均以 68×Z 為長度。
- 用 `H_expanded` 乘以 codeword 計算 syndrome，理論上此處的矩陣乘法正確，且 syndrome 的長度為 parity check 數量（46×Z）。

**但目前計算出的 syndrome 仍不為 0，表示 parity bits 尚未完美符合約束條件。**

---

## 三、總結  
- 5G NR LDPC 的 H 矩陣結構複雜，直接大矩陣展開後用矩陣乘法編碼不易正確。  
- 先疊加 base graph 層後展開矩陣，可確保展開矩陣結構正確。  
- 編碼時沿用 OAI 風格鄰接表與位移值計算，避免展開大矩陣。  
- 編碼函式會自行補 dummy bits，輸出完整 68×Z 長度 codeword。  
- 調變、通道、LLR 計算皆基於完整 codeword，流程合理。  
- syndrome 應為 0 以證明 parity bits 正確，但目前不為 0，代表 parity bit 計算仍有誤。  
- 後續需進一步排查鄰接表資料正確性、位移值使用細節與 cyclic rotate 運算的細節。

---

## 四、後續建議與可能方向

- **仔細核對鄰接表與位移值資料**  
  確認 `no_shift_values`、`pointer_shift_values`、`Gen_shift_values` 與鄰接表 `cn_to_vn` 的內容，確保沒有遺漏或錯位，特別是鄰接表的完整性與對應關係。

- **詳細檢查 cyclic rotate 運算邏輯**  
  `ldpc_encode_oai_style` 中的位元輪轉（rotate）是否符合 5G NR 規範，特別是輪轉方向和輪轉位置索引，是否與標準相符。

- **利用單個 codeword block 逐步偵錯**  
  測試極簡化輸入（如全零、全一、單一 bit 置 1）並觀察 parity bits 與 syndrome 的變化，確認編碼流程邏輯。

- **嘗試使用鄰接表進行 parity check 運算**  
  不僅用展開矩陣做 syndrome 檢查，也可用鄰接表結合位移值用 bitxor 方式手動計算 syndrome，確認兩者是否一致。

- **參考 OpenAirInterface 最新版本源碼**  
  深入對照 OAI 專案的 `ldpc_encoder.c` 實現與參數，檢查自己寫法與其差異。

- **逐步加強單元測試與視覺化工具**  
  使用 spy、heatmap 等繪圖工具驗證矩陣結構，用詳細 log 或斷點檢查計算過程。

- **考慮動態偵錯工具或模擬器**  
  嘗試借助 LDPC 解碼器輸出錯誤指標，甚至在仿真中注入已知錯誤位元，驗證編碼正確性。

# 2025/07/015 - LDPC 模擬流程 實作與除錯紀錄

## 1. 起點：用 OAI 系統模擬驗證 syndrome 失敗

我一開始用 OpenAirInterface（OAI）系統的 LDPC 編碼器來做模擬，發現 syndrome 驗證一直不正確，錯誤率很高。雖然 OAI 裡面有完整的編碼與解碼流程，但我對其中的細節並不完全理解，尤其是 LDPC 編碼器如何計算 parity bits、整體 codeword 結構是什麼樣子。

## 2. 對 OAI 編碼流程的理解與拆解

為了釐清問題，我開始拆解 OAI LDPC 編碼器的實作流程，嘗試找到 parity bits 計算與 syndrome 驗證的關鍵。

- 了解到 codeword 總長是 30Z，其中：
  - 前 2Z 是 dummy bits (0)
  - 接著 20Z 是原始資訊位元
  - 接著 4Z 是 core parity bits
  - 最後 4Z 是 parity bits（不完全是 core parity bits）

我嘗試將原本的 H 矩陣（46×68 的 base graph 矩陣展開）縮減，只保留用於編碼的部分（前 4 行 × 26 列），原因是我只用到資訊與 parity bits 對應的子矩陣。

## 3. 只用 4×26Z 的編碼區塊，重新實作 LDPC 編碼器

基於上述理解，我實作了新的編碼器流程：

- 初始化一個長度為 26Z 的向量 c，包含 dummy bits、原始資訊 bits、core parity bits。
- 原始資訊 bits 從 c 的第 2Z+1 位開始填入。
- 使用 cyclic shift 與 XOR 運算，計算 core parity bits（欄位 23~26）：
  - 利用儲存的位移資訊（Gen_shift_values、no_shift_values、pointer_shift_values）計算 XOR 和，類似矩陣乘法中乘以循環移位矩陣。
- 接著再做 cyclic shift，計算整體 parity bits（4Z），放入 d 向量。
- 最終輸出 codeword = [c; d]，長度為 30Z。

程式碼片段示意：

```matlab
% 填入資訊 bits
c((2*Zc+1):(2*Zc+K)) = input_bits;

% 計算 core parity bits
for row = 1:4
    parity_bits = zeros(Zc,1);
    for col = 1:22
        % 根據位移資訊做 XOR 計算
    end
    c(core_parity_idx) = parity_bits;
end

% cyclic shift 與 parity bits 計算
for z = 1:Zc
    cyclic_shift(c);
    for row = 1:4
        parity_bit = 0;
        for col = 0:25
            % 根據位移做 XOR parity 計算
        end
        d(parity_bit_idx) = parity_bit;
    end
end

codeword = [c; d];
```

## 4. LDPC 編碼驗證的困惑與探索

我在驗證編碼正確性時遇到問題：

- 傳統使用 syndrome = c * H' (mod 2) 驗證 syndrome 是否為 0，但因為我用的是分段矩陣結構，不能直接用整個 H 矩陣乘法。
- 我知道 core parity bits 是用 H 的前 4 行 × 22 列子矩陣乘以資訊 bits 計算得來。
- 後面 parity bits 的計算是利用公式：p = B^{-1} * A * m，其中 B 與 A 分別是 H 矩陣拆分出來的子矩陣，p 是 parity bits，m 是資訊 bits。

## 5. 驗證方法的嘗試

我將 codeword 分成：

- c 部分：包含原始資訊 bits (22Z) 與 core parity bits (4Z)，共 26Z。
- d 部分：最後 4Z parity bits。

驗證步驟：

1. 用 H 前 4 行，對 c 前 26Z 做驗證：H_check * c ≡ 0 (mod 2)
2. 用 B^{-1} A 反推公式對 parity bits d 做驗證，看與計算結果是否相符。

程式碼示意：

```matlab
% 取 H 矩陣子區域
H_check = [P I];  % P: 4×22Z, I: 4×4Z 單位矩陣

% syndrome 驗證
syndrome = mod(H_check * c, 2);
assert(all(syndrome == 0));

% parity bits 驗證
p_calc = mod(B_inv * A * m, 2);
assert(all(p_calc == d));
```

## 6. 總結與後續

目前我仍在嘗試正確寫出驗證程式，避免直接用完整 H 矩陣乘法，而是分段利用矩陣拆分、循環移位與 XOR 計算，對 core parity 與 parity bits 做分別驗證。

這樣的學習過程讓我：

- 理解 5G NR LDPC 的編碼結構與子矩陣劃分
- 知道 cyclic shift 與 XOR 可視為矩陣乘法的簡化實現
- 掌握了編碼與驗證需分段進行，因為 parity bits 有反運算複雜度

後續會再繼續釐清驗證細節，並嘗試撰寫更嚴謹的驗證碼。

---
