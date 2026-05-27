## Workshop: Machine Learning and Deep Learning with KNIME — Day 3

## Deep Learning with KNIME: TensorFlow, CNN และ LSTM

---

### บทนำ (Introduction)

วันที่ 3 เป็นจุดสูงสุดของหลักสูตร — เราจะก้าวเข้าสู่โลกของ **Deep Learning** ซึ่งเป็นเทคนิคที่อยู่เบื้องหลังนวัตกรรม AI ทั้งหมด ตั้งแต่การแปลภาษา การรู้จำใบหน้า ไปจนถึงการตรวจจับการฉ้อโกงระดับสถาบันการเงินชั้นนำโลก

สำหรับ **กลุ่มงานตรวจสอบภายใน (Internal Audit Group)** ธนาคารกรุงศรีอยุธยา Deep Learning เปิดโอกาสให้:
- วิเคราะห์ **pattern ซับซ้อน** ในธุรกรรมที่ ML แบบเดิมจับไม่ได้
- พยากรณ์ **แนวโน้มความเสี่ยง** จากข้อมูล time series
- ตรวจสอบ **เอกสารและภาพถ่าย** ด้วย Computer Vision

ด้วย KNIME เราสามารถใช้ Deep Learning ได้โดยไม่ต้องเขียนโค้ด Python/TensorFlow โดยตรง

---

### สถาบันผู้จัดอบรม

| รายละเอียด  | ข้อมูล |
| ----------- | ------- |
| สถานที่อบรม | กลุ่มงานตรวจสอบภายใน (Internal Audit Group) ธนาคารกรุงศรีอยุธยา |
| วิทยากร     | อาจารย์สามิตร โกยม |
| สถาบัน      | สถาบันไอทีจีเนียส (IT Genius) |
| วันอบรม Day 3 | พฤหัสบดีที่ 28 พฤษภาคม 2569 |
| เวลา        | 09:00–16:00 น. (6 ชั่วโมง) |
| รูปแบบ      | Concept 35% · Hands-on Lab 65% |
| Dataset หลัก | loan_default.csv, customer_churn.csv, time_series_daily_transactions.csv |
| Extensions ที่ต้องมี | KNIME Deep Learning - Keras, TensorFlow 2, Python Integration, Image Processing, Network Mining, Textprocessing, AI Extension |
| Python Environment | `C:\knime_python_env\` — Python 3.11 + TensorFlow 2.21 CPU |

---

### วัตถุประสงค์ Day 3

1. เข้าใจความแตกต่างระหว่าง Machine Learning และ Deep Learning อย่างชัดเจน
2. อธิบาย Neural Network: Neuron, Layer, Activation Function, Loss Function ได้
3. เชื่อมต่อและใช้งาน TensorFlow Integration ใน KNIME
4. สร้างโมเดล Deep Learning สำหรับงาน Classification และประเมิน Overfitting
5. เข้าใจหลักการ CNN: Convolution, Pooling, Feature Map
6. สร้างและฝึก LSTM สำหรับงาน Time Series Prediction
7. สร้าง End-to-End Deep Learning Workflow ในโจทย์จริงสำหรับ Audit

---

### ตารางเวลา — Day 3: พฤหัสบดีที่ 28 พฤษภาคม 2569

| Section | เวลา        | หัวข้อ                                           | รูปแบบ        |
| ------- | ----------- | ------------------------------------------------ | ------------- |
| S1      | 09:00–10:00 | Introduction to Deep Learning                    | Concept + Visual |
| S2      | 10:00–11:00 | TensorFlow with KNIME                            | Demo + Lab    |
| —       | 11:00–11:15 | พักเบรก                                          | —             |
| S3      | 11:15–12:00 | Deep Learning for Classification                 | Hands-on Lab  |
| —       | 12:00–13:00 | พักรับประทานอาหาร                                | —             |
| S4      | 13:00–14:00 | Convolutional Neural Networks (CNN)              | Concept + Demo |
| —       | 14:00–14:15 | พักเบรก                                          | —             |
| S5      | 14:15–15:00 | LSTM และ Sequence Modeling                       | Hands-on Lab  |
| S6      | 15:00–16:00 | Mini Project / Capstone Workshop                 | Workshop      |

---

## Section 1: Introduction to Deep Learning

`09:00–10:00 (60 นาที) | Concept + Visual`

### 1.1 Deep Learning vs Machine Learning

```
Machine Learning:
┌────────────┐   ┌──────────────────────────────┐   ┌─────────┐
│  Raw Data  │──▶│  Manual Feature Engineering  │──▶│  Model  │
│            │   │  (คนต้องเลือก feature เอง)    │   │         │
└────────────┘   └──────────────────────────────┘   └─────────┘

