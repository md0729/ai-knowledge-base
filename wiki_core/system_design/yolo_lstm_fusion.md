# 🧠 Multi-Modal Fusion: YOLO + LSTM for Reliable Interaction Detection

## 📌 概念摘要（Summary）

在智慧製造或人機協作場景中，僅依賴單一模型（如 YOLO）進行事件判斷容易產生誤判。因此本系統結合 YOLO（空間資訊）與 LSTM（時間序列資訊），透過多模態融合提升判斷準確性與穩定性。

---

## ⚠️ 問題描述（Problem）

使用 YOLO 偵測料盤與手部位置時，可能出現以下問題：

* 手靠近即被判定為接觸
* 無法判斷是否「真正執行動作」
* 遮擋導致錯誤判斷

👉 YOLO 缺乏動作語意（action semantics）

---

## 🔍 原因分析（Analysis）

### 1. YOLO 的限制

* 僅提供 bounding box（空間資訊）
* 無時間維度
* 無法理解動作過程

---

### 2. 動作本質為時間序列

例如「取料」：

👉 靠近 → 接觸 → 抬起 → 離開

👉 必須觀察「變化過程」，而不是單一畫面

---

## 🛠 解決方法（Solution）

### ✔ 多模態融合設計

| 模組        | 功能           |
| --------- | ------------ |
| YOLO      | 偵測料盤位置（空間）   |
| MediaPipe | 提供手部關鍵點      |
| LSTM      | 判斷動作是否成立（時間） |

---

### ✔ 判斷邏輯

1. YOLO 偵測 tray
2. 判斷 hand 是否進入區域（contact）
3. 將 keypoints 輸入 LSTM
4. 若 LSTM 判定為「take」→ 觸發事件

---

## 🔧 實作細節（Implementation）

### Feature 設計

* 84 維（雙手 keypoints）
* window size = 60

---

### Decision Strategy

* YOLO → 提供候選事件
* LSTM → 驗證事件是否成立

👉 類似「二階段驗證機制」

---

## 📝 注意事項（Notes）

* 若 LSTM 準確率不足，整體系統仍會誤判
* 多模態會增加計算成本
* 需處理不同模組的同步問題（timing issue）

---

## 🔗 關聯知識（Related Concepts）

* Multi-modal Learning
* Action Recognition
* State Machine
* Sensor Fusion

---

## 📚 資料來源（Sources）

* 自身專案設計（YOLO + MediaPipe + LSTM）
* 系統整合測試經驗

---

## 🚀 未來優化方向（Future Work）

* 使用 Transformer 取代 LSTM
* 加入 force sensor（力覺）做三模態融合
* 動態權重融合（adaptive fusion）
