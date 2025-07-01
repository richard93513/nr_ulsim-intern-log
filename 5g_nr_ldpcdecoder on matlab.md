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