Deep Learning:
┌────────────┐   ┌──────────────────────────────────────────────┐   ┌─────────┐
│  Raw Data  │──▶│     Neural Network (เรียนรู้ feature เอง)    │──▶│  Model  │
│            │   │  Layer 1   Layer 2   Layer 3   Layer 4       │   │         │
└────────────┘   └──────────────────────────────────────────────┘   └─────────┘
```

**เปรียบเทียบ ML vs Deep Learning:**

| ลักษณะ | Machine Learning | Deep Learning |
| ------ | ---------------- | ------------- |
| Feature Engineering | ต้องทำเอง | อัตโนมัติ |
| ข้อมูลที่ต้องการ | น้อย-ปานกลาง | ต้องการมาก |
| ตีความผลได้ | ✅ (Decision Tree) | ❌ (Black Box) |
| Accuracy บนข้อมูลซับซ้อน | ปานกลาง | สูงมาก |
| Computation ที่ต้องการ | ต่ำ | สูง (GPU) |
| เหมาะกับ | Tabular data | Image, Text, Time Series |

---

### 1.2 Neural Network คืออะไร?

**Neural Network** ได้แรงบันดาลใจจากสมองมนุษย์ ประกอบด้วย **Neurons** ที่เชื่อมต่อกัน:

```
               Input Layer    Hidden Layer 1   Hidden Layer 2   Output Layer
                              
               ┌─────┐        ┌─────┐           ┌─────┐
               │  x1 │───────▶│  H1 │──────────▶│  H5 │──────▶ Output
               └─────┘   ╲    └─────┘   ╲        └─────┘         (0 หรือ 1)
                           ╲  ┌─────┐    ╲       ┌─────┐
               ┌─────┐      ╲▶│  H2 │     ╲────▶│  H6 │
               │  x2 │───────▶│     │           └─────┘
               └─────┘   ╲    └─────┘
                           ╲  ┌─────┐
               ┌─────┐      ╲▶│  H3 │
               │  x3 │───────▶│     │
               └─────┘        └─────┘
                              ┌─────┐
                              │  H4 │
                              └─────┘

Input Features:
x1 = credit_score
x2 = debt_to_income
x3 = missed_payments_6m
...
```

---

### 1.3 Neuron — หน่วยพื้นฐาน

**แต่ละ Neuron ทำงานอย่างไร:**

```
       w1 ╮
  x1 ─────╮
       w2 ╮│   ┌─────────────────────────────────────────┐
  x2 ─────╮├──▶│  Σ (weighted sum) + bias → f(z) output  │──▶ ค่าออก
       w3 ╮│   └─────────────────────────────────────────┘
  x3 ─────╯    Activation Function: f(z) เปลี่ยน z ให้เป็น non-linear

z = w1×x1 + w2×x2 + w3×x3 + bias
output = f(z)
```

**Activation Functions:**

| Function | สูตร | ใช้ใน | ผลลัพธ์ |
| -------- | ---- | ------ | ------- |
| **ReLU** | max(0, z) | Hidden Layers (นิยมที่สุด) | 0 ถ้า z<0, z ถ้า z≥0 |
| **Sigmoid** | 1/(1+e^-z) | Output Layer (Binary) | [0, 1] |
| **Softmax** | e^z / Σe^z | Output Layer (Multi-class) | ความน่าจะเป็นต่อ class |
| **Tanh** | (e^z-e^-z)/(e^z+e^-z) | Hidden (LSTM) | [-1, 1] |

---

### 1.4 Loss Function และ Backpropagation

**Loss Function** วัดว่าโมเดลผิดพลาดมากแค่ไหน:

| Loss | ใช้กับ | สูตร |
| ---- | ------ | ---- |
| **Binary Cross-Entropy** | Binary Classification (Fraud/Not) | -[y×log(ŷ) + (1-y)×log(1-ŷ)] |
| **Categorical Cross-Entropy** | Multi-class | -Σ y×log(ŷ) |
| **Mean Squared Error** | Regression | (1/n)×Σ(y-ŷ)² |

**Backpropagation** คือกระบวนการปรับ weights เพื่อลด Loss:

```
Forward Pass:          Backward Pass (Gradient Descent):
Input → Predict ──▶   CalculateLoss ──▶ Adjust Weights ──▶ Repeat
                              ↑
                    Loss สูง = weights ผิด → ปรับ weights ลดลง
                    Loss ต่ำ = weights ถูก → ปรับเล็กน้อย
```

**Learning Rate:**
```
Learning Rate สูงเกิน:        Learning Rate ต่ำเกิน:       Learning Rate พอดี:
   Loss                          Loss                          Loss
    │ ╱╲ ╱╲ ╱              │ ──────────────         │ ╲
    │╱  ╲╱  ╲              │           ────          │  ╲____
    │        ╲             │               ────      │       ──────
    └──────────→           └──────────────────→      └──────────────→
    (ไม่ converge)          (converge ช้ามาก)         (ดีที่สุด)
```

---

### 1.5 Overfitting และ Underfitting

```
             Training Accuracy     Test Accuracy
Good Fit:         92%                  90%      ← ดีที่สุด
Overfitting:      99%                  70%      ← เรียนรู้มากเกิน (จำ training)
Underfitting:     65%                  64%      ← เรียนรู้น้อยเกิน

ป้องกัน Overfitting:
• Dropout: ปิด Neuron แบบสุ่มระหว่าง Training
• L1/L2 Regularization: ลงโทษ weights ขนาดใหญ่
• Early Stopping: หยุด train เมื่อ validation loss เริ่มเพิ่ม
• ใช้ข้อมูล Training มากขึ้น
```

---

## Section 2: TensorFlow with KNIME

`10:00–11:00 (60 นาที) | Demo + Lab`

### 2.1 TensorFlow Integration ใน KNIME

**TensorFlow** คือ Deep Learning Framework ของ Google ที่ KNIME รองรับผ่าน Extension:

```
KNIME DL4J / TensorFlow Extensions:

