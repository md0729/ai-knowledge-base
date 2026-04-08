# 🧠 MediaPipe Hand Detection Instability in Real-world Scenarios

## 📌 概念摘要（Summary）

在使用 MediaPipe Hands 進行手部關鍵點偵測時，於實際應用場景中常出現偵測不穩定的問題，例如關鍵點抖動、手部消失或誤判。此問題會直接影響後續動作辨識（如 LSTM）與整體系統可靠性。

---

## ⚠️ 問題描述（Problem）

在系統整合過程中，觀察到以下現象：

* 手部關鍵點位置持續抖動（jitter）
* 手部短暫消失（tracking lost）
* 不同角度或光線下偵測結果不一致
* 雙手偵測時，左右手辨識錯亂

👉 導致：

* LSTM 輸入不穩定
* 動作判斷錯誤（false prediction）
* 系統觸發不可靠

---

## 🔍 原因分析（Analysis）

### 1. 光線與環境影響

* 低光源或過曝會影響特徵提取
* 背景複雜度會干擾模型判斷

---

### 2. 遮擋（Occlusion）

* 手部部分被遮住時，關鍵點會消失或偏移
* 手與物體（如料盤）重疊時影響偵測

---

### 3. 手部姿態變化

* 非常規角度（旋轉、側面）會降低準確率
* 快速移動造成 motion blur

---

### 4. 模型本身限制

* MediaPipe 為輕量模型，為即時性犧牲部分穩定性
* 偵測與追蹤模式切換可能導致短暫不連續

---

## 🛠 解決方法（Solution）

### ✔ 時間平滑（Temporal Smoothing）

* 對 keypoints 進行移動平均（moving average）
* 降低 jitter 對模型的影響

---

### ✔ 穩定性判斷（Stability Check）

* 設定「穩定幀數門檻」
* 僅當連續幀穩定時才觸發事件

---

### ✔ Missing Data 處理

* 手部消失時使用：

  * 前一幀補值（last value hold）
  * 或填入零（padding）

---

### ✔ Reset 機制

* 若手部消失超過一定時間（例如 2 秒）
  → 重置狀態機與序列 buffer

---

### ✔ 限制偵測條件

* 設定 ROI（只在料盤附近判斷）
* 避免背景干擾

---

## 🔧 實作細節（Implementation）

### Keypoints 平滑

```python id="sm3a2q"
smoothed = alpha * current + (1 - alpha) * previous
```

---

### Buffer 控制

* 維持固定長度序列（如 60 frames）
* 避免突發異常值影響整體判斷

---

### 偵測策略

* 僅當 hand 與 tray 接近時才啟用手勢判斷
* 減少不必要的計算與誤判

---

## 📝 注意事項（Notes）

* 過度平滑會導致反應延遲
* 不同使用者的手型與速度會影響效果
* 雙手情境需特別處理左右手對應
* 偵測不穩時，系統應避免立即做決策（需延遲確認）

---

## 🔗 關聯知識（Related Concepts）

* Temporal Filtering
* Noise Reduction
* Real-time Tracking
* Computer Vision Robustness
* Sequence Stability

---

## 📚 資料來源（Sources）

* MediaPipe Hands 實際測試
* 系統整合與實驗觀察

---

## 🚀 未來優化方向（Future Work）

* 使用 Kalman Filter 提升穩定性
* 結合 Optical Flow 改善追蹤
* 使用更高精度模型（如 OpenPose）
* 加入 confidence-based filtering
