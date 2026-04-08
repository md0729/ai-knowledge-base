# 🧠 LSTM Window Size Design for Hand Gesture Recognition

## 📌 概念摘要（Summary）

在基於 LSTM 的手勢辨識系統中，window size（時間序列長度）是影響模型表現與系統延遲的關鍵參數。適當的 window size 能夠在「辨識準確率」與「即時性」之間取得平衡。

---

## ⚠️ 問題描述（Problem）

在實作手勢辨識時，需將連續影像轉換為固定長度的序列（例如 60 frames）作為 LSTM 輸入。然而：

* window 太小 → 無法完整捕捉動作
* window 太大 → 系統延遲增加

因此需設計合適的 window size。

---

## 🔍 原因分析（Analysis）

### 1. 動作時間特性

手勢（如「取料」）通常包含完整流程：

👉 靠近 → 接觸 → 拿起 → 離開

若 window 無法涵蓋完整過程，模型將難以判斷動作語意。

---

### 2. 時間序列資訊

LSTM 依賴時間上下文：

* 短序列 → 資訊不足（underfitting）
* 長序列 → 雜訊增加（noise accumulation）

---

### 3. 系統延遲（Latency）

window size 直接影響延遲：

👉 latency ≈ window_size / FPS

例如：

* 60 frames / 30 FPS ≈ 2 秒延遲

---

## 🛠 解決方法（Solution）

### ✔ 建議策略

1. **以動作完整性為優先**

   * 確保 window 能涵蓋完整動作

2. **搭配 Sliding Window**

   * 每幀都做預測（stride < window）
   * 降低等待時間

3. **實驗比較不同長度**

   * 30 / 45 / 60 frames 進行測試

---

## 🔧 實作細節（Implementation）

### Input 設定

* shape: (window_size, 84)
* 84 = 21 keypoints × 2（x,y）× 2 hands

---

### 推薦設定（你的專案）

* window_size = 60（目前最佳平衡）
* stride = 1~5（即時性 vs 計算量）
* padding：不足時補零（搭配 attention mask 更佳）

---

### 即時推論方式

```python
buffer.append(frame_keypoints)

if len(buffer) >= window_size:
    input_seq = buffer[-window_size:]
    prediction = model(input_seq)
```

---

## 📝 注意事項（Notes）

* 不同動作需不同 window size（取 vs 放 vs idle）
* 使用者速度不同會影響最佳設定
* 建議搭配機率平滑（probability smoothing）避免抖動
* 過大 window 會導致「反應遲鈍」

---

## 🔗 關聯知識（Related Concepts）

* Time Series Modeling
* Sliding Window Technique
* Real-time Inference
* Latency vs Accuracy Trade-off
* Human Motion Analysis

---

## 📚 資料來源（Sources）

* 自身專案實驗（YOLO + MediaPipe + LSTM）
* 實際部署測試結果

---

## 🚀 未來優化方向（Future Work）

* 使用 Transformer 替代 LSTM（處理長序列）
* 加入 attention mask（改善 padding 問題）
* 動態 window size（依動作調整）
* Early prediction（提前預測動作）