┌──────────────────────────────────────────────────────┐
│                KNIME Deep Learning Nodes             │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐                  │
│  │ DL Network   │  │  Keras       │                  │
│  │ Creator      │  │  Network     │                  │
│  │ (สร้าง model) │  │  Reader      │                  │
│  └──────────────┘  └──────────────┘                  │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐                  │
│  │ DL Learner   │  │ DL Predictor │                  │
│  │ (train model)│  │(ทำนายผล)    │                  │
│  └──────────────┘  └──────────────┘                  │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐                  │
│  │ DL to Table  │  │ DL Network   │                  │
│  │ Converter    │  │ Writer       │                  │
│  └──────────────┘  └──────────────┘                  │
└──────────────────────────────────────────────────────┘
```

---

### 2.2 Extensions ที่ติดตั้งใน KNIME 5.3.x

Extensions ทั้งหมดติดตั้งผ่าน **Help → Install KNIME Extensions** (ต้องการอินเทอร์เน็ตสำหรับดาวน์โหลด)

| Extension | Version | ใช้ใน |
| :--- | :--- | :--- |
| KNIME AI Extension | 5.11.0.v202602202108 | GenAI, LLM nodes |
| KNIME Deep Learning - Keras Integration | 5.11.0.v202602010958 | Keras Learner/Predictor |
| KNIME Deep Learning - TensorFlow 2 Integration | 5.11.0.v202602010958 | TF2 backend |
| KNIME Python Integration | 5.11.0.v202602211520 | Python Script Node |
| KNIME Image Processing | 1.8.3.v202512031239 | Image Loader, Resizer |
| KNIME Image Processing - Deep Learning Extension | 1.0.2.v202601161305 | Image to DL Tensor |

**ตรวจสอบหลังติดตั้ง:**
```
Node Repository → Deep Learning → มี nodes ต่อไปนี้:
• Keras Network Learner
• Keras Network Executor
• DL Python Network Creator
• DL Python Network Executor
```

**Python Environment สำหรับ Deep Learning:**
```
Path:         C:\knime_python_env\
Python:       3.11.15
TensorFlow:   2.21.0 (CPU)

ตั้งค่าใน KNIME:
  Preferences → KNIME → Python Deep Learning
  → Python executable: C:\knime_python_env\python.exe
```

---

### 2.3 เตรียมข้อมูลสำหรับ Neural Network

Neural Network ต้องการข้อมูลในรูปแบบพิเศษ:

```
การเตรียมข้อมูล Tabular Data สำหรับ DL:

1. Categorical Features → One-Hot Encoding
   employment_type: "Permanent" → [1,0,0,0,0,0]
   employment_type: "Contract"  → [0,1,0,0,0,0]
   
2. Numeric Features → Normalize [0,1]
   credit_score: 750 → 0.865 (ใช้ Min-Max หรือ Z-score)
   
3. Target → Integer/Binary
   default: "Yes" → 1
   default: "No"  → 0
```

**Node ที่ใช้ในการเตรียมข้อมูล:**

| ขั้นตอน | Node |
| -------- | ---- |
| Categorical → One-Hot | Category to Number |
| Normalize Numeric | Normalizer |
| Target Encoding | Number to String + Rule Engine |
| ตรวจสอบ Null | Missing Value |

---

### 🧪 Lab 1: เตรียมข้อมูล loan_default.csv สำหรับ Deep Learning

**ขั้นตอนที่ 1:** One-Hot Encoding สำหรับ Categorical
```
Category to Number node:
• columns: employment_type, collateral_type, loan_purpose, gender, marital_status
• Mode: Binary encoding (One-Hot)
Execute
```

**ขั้นตอนที่ 2:** Normalize Numeric
```
Normalizer:
• columns: ทุก numeric column ที่เหลือ
• Method: Min-Max Normalization [0,1]
Execute
```

**ขั้นตอนที่ 3:** แปลง Target
```
Rule Engine:
$default$ = "1" => 1
TRUE => 0
Output: default_numeric (Integer)
Execute
```

**ขั้นตอนที่ 4:** Train/Test Split
```
Partitioning: 80/20, Stratified on default
Execute
```

---

## Section 3: Deep Learning for Classification

`11:15–12:00 (45 นาที) | Hands-on Lab`

### 3.1 สร้าง Neural Network สำหรับ Loan Default Classification

**Architecture ที่เหมาะสม:**

```
Input Layer:  n nodes (จำนวน features หลัง One-Hot)
     │
     ▼
Hidden Layer 1: 64 nodes, ReLU activation, Dropout 0.3
     │
     ▼
Hidden Layer 2: 32 nodes, ReLU activation, Dropout 0.2
     │
     ▼
Hidden Layer 3: 16 nodes, ReLU activation
     │
     ▼
Output Layer: 1 node, Sigmoid activation
     │
     ▼
Output: 0.0 - 1.0 (ความน่าจะเป็นที่จะ Default)
```

**การตั้งค่า Keras Learner ใน KNIME:**

```
Keras Network Learner Configuration:
┌────────────────────────────────────────────────────────────┐
│  Target Column: default_numeric                            │
│                                                            │
│  Optimizer: Adam                                           │
│  Learning Rate: 0.001                                      │
│  Loss Function: binary_crossentropy                        │
│  Epochs: 50                                               │
│  Batch Size: 32                                            │
│                                                            │
│  ☑ Shuffle Training Data                                   │
│  Validation Split: 0.2 (20% ของ Training สำหรับ monitor)  │
└────────────────────────────────────────────────────────────┘
```

---

### 3.2 Monitor Training — Loss Curve

การ monitor Loss ระหว่าง training ช่วยตรวจสอบ Overfitting:

```
Training/Validation Loss:

     Loss
      │
  0.6 │ ╲
      │  ╲  training loss
  0.4 │   ╲___
      │       ╲___
  0.3 │           ╲___
      │               ╲__ training
  0.2 │    validation ╱╲  converged
      │         ╲___╱  ╲__________
      │                           (validation เพิ่ม = Overfitting!)
      └────────────────────────────→ Epochs
       1  5  10  15  20  25  30

