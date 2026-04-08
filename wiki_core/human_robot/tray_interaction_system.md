# 🧠 Human-Robot Collaboration: Tray Interaction Detection System

## 📌 概念摘要（Summary）

本系統結合 YOLO 物件偵測與手勢時序模型（LSTM），用於判斷人是否真正「取走」或「放置」料盤。
透過多模態融合（vision + temporal pattern），提升單一視覺判斷的穩定性，避免誤觸發。

---

## 🧱 系統架構（System Architecture）

### 🎯 核心模組

* YOLO：偵測料盤位置（tray bounding box）
* MediaPipe Hands：取得雙手 21 keypoints（共 84 維）
* LSTM：判斷動作（take / idle）
* State Machine：整合所有訊號做決策

---

## 🔄 判斷流程（Pipeline）

1. YOLO 偵測 tray
2. MediaPipe 偵測手部 keypoints
3. 判斷 hand 是否進入 tray 區域（contact）
4. 將 keypoints 輸入 LSTM（60 frames window）
5. 若 LSTM 預測為 take 且符合條件 → 判定為「取走」
6. 若 tray 穩定消失 → 確認事件成立

---

## ⚠️ 為什麼不能只用 YOLO？

### 問題：

* 手靠近 ≠ 真的拿
* 遮擋造成誤判
* 短暫接觸造成誤觸發

### 解法：

👉 加入 LSTM 判斷「動作是否成立」

---

## 🧠 為什麼需要 LSTM？

LSTM 用來捕捉「時間序列」：

* 單幀：不知道是拿還是放
* 多幀：可以看出動作趨勢

👉 例如：

* take：手靠近 → 接觸 → 抬起
* idle：手晃來晃去但沒有完成動作

---

## 🔧 實作細節（Implementation Notes）

### Input

* shape: (60, 84)
* 雙手 keypoints（21 × 2 × 2）

### Threshold 設定

* TAKE_THRESH = 0.8
* IDLE_THRESH = 0.5
* CONTACT_THRESHOLD ≈ 20px

### 狀態機（State）

* idle
* contact
* taking
* placed

---

## 🐞 常見問題（Debug Notes）

### 問題 1：誤判 take

原因：

* 手只是靠近但沒拿

解法：

* 提高 TAKE threshold
* 增加連續判定次數（buffer）

---

### 問題 2：模型延遲

原因：

* window size 太大（60 frames）

解法：

* sliding window（stride < window）
* 或減少 window size

---

### 問題 3：不同人準確率下降

原因：

* 訓練資料不足

解法：

* 增加不同使用者資料（cross-user）

---

## 🔗 關聯知識（Related Concepts）

* Object Detection（YOLO）
* Hand Tracking（MediaPipe）
* Sequence Modeling（LSTM）
* State Machine Design
* Human-Robot Collaboration (HRC)

---

## 📚 資料來源（Sources）

* 自身專案實作
* MediaPipe Hands
* YOLOv10 文件

---

## 🚀 未來優化方向（Future Work）

* 改用 Transformer（支援長序列）
* 加入 attention mask（處理 padding）
* 結合 force sensor 做多模態融合

Add first wiki: tray interaction system