Early Stopping: หยุดเมื่อ validation loss ไม่ลดลงต่อเนื่อง (patience=5)
```

---

### 🧪 Lab 2: สร้างและ Train Deep Learning Model

**Workflow:**
```
Prepared Data (จาก Lab 1)
      │
Partitioning (80/20)
  ┌───┴────┐
Training   Test
  │
  ▼
DL Python Network Creator
(หรือ Keras Network Creator)
  │
  ▼
Keras Network Learner
• Target: default_numeric
• Epochs: 50, Batch: 32
• Optimizer: Adam, LR: 0.001
  │
  ├──▶ (Loss Plot) → ดู Overfitting
  │
  ▼
Keras Network Executor
• Input: Test data
  │
  ▼
DL to Table Converter
  │
  ▼
Scorer / ROC Curve
```

**ขั้นตอนที่ 1:** สร้าง Network Architecture
```
DL Python Network Creator:
เขียน Python code สร้าง Keras model:

from tensorflow import keras

model = keras.Sequential([
    keras.layers.Dense(64, activation='relu', input_shape=(n_features,)),
    keras.layers.Dropout(0.3),
    keras.layers.Dense(32, activation='relu'),
    keras.layers.Dropout(0.2),
    keras.layers.Dense(16, activation='relu'),
    keras.layers.Dense(1, activation='sigmoid')
])
model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
output_network = model
```

**ขั้นตอนที่ 2:** Train Model
```
Keras Network Learner:
• เลือก Target column และ Feature columns
• Epochs: 50, Batch Size: 32
Execute → สังเกต Loss ลดลงทุก Epoch
```

**ขั้นตอนที่ 3:** ประเมินผล
```
Keras Network Executor → DL to Table → Scorer
เปรียบเทียบผลกับ ML algorithms จาก Day 2:
• Accuracy, Recall, F1, AUC
```

**ตารางเปรียบเทียบ ML vs DL:**

| โมเดล | Accuracy | Recall | AUC | ตีความได้ |
| ----- | -------- | ------ | --- | --------- |
| Naive Bayes | ? | ? | ? | ✅ |
| Decision Tree | ? | ? | ? | ✅✅ |
| k-NN | ? | ? | ? | ⚠️ |
| SVM | ? | ? | ? | ❌ |
| Neural Network | ? | ? | ? | ❌ |

> **Audit Insight:** DL อาจ Accuracy สูงกว่า แต่ Decision Tree ตีความได้ — เลือกตาม **business requirement**

---

## Section 4: Convolutional Neural Networks (CNN)

`13:00–14:00 (60 นาที) | Concept + Demo`

### 4.1 CNN คืออะไร?

**CNN (Convolutional Neural Network)** เป็น Neural Network ที่ออกแบบมาสำหรับ **ข้อมูลที่มีโครงสร้างเชิงพื้นที่** โดยเฉพาะรูปภาพ:

```
รูปภาพ (Image) = Matrix ของ Pixel Values:

┌───────────────────────────────┐
│ 255 240 200 100  50  30  10  │  ← Row ของ pixels
│ 240 250 210 120  60  20   5  │
│ 200 220 230 150  80  15   3  │
│  80 100 120 200 180 100  50  │
│  30  40  60 180 220 190 120  │
└───────────────────────────────┘
Image ขนาด 7×7 pixels (Gray Scale)
```

---

### 4.2 Convolution Operation

**Convolution** คือการ "กวาด" filter เล็กๆ ผ่านรูปภาพเพื่อดึง feature:

```
Image (5×5):          Filter (3×3):     Feature Map (3×3):
┌─────────────┐       ┌─────────┐        ┌─────────┐
│ 1  2  3  0  1 │   │ 1  0  -1│        │  -3  4  7 │
│ 0  4  5  1  2 │ × │ 1  0  -1│   =    │   2  5  8 │
│ 2  3  6  2  1 │   │ 1  0  -1│        │   1  3  6 │
│ 1  2  3  0  0 │   └─────────┘        └─────────┘
│ 0  1  2  1  1 │
└─────────────┘
Filter นี้ตรวจจับ "Vertical Edge"
```

**Filters ตัวอย่างที่ CNN เรียนรู้อัตโนมัติ:**

```
Edge Detection:   Blur:        Sharpen:
┌──────────┐   ┌──────────┐  ┌──────────┐
│-1  0  1  │   │1/9 1/9 1/9│  │ 0  -1  0 │
│-1  0  1  │   │1/9 1/9 1/9│  │-1   5 -1 │
│-1  0  1  │   │1/9 1/9 1/9│  │ 0  -1  0 │
└──────────┘   └──────────┘  └──────────┘
```

---

### 4.3 Pooling — ลดขนาด Feature Map

**Pooling** ลดขนาด Feature Map เพื่อลด Computation และ Overfitting:

```
Max Pooling (2×2):

┌────────────────┐         ┌────────┐
│ 1   3   2   4  │         │ 3   4  │
│ 5   6   1   2  │  ──▶    │ 8   4  │
│ 7   8   3   2  │  Max    └────────┘
│ 1   2   4   3  │  Pool
└────────────────┘
(4×4 → 2×2, ลดขนาดครึ่งหนึ่ง)
```

---

### 4.4 สถาปัตยกรรม CNN ทั้งหมด

```
Input Image
    │
    ▼
Convolution Layer 1 (32 filters, 3×3) + ReLU
    │
    ▼
Max Pooling (2×2)
    │
    ▼
Convolution Layer 2 (64 filters, 3×3) + ReLU
    │
    ▼
Max Pooling (2×2)
    │
    ▼
Flatten (แปลง 2D → 1D)
    │
    ▼
Dense Layer (128 neurons) + ReLU + Dropout
    │
    ▼
Output Layer (n classes) + Softmax
    │
    ▼
Prediction (ประเภทของภาพ)
```

---

### 4.5 Use Cases ของ CNN ใน Internal Audit

| Use Case | ตัวอย่าง |
| -------- | -------- |
| **OCR เอกสาร** | อ่านใบเสร็จ, slip โอนเงิน, เอกสารสัญญา |
| **Signature Verification** | ตรวจสอบลายเซ็นในเอกสาร |
| **Stamp Detection** | ตรวจสอบตราประทับบนเอกสาร |
| **Document Classification** | แยกประเภทเอกสาร audit |
| **Anomaly in Charts** | ตรวจสอบ pattern ผิดปกติในกราฟการเงิน |

---

### 🧪 Lab 3: Demo CNN Image Classification (Concept Lab)

**วัตถุประสงค์:** เข้าใจกระบวนการ CNN ผ่าน demo ตัวอย่าง

**สถานการณ์:** จำแนกประเภทเอกสาร (ใบเสร็จ vs สัญญา vs หนังสือมอบอำนาจ)

**Workflow ใน KNIME (Image Processing Extension):**
```
Image Loader (โหลดรูปภาพ)
      │
      ▼
Image Resizer (ปรับขนาด 224×224)
      │
      ▼
Image to DL Tensor (แปลงเป็น Tensor)
      │
      ▼
Keras Network Learner (CNN Architecture)
• Conv2D(32) → MaxPool → Conv2D(64) → MaxPool
• Flatten → Dense(128) → Dropout → Dense(3, softmax)
      │
      ▼
Keras Network Executor
      │
      ▼
Class Label (ประเภทเอกสาร)
```

**ตัวอย่าง Keras Code สำหรับ CNN:**
```python
from tensorflow import keras

model = keras.Sequential([
    keras.layers.Conv2D(32, (3,3), activation='relu', input_shape=(224,224,3)),
    keras.layers.MaxPooling2D(2,2),
    keras.layers.Conv2D(64, (3,3), activation='relu'),
    keras.layers.MaxPooling2D(2,2),
    keras.layers.Conv2D(128, (3,3), activation='relu'),
    keras.layers.MaxPooling2D(2,2),
    keras.layers.Flatten(),
    keras.layers.Dense(128, activation='relu'),
    keras.layers.Dropout(0.5),
    keras.layers.Dense(3, activation='softmax')  # 3 ประเภทเอกสาร
])
model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])
output_network = model
```

> **หมายเหตุ:** Lab นี้เป็น Concept Lab — ในการใช้งานจริงต้องมีชุดรูปภาพ training ที่ label แล้ว อาจารย์จะ demo workflow และอธิบาย concept

---

## Section 5: LSTM และ Sequence Modeling

`14:15–15:00 (45 นาที) | Hands-on Lab`

### 5.1 Sequence Data คืออะไร?

**Sequence Data** คือข้อมูลที่ **ลำดับมีความสำคัญ** — ค่าในอดีตส่งผลต่อปัจจุบันและอนาคต:

```
ตัวอย่าง Sequence Data:
• ยอดธุรกรรมรายวัน:    [Mon=50M, Tue=45M, Wed=55M, Thu=?]
• ราคาหลักทรัพย์:      [Day1=100, Day2=105, Day3=98, Day4=?]
• Log การเข้าใช้งาน:   [Login→View→Transfer→Logout→?]

Regular Neural Network ไม่รองรับ Sequence:
• ทุก input เป็นอิสระกัน
• ไม่จำ "context" ของข้อมูลก่อนหน้า
```

---

### 5.2 Recurrent Neural Network (RNN)

**RNN** แก้ปัญหาโดยมี "ความจำ" จากขั้นตอนก่อนหน้า:

```
Standard Neural Net:        RNN:
  x1 ──▶ [Network] ──▶ y      x1 ──▶ [h1] ──▶ y1
  x2 ──▶ [Network] ──▶ y      x2 ──▶ [h2] ──▶ y2
  x3 ──▶ [Network] ──▶ y      x3 ──▶ [h3] ──▶ y3
                               ↑       ↑       ↑
                            h0───────h1───────h2
                          (hidden state ส่งต่อ)
```

**ปัญหาของ RNN: Vanishing Gradient**
- เมื่อ sequence ยาวมาก gradient จะเล็กลงเรื่อยๆ
- โมเดลจำข้อมูลเก่าๆ ไม่ได้
- LSTM แก้ปัญหานี้!

---

### 5.3 LSTM — Long Short-Term Memory

**LSTM** มีกลไกพิเศษ "Gates" ที่ควบคุมว่าจะจำหรือลืมข้อมูล:

```
┌──────────────────────────── LSTM Cell ────────────────────────────┐
│                                                                    │
│  Cell State (ความจำระยะยาว) ──────────────────────────────────▶   │
│                    │                                              │
│           ┌────────┼────────┐                                     │
│           │        │        │                                     │
│        Forget    Input     Output                                 │
│         Gate     Gate       Gate                                  │
│           │        │        │                                     │
│        (จะลืม   (จะจำ    (จะส่ง                                  │
│         อะไร?)  อะไรใหม่?) ออกอะไร?)                             │
│                                                                    │
│  Input x(t) ──────────────────────────────────────────────────▶  │
│  Hidden h(t-1) ────────────────────────────────────────────────▶ │
└────────────────────────────────────────────────────────────────────┘
```

**3 Gates ของ LSTM:**

| Gate | หน้าที่ | เปรียบเทียบ |
| ---- | ------- | ----------- |
| **Forget Gate** | ตัดสินใจว่าจะลืมข้อมูลอะไรจาก cell state เก่า | ตะแกรงกรองข้อมูล |
| **Input Gate** | ตัดสินใจว่าจะเพิ่มข้อมูลใหม่อะไรเข้า cell state | ประตูรับข้อมูลใหม่ |
| **Output Gate** | ตัดสินใจว่าจะส่งข้อมูลอะไรออกไป | ประตูส่งออก |

---

### 5.4 เตรียมข้อมูล Time Series สำหรับ LSTM

**Dataset:** time_series_daily_transactions.csv (730 วัน)

**การสร้าง Sliding Window:**
```
ข้อมูลยอดธุรกรรมรายวัน:
[Day1, Day2, Day3, Day4, Day5, ..., Day730]

Sliding Window (window_size = 7):
X = [Day1, Day2, Day3, Day4, Day5, Day6, Day7]  → y = Day8
X = [Day2, Day3, Day4, Day5, Day6, Day7, Day8]  → y = Day9
X = [Day3, Day4, Day5, Day6, Day7, Day8, Day9]  → y = Day10
...

แต่ละ X มี 7 timestep → ทำนาย y วันถัดไป
```

**Shape ของ LSTM Input:**
```
Input shape: (samples, timesteps, features)
ตัวอย่าง: (723, 7, 1)
 └─ 723 windows
        └─ 7 timesteps ต่อ window
              └─ 1 feature (total_amount_thb)
```

---

### 🧪 Lab 4: พยากรณ์ยอดธุรกรรมด้วย LSTM

**วัตถุประสงค์:** สร้างโมเดล LSTM พยากรณ์ยอดธุรกรรมรายวันของธนาคาร

**Workflow:**
```
time_series_daily_transactions.csv
      │
      ▼
CSV Reader → Sort (by date) → Column Filter (total_amount_thb)
      │
      ▼
Normalizer (Min-Max [0,1]) ← สำคัญ! LSTM ต้องการข้อมูล normalized
      │
      ▼
Lag Column (สร้าง window: lag1...lag7)
      │
      ▼
Column Filter (เลือก lag1-lag7 เป็น input, total_amount เป็น target)
      │
      ▼
Partitioning (80% Train ข้อมูลเก่า, 20% Test ข้อมูลใหม่)
แนะนำ: แบ่งตาม time (ไม่ใช่ random!)
  ┌───┴────┐
Training   Test
  │
  ▼
LSTM Network Creator (DL Python)
• LSTM(50 units) → LSTM(25 units) → Dense(1)
  │
  ▼
Keras Network Learner
• Epochs: 100, Batch: 16
• Loss: MSE, Optimizer: Adam
  │
  ▼
Keras Network Executor
  │
  ▼
Denormalizer (คืนค่าเป็น THB จริง)
  │
  ▼
Line Chart (Actual vs Predicted)
```

**ขั้นตอนที่ 1:** สร้าง Lag Features
```
Lag Column node:
• Column: total_amount_thb
• Lag values: 1, 2, 3, 4, 5, 6, 7
Execute → ได้ lag_1, lag_2, ..., lag_7 เป็น features
```

**ขั้นตอนที่ 2:** แบ่ง Train/Test ตาม Time
```
Row Filter:
• Training: date < 2024-09-01 (ประมาณ 80%)
• Test: date >= 2024-09-01 (ประมาณ 20%)
(ไม่ใช้ Random Split สำหรับ Time Series!)
```

**ขั้นตอนที่ 3:** สร้าง LSTM Model
```python
from tensorflow import keras

model = keras.Sequential([
    keras.layers.LSTM(50, return_sequences=True, input_shape=(7, 1)),
    keras.layers.Dropout(0.2),
    keras.layers.LSTM(25, return_sequences=False),
    keras.layers.Dropout(0.2),
    keras.layers.Dense(1)  # Output: ยอดธุรกรรมวันถัดไป
])
model.compile(optimizer='adam', loss='mse')
output_network = model
```

**ขั้นตอนที่ 4:** ประเมินผล
```
เมตริกสำหรับ Time Series:
• MAE (Mean Absolute Error): ค่าคลาดเคลื่อนเฉลี่ยเป็น THB
• RMSE (Root Mean Squared Error): คลาดเคลื่อนรวม
• MAPE (Mean Absolute Percentage Error): % คลาดเคลื่อน

Line Chart: เปรียบเทียบ Actual vs Predicted
```

**ตัวอย่างผลที่ควรได้:**
```
Actual (THB) vs Predicted (THB):
วัน 1: Actual=45,200,000   Predicted=44,800,000  Error=0.9%
วัน 2: Actual=38,500,000   Predicted=39,100,000  Error=1.6%
วัน 3: Actual=52,300,000   Predicted=51,900,000  Error=0.8%
...
MAPE ≈ 3-7% = ถือว่าดีสำหรับ Financial Time Series
```

> **Audit Application:** ใช้โมเดลนี้ตรวจสอบว่าวันไหนยอดธุรกรรม "ผิดปกติ" จากที่คาดการณ์ → trigger การตรวจสอบเพิ่มเติม

---

## Section 6: Mini Project / Capstone Workshop

`15:00–16:00 (60 นาที) | Workshop`

### 6.1 ภาพรวม Capstone Workshop

ผู้เรียนแต่ละกลุ่มสร้าง End-to-End Workflow ที่ครบกระบวนการ โดยเลือก 1 โจทย์จาก 3 ตัวเลือก:

---

### โจทย์ที่ 1: Fraud Detection System

**ธีม:** ระบบตรวจจับธุรกรรมน่าสงสัยอัตโนมัติ

**ข้อมูล:** financial_transactions.csv

**กระบวนการ:**
```
financial_transactions.csv
      │
      ▼
Data Preparation (Type Conversion, Missing Values, Rule Engine)
      │
      ▼
Feature Engineering (สร้าง features เพิ่มเติม)
• transaction_hour (จาก transaction_time)
• is_high_value (amount > 30000)
• is_foreign_merchant (จาก merchant_category)
      │
      ▼
Train/Test Split (80/20, Stratified)
      │
      ├──▶ Decision Tree ──▶ Scorer
      ├──▶ Neural Network ──▶ Scorer
      └──▶ ROC Curve (เปรียบเทียบ)
      │
      ▼
เลือกโมเดลที่ดีที่สุด → Apply กับ All Data
      │
      ▼
Export: ธุรกรรมน่าสงสัย + Risk Score → audit_suspicious.csv
      │
      ▼
Bar Chart: Top 10 สาขาที่มี Fraud สูงสุด
```

**ผลลัพธ์ที่ต้องส่ง:**
- ❏ Workflow สมบูรณ์
- ❏ ตาราง Metrics เปรียบเทียบโมเดล
- ❏ ไฟล์ audit_suspicious.csv
- ❏ Chart แสดงผลสรุป

---

### โจทย์ที่ 2: Loan Default Early Warning System

**ธีม:** ระบบเตือนภัยล่วงหน้าสำหรับสินเชื่อเสี่ยง

**ข้อมูล:** loan_default.csv

**กระบวนการ:**
```
loan_default.csv
      │
      ▼
Data Prep + One-Hot Encoding
      │
      ▼
Partitioning (80/20)
      │
      ├──▶ Random Forest / Decision Tree
      ├──▶ Neural Network (64→32→16→1, Sigmoid)
      └──▶ เปรียบเทียบ ROC + F1
      │
      ▼
เลือกโมเดลที่ดีที่สุด → Score ลูกค้าทั้งหมด
      │
      ▼
Risk Scoring: ลูกค้าแต่ละคนได้ probability 0-1
• ≥ 0.7 → HIGH RISK → Immediate Audit
• 0.4-0.7 → MEDIUM RISK → Monitor
• < 0.4 → LOW RISK → Routine Check
      │
      ▼
Export: Loan Risk Report + Bar Chart Risk Distribution
```

**ผลลัพธ์ที่ต้องส่ง:**
- ❏ Workflow สมบูรณ์
- ❏ Confusion Matrix + ROC ของโมเดลที่เลือก
- ❏ Loan Risk Report (3 tier)
- ❏ Decision Tree ที่อ่านออก (อธิบายเหตุผลการทำนายได้)

---

### โจทย์ที่ 3: Customer Risk Monitoring Dashboard

**ธีม:** Dashboard วิเคราะห์ความเสี่ยงลูกค้าแบบครบวงจร

**ข้อมูล:** customer_churn.csv + loan_default.csv

**กระบวนการ:**
```
customer_churn.csv ──┐
                      ├──▶ Joiner (customer_id) ──▶ Combined Dataset
loan_default.csv ────┘
      │
      ▼
Clustering (k-Means, k=4)
→ แบ่ง 4 กลุ่มลูกค้าตาม risk profile
      │
      ▼
Classification (DL / Decision Tree)
→ ทำนาย churn และ default ต่อลูกค้าแต่ละราย
      │
      ▼
Risk Matrix (2×2):
         Default Risk
         Low     High
Churn ┌──────────────┐
Low   │  Safe   │ Loan │
      │  Zone   │ Risk │
High  ├──────────────┤
      │  Churn  │ HIGH │
      │  Risk   │ALERT │
      └──────────────┘
      │
      ▼
Export: Customer Risk Matrix + Cluster Profile + Chart
```

**ผลลัพธ์ที่ต้องส่ง:**
- ❏ Workflow สมบูรณ์
- ❏ Cluster Profile ของแต่ละกลุ่ม (ลักษณะเด่น)
- ❏ Risk Matrix และ Customer List ต่อ Quadrant
- ❏ Scatter Plot: 4 Cluster ที่มีสี

---

### 6.2 เกณฑ์การประเมิน Workshop

| เกณฑ์ | คะแนน | รายละเอียด |
| ----- | ------ | ---------- |
| **Workflow ทำงานได้** | 30% | Execute ได้โดยไม่ error |
| **Data Preparation ถูกต้อง** | 20% | Type, Missing, Normalize |
| **โมเดลเลือกและตีความถูก** | 25% | Metrics, เหตุผลการเลือก |
| **Output ใช้งานได้จริง** | 15% | CSV export, Chart อ่านออก |
| **การนำเสนอผลลัพธ์** | 10% | อธิบาย Business Impact |

---

### 6.3 Workflow Template สำหรับ Capstone

**โครงสร้าง Workflow ที่แนะนำ:**

```
┌─────────────────────────────────────────────────────────────────┐
│                    CAPSTONE WORKFLOW                             │
│                                                                 │
│  1. DATA INGESTION          2. DATA PREPARATION                 │
│  ┌──────────────┐           ┌──────────────────────┐            │
│  │ CSV Reader   │──────────▶│ Type Cast + Missing  │            │
│  └──────────────┘           │ + Rule Engine + Norm  │            │
│                             └──────────┬───────────┘            │
│                                        │                        │
│  3. SPLIT                  4. MODELING  │                        │
│  ┌──────────────┐           ┌──────────▼──────────┐             │
│  │ Partitioning │──────────▶│ Learner             │             │
│  │ (80/20 Strat)│           │ (DT / NN / kNN)     │             │
│  └──────────────┘           └──────────┬──────────┘             │
│                                        │                        │
│  5. EVALUATION             6. OUTPUT   │                        │
│  ┌──────────────┐           ┌──────────▼──────────┐             │
│  │ Scorer       │◀──────────│ Predictor + Scorer  │             │
│  │ ROC Curve    │           │ Chart + CSV Writer  │             │
│  └──────────────┘           └─────────────────────┘             │
└─────────────────────────────────────────────────────────────────┘
```

---

### สรุป Day 3 และภาพรวมหลักสูตรทั้งหมด

เมื่อจบวันที่ 3 ผู้เรียนได้เรียนรู้และฝึกปฏิบัติครบดังนี้:

**Section 1: Introduction to Deep Learning**

- ✅ เข้าใจ ML vs Deep Learning: Feature Engineering อัตโนมัติ vs มือ
- ✅ อธิบาย Neural Network: Neuron, Hidden Layer, Activation Function
- ✅ เข้าใจ Backpropagation, Loss Function, Learning Rate
- ✅ รู้จักปัญหา Overfitting และวิธีป้องกัน (Dropout, Early Stopping)

**Section 2: TensorFlow with KNIME**

- ✅ ติดตั้งและใช้งาน KNIME Deep Learning Extension
- ✅ เตรียมข้อมูลสำหรับ Neural Network: One-Hot, Normalize, Target Encoding
- ✅ สร้าง Network Architecture ด้วย Keras ใน KNIME

**Section 3: Deep Learning for Classification**

- ✅ สร้างโมเดล Neural Network สำหรับ Loan Default Classification
- ✅ Monitor Loss/Accuracy Curve และตรวจจับ Overfitting
- ✅ เปรียบเทียบ ML vs DL: เลือกตาม interpretability requirement

**Section 4: CNN**

- ✅ เข้าใจ Convolution Operation, Filter, Feature Map
- ✅ เข้าใจ Pooling (Max Pool) ในการลดขนาด
- ✅ ออกแบบ CNN Architecture สำหรับ Document Classification
- ✅ รู้จัก Use Cases CNN ในงาน Internal Audit

**Section 5: LSTM และ Sequence Modeling**

- ✅ เข้าใจ Sequence Data และปัญหาของ RNN (Vanishing Gradient)
- ✅ อธิบาย LSTM 3 Gates: Forget, Input, Output
- ✅ เตรียมข้อมูล Time Series ด้วย Sliding Window / Lag Features
- ✅ สร้างโมเดล LSTM พยากรณ์ยอดธุรกรรมรายวัน
- ✅ ประเมิน MAE, RMSE, MAPE สำหรับ Time Series

**Section 6: Capstone Workshop**

- ✅ สร้าง End-to-End Workflow ครบกระบวนการ
- ✅ ผสมผสาน Data Prep + ML + DL ในโจทย์จริง
- ✅ Export ผลลัพธ์ที่ใช้งานได้จริงสำหรับงาน Audit

---

### แนวทางต่อยอดหลังจบหลักสูตร

**ระยะสั้น (1-3 เดือน):**
- นำ workflow ที่สร้างใน workshop ไปปรับใช้กับข้อมูลจริงในองค์กร
- ทดลองใช้ข้อมูลธุรกรรมจริง (anonymized) กับ Fraud Detection workflow
- เรียน KNIME Hub (hub.knime.com) เพื่อดู workflow ตัวอย่างเพิ่มเติม

**ระยะกลาง (3-6 เดือน):**
- ต่อยอด KNIME Server เพื่อ automate workflow รายวัน/รายสัปดาห์
- เชื่อมต่อ KNIME กับฐานข้อมูล Oracle/MSSQL ของธนาคารโดยตรง
- ศึกษา KNIME Network Analytics สำหรับ Fraud Network Detection

**ระยะยาว (6+ เดือน):**
- Python Integration: ใช้ Python Script Node เพื่อ custom ML algorithms
- KNIME AI Extension: ลอง LLM และ GenAI nodes สำหรับ Document Analysis
- ขยายสู่ MLOps: deploy model ใน production และ monitor drift

**แหล่งเรียนรู้เพิ่มเติม:**

| แหล่ง | URL | เนื้อหา |
| ----- | --- | ------- |
| KNIME Learning Hub | learning.knime.com | Course ฟรีทุกระดับ |
| KNIME Forum | forum.knime.com | ถาม-ตอบ community |
| KNIME Hub | hub.knime.com | Workflow ตัวอย่างนับพัน |
| Kaggle | kaggle.com | Dataset และ Competition |
| UCI ML Repository | archive.ics.uci.edu | Dataset งานวิจัย |
| Coursera / Deeplearning.ai | coursera.org | Deep Learning Specialization |

---

> **ขอบคุณทุกท่านที่เข้าร่วมหลักสูตร Machine Learning and Deep Learning with KNIME** 🙏
>
> หวังว่าความรู้ที่ได้รับจาก 3 วันนี้จะเป็นประโยชน์ในการยกระดับงาน Internal Audit ของกลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา ให้ก้าวทันเทคโนโลยี Data Science และ AI ในยุคดิจิทัล
>
> **อาจารย์สามิตร โกยม | IT Genius Institute**
