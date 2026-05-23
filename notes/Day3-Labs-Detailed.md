# Day 3 — Labs Detailed: Deep Learning with KNIME
## Workshop: Machine Learning and Deep Learning with KNIME — Krungsri Internal Audit Group

---

### ข้อมูลหลักสูตร

| รายละเอียด | ข้อมูล |
| ---------- | ------- |
| สถานที่อบรม | กลุ่มงานตรวจสอบภายใน (Internal Audit Group) ธนาคารกรุงศรีอยุธยา |
| วิทยากร | อาจารย์สามิตร โกยม |
| สถาบัน | สถาบันไอทีจีเนียส (IT Genius) |
| วันอบรม Day 3 | พฤหัสบดีที่ 28 พฤษภาคม 2569 |
| เวลา | 09:00–16:00 น. |
| Dataset หลัก | loan_default.csv, time_series_daily_transactions.csv |
| KNIME Version | KNIME Analytics Platform 5.3 (Modern UI) |
| Python Environment | `C:\knime_python_env\` — Python 3.11.15, TensorFlow 2.21.0 (CPU) |

---

### Extensions ที่ต้องติดตั้งก่อน Lab

ตรวจสอบผ่าน **Help → Install KNIME Extensions** หรือ **File → Install KNIME Extensions**

| Extension | ใช้ใน Lab |
| --------- | --------- |
| KNIME Deep Learning - Keras Integration | Lab 2, Lab 3, Lab 4 |
| KNIME Deep Learning - TensorFlow 2 Integration | Lab 2, Lab 3, Lab 4 |
| KNIME Python Integration | Lab 2, Lab 3, Lab 4 |
| KNIME Image Processing | Lab 3 (Demo) |
| KNIME Image Processing - Deep Learning Extension | Lab 3 (Demo) |

**ตั้งค่า Python Environment ก่อนเริ่ม Lab:**

```
KNIME → Preferences → KNIME → Python Deep Learning
→ Python executable: C:\knime_python_env\python.exe
→ กด Apply and Close
```

---

## Day3_Lab1_Data_Preparation_for_Deep_Learning

**ชื่อ Lab:** Data Preparation for Deep Learning
**Dataset:** `loan_default.csv`
**วัตถุประสงค์:** เตรียมข้อมูลสำหรับ Neural Network โดยเฉพาะ — One-Hot Encoding, Normalization, Target Encoding และ Train/Test Split
**ตำแหน่งใน Schedule:** Section 2 — 10:00–11:00 น.

---

### Overview Workflow

```
[Node 1]          [Node 2]          [Node 3]               [Node 4]
CSV Reader   →  Missing Value  →  Category to Number  →   Normalizer
                                  (One-Hot Encoding)

[Node 5]          [Node 6]
Rule Engine  →  Partitioning
(default_numeric)  (80/20)
```

---

### Node 1: CSV Reader

**ชื่อ Node:** CSV Reader
**Node Path:** `IO → Read → CSV Reader`
**วัตถุประสงค์:** โหลดไฟล์ loan_default.csv เข้า KNIME

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Settings**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| File/URL | `C:\TrainingDocument\Machine Learning and Deep Learning with KNIME - Krungsri\dataset\loan_default.csv` |
| Column delimiter | `,` (comma) |
| Row delimiter | System Default |
| Quote char | `"` (double quote) |
| Comment char | (ว่าง) |

**Tab: Transformation**

- คลิก **Detect column types automatically** (หรือกด Auto-detect)
- ตรวจสอบ column types:
  - `loan_id` → String
  - `credit_score`, `loan_amount`, `annual_income`, `debt_to_income` → Double
  - `employment_type`, `collateral_type`, `loan_purpose`, `gender`, `marital_status` → String
  - `default` → String (ค่า "0"/"1" หรือ "No"/"Yes")
  - `default_label` → String

**Tab: Advanced Settings**
- ✅ **Has column header** (แถวแรกเป็นชื่อ column)
- ✅ **Has row ID** → ปิด (ไม่มี row ID column)
- Encoding: UTF-8

กด **OK** แล้ว **Execute** (Shift+F7)

**ผลลัพธ์ที่ได้:** ตารางข้อมูล loan_default.csv ประมาณ 10,000 แถว

---

### Node 2: Missing Value

**ชื่อ Node:** Missing Value
**Node Path:** `Manipulation → Column → Transform → Missing Value`
**วัตถุประสงค์:** จัดการค่า Missing ก่อนส่งเข้า Neural Network (DL ไม่รับ null values)

**การเชื่อมต่อ:**
- Input port: ต่อจาก CSV Reader (Node 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Default**

ตั้งค่า Default handling ตาม column type:

| Column Type | Method | ค่า |
| ----------- | ------ | --- |
| Double / Integer | **Mean** | คำนวณอัตโนมัติ |
| String | **Most frequent value** | ค่าที่พบมากสุด |

**Tab: Column Settings** (Optional — ถ้าต้องการตั้งทีละ column)

กรณีพบ column ที่ต้องการ handle เป็นพิเศษ เช่น `credit_score`:
1. คลิกที่ column ในรายการด้านซ้าย
2. เลือก Method: `Fixed Value` → ใส่ `600`
3. คลิก **Add**

กด **OK** แล้ว **Execute**

**ตรวจสอบผลลัพธ์:**
- คลิกขวาที่ Node → **Open First Output Table**
- กด **Ctrl+Shift+F** หรือดูที่ Column Statistics → ไม่มี Missing Count เหลืออยู่

---

### Node 3: Category to Number

**ชื่อ Node:** Category to Number
**Node Path:** `Other Data Mining → Binary Classification → Category to Number`
**วัตถุประสงค์:** แปลง Categorical features เป็น One-Hot Encoding สำหรับ Neural Network

**การเชื่อมต่อ:**
- Input port: ต่อจาก Missing Value (Node 2)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Settings**

**Column Selection (เลือก columns ที่ต้องการ One-Hot):**

คลิก **Add** แล้วเลือก columns ต่อไปนี้ทีละตัว:
- `employment_type`
- `collateral_type`
- `loan_purpose`
- `gender`
- `marital_status`

**Encoding Settings:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Encoding type | **Binary (One-Hot)** |
| Append columns | ✅ เลือก (เพิ่ม columns ใหม่แทนที่จะแทนที่) |
| Remove original columns | ✅ เลือก (ลบ column เดิมออก) |
| Column name separator | `_` (underscore) |

> **หมายเหตุ:** ตัวอย่างผลลัพธ์ — `employment_type` ที่มี 5 ค่าจะถูกแปลงเป็น 5 columns: `employment_type_Contract`, `employment_type_Freelance`, `employment_type_Part-time`, `employment_type_Permanent`, `employment_type_Self-employed`

กด **OK** แล้ว **Execute**

**ผลลัพธ์ที่ได้:**
- จำนวน columns เพิ่มขึ้น (แต่ละ category แปลงเป็นหลาย binary columns)
- ไม่มี String columns สำหรับ features ที่เลือกแล้ว

---

### Node 4: Normalizer

**ชื่อ Node:** Normalizer
**Node Path:** `Manipulation → Column → Transform → Normalizer`
**วัตถุประสงค์:** Normalize ค่า Numeric features ให้อยู่ในช่วง [0,1] — Neural Network ต้องการข้อมูลที่ scale เดียวกัน

**การเชื่อมต่อ:**
- Input port: ต่อจาก Category to Number (Node 3)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Settings**

**Method:**
- เลือก ● **Min-Max Normalization** (ปรับค่าให้อยู่ระหว่าง 0 ถึง 1)
- New minimum: `0.0`
- New maximum: `1.0`

**Column Selection:**

คลิก **Add All** เพื่อเลือก Numeric columns ทั้งหมด จากนั้น **Remove** columns ที่ไม่ต้องการ Normalize:
- Remove: `default_label` (ถ้าเป็น String จะไม่แสดงในรายการ)
- Remove: One-Hot columns ที่เพิ่งสร้าง (ค่าเป็น 0/1 อยู่แล้ว ไม่จำเป็นต้อง normalize อีก)

Columns ที่ควร normalize:
- `credit_score`
- `loan_amount`
- `annual_income`
- `debt_to_income`
- `missed_payments_6m`
- `loan_age_months`
- numeric columns อื่นๆ ที่มีช่วงค่ากว้าง

กด **OK** แล้ว **Execute**

> **หมายเหตุสำคัญ:** Node นี้มี Output port 2 เป็น **Normalization Model** ให้บันทึกไว้ — จำเป็นต้องใช้กับ Denormalizer ในกรณีที่ต้องแปลงค่ากลับ

---

### Node 5: Rule Engine

**ชื่อ Node:** Rule Engine
**Node Path:** `Manipulation → Column → Convert & Replace → Rule Engine`
**วัตถุประสงค์:** แปลง Target column `default_label` (String) เป็น `default_numeric` (Integer 0/1) ที่ Neural Network ต้องการ

**การเชื่อมต่อ:**
- Input port: ต่อจาก Normalizer (Node 4)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Rule Editor**

พิมพ์ Rules ต่อไปนี้ในช่อง Rule:

```
$default_label$ = "1" => 1
TRUE => 0
```

หรือถ้า column `default` มีค่า "Yes"/"No":
```
$default_label$ = "Yes" => 1
TRUE => 0
```

**Output Column Settings:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Append Column | ✅ เลือก |
| New column name | `default_numeric` |
| Output type | **Integer** |

กด **OK** แล้ว **Execute**

**ตรวจสอบ:** column `default_numeric` ควรมีค่าเป็น 0 และ 1 เท่านั้น

---

### Node 6: Partitioning

**ชื่อ Node:** Partitioning
**Node Path:** `Manipulation → Row → Transform → Partitioning`
**วัตถุประสงค์:** แบ่ง dataset เป็น Training Set (80%) และ Test Set (20%) แบบ Stratified

**การเชื่อมต่อ:**
- Input port: ต่อจาก Rule Engine (Node 5)
- Output port 1 (TOP): Training set → ต่อไปยัง Keras Network Learner ใน Lab 2
- Output port 2 (BOTTOM): Test set → ต่อไปยัง Keras Network Predictor ใน Lab 2

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Settings**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| How to determine partition size | ● **Relative (%) size of first partition** |
| Size of first partition | `80` (%) |
| Sampling | ● **Stratified sampling** |
| Class column | `default_numeric` |
| Random seed | ✅ เลือก → `12345` |

กด **OK** แล้ว **Execute**

**ผลลัพธ์ที่ได้:**
- Output 1: Training Set ≈ 8,000 แถว (80%)
- Output 2: Test Set ≈ 2,000 แถว (20%)
- สัดส่วน 0/1 ใน Training ควรใกล้เคียงกับ Test (Stratified)

---

### สรุป Lab 1 — Data Preparation for Deep Learning

| ขั้นตอน | Node | Input | Output |
| -------- | ---- | ----- | ------ |
| โหลดข้อมูล | CSV Reader | loan_default.csv | 10,000 แถว |
| จัดการ Missing | Missing Value | มี null | ไม่มี null |
| One-Hot Encoding | Category to Number | 5 String columns | หลาย binary columns |
| Normalize | Normalizer | Numeric [0-1000+] | Numeric [0.0-1.0] |
| แปลง Target | Rule Engine | default_label (String) | default_numeric (0/1) |
| แบ่ง Dataset | Partitioning | 10,000 แถว | 8,000 Train / 2,000 Test |

> **หมายเหตุ:** เก็บ Normalizer Node ไว้เสมอ — Model ที่บันทึกไว้ใน port 2 ต้องใช้ตอน Deploy หรือ Denormalize

---

## Day3_Lab2_Deep_Learning_Classification

**ชื่อ Lab:** Deep Learning for Loan Default Classification
**Dataset:** ข้อมูลที่เตรียมจาก Lab 1 (Prepared Data)
**วัตถุประสงค์:** สร้างและ Train Neural Network สำหรับทำนาย Loan Default โดยใช้ Keras/TensorFlow ใน KNIME
**ตำแหน่งใน Schedule:** Section 3 — 11:15–12:00 น.

---

### Overview Workflow

```
[Training Set จาก Lab1]    [Node 1]                    [Node 2]
Partitioning Port 1    → DL Python Network Creator → Keras Network Learner
                                                          │
[Test Set จาก Lab1]         [Node 3]              [Node 4]
Partitioning Port 2    → Keras Network Predictor → DL to Table Converter
                                                          │
                              [Node 5]              [Node 6]
                            Scorer (Accuracy)    ROC Curve (AUC)
```

---

### Node 1: DL Python Network Creator

**ชื่อ Node:** DL Python Network Creator
**Node Path:** `Deep Learning → DL Python Network Creator`
**วัตถุประสงค์:** สร้าง Neural Network Architecture ด้วย Python/Keras code

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Script**

พิมพ์โค้ด Python ต่อไปนี้ในช่อง Script:

```python
from tensorflow import keras

# นับจำนวน features จาก input table (ไม่รวม target column)
# n_features คือจำนวน columns ที่จะใช้เป็น input ของ network
n_features = len(input_table_1.columns) - 1  # ลบ 1 สำหรับ target column

# สร้าง Neural Network Architecture
model = keras.Sequential([
    keras.layers.Dense(64, activation="relu", input_shape=(n_features,)),
    keras.layers.Dropout(0.3),
    keras.layers.Dense(32, activation="relu"),
    keras.layers.Dropout(0.2),
    keras.layers.Dense(16, activation="relu"),
    keras.layers.Dense(1, activation="sigmoid")
])

# Compile model
model.compile(
    optimizer="adam",
    loss="binary_crossentropy",
    metrics=["accuracy"]
)

# ส่ง model ออก
output_network = model
```

> **คำอธิบาย Architecture:**
> - **Dense(64, relu)** + Dropout(0.3): Hidden Layer 1 — 64 neurons รับรู้ patterns หลัก
> - **Dense(32, relu)** + Dropout(0.2): Hidden Layer 2 — 32 neurons ดึง features ที่ซับซ้อน
> - **Dense(16, relu)**: Hidden Layer 3 — สรุปข้อมูลก่อน output
> - **Dense(1, sigmoid)**: Output Layer — ให้ค่า 0.0-1.0 (ความน่าจะเป็น Default)

**Tab: Flow Variables** (ไม่ต้องตั้งค่า)

กด **OK** แล้ว **Execute**

> **หากเกิด Error:** ตรวจสอบว่าตั้งค่า Python Path แล้ว: `Preferences → KNIME → Python Deep Learning → Python executable`

---

### Node 2: Keras Network Learner

**ชื่อ Node:** Keras Network Learner
**Node Path:** `Deep Learning → Keras Network Learner`
**วัตถุประสงค์:** Train Neural Network ด้วยข้อมูล Training set

**การเชื่อมต่อ:**
- Input port 1 (Keras Network): ต่อจาก DL Python Network Creator (Node 1)
- Input port 2 (Table): ต่อจาก Partitioning Output 1 — Training Data (จาก Lab 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Learning**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Target columns | `default_numeric` |
| Feature columns | เลือก **All columns** แล้ว Remove `default_numeric`, `default_label`, `default`, `loan_id` |
| Epochs | `50` |
| Training batch size | `32` |
| Optimizer | **Adam** |
| Learning rate | `0.001` |
| Loss function | **binary_crossentropy** |

**Tab: Advanced**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Shuffle training data each epoch | ✅ เลือก |
| Use validation data | ✅ เลือก |
| Validation split | `0.2` (20% ของ training data ใช้ monitor) |
| Random seed | `42` |

กด **OK** แล้ว **Execute**

> **ระหว่าง Execute:** จะเห็น Progress bar แสดง Loss และ Accuracy ของแต่ละ Epoch ใน KNIME Console
> - กด Ctrl+Shift+A หรือ View → KNIME Console เพื่อดู output
> - Loss ควรลดลงทุก Epoch → หาก Loss ไม่ลดลงให้ตรวจสอบ data preparation อีกครั้ง

**Output ports:**
- Port 1: Trained Keras Model → ต่อไปยัง Keras Network Predictor
- Port 2: Learning Curve Table → ดูได้โดย Open Second Output Table

---

### Node 3: Keras Network Predictor

**ชื่อ Node:** Keras Network Predictor
**Node Path:** `Deep Learning → Keras Network Predictor`
**วัตถุประสงค์:** ใช้ Model ที่ Train แล้วทำนายผลบน Test set

**การเชื่อมต่อ:**
- Input port 1 (Keras Network): ต่อจาก Keras Network Learner (Node 2), Output port 1
- Input port 2 (Table): ต่อจาก Partitioning Output 2 — Test Data (จาก Lab 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Options**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Feature columns | เลือกเหมือนกับที่ตั้งใน Keras Network Learner |
| Batch size | `32` |
| Append prediction columns | ✅ เลือก |
| Prediction column name | `DL Prediction (default_numeric)` |

กด **OK** แล้ว **Execute**

**ผลลัพธ์:** ตารางเพิ่ม column ค่า Prediction เป็น probability 0.0-1.0

---

### Node 4: DL to Table Converter (หรือ Column to Number)

**ชื่อ Node:** DL to Table (หรือ Math Formula สำหรับ Threshold)
**Node Path:** ใช้ **Math Formula** หรือ **Rule Engine** แปลง probability → class label

**วัตถุประสงค์:** แปลง Output probability (0.0-1.0) จาก DL model เป็น Class label (0 หรือ 1) สำหรับ Scorer

**การเชื่อมต่อ:**
- Input port: ต่อจาก Keras Network Predictor (Node 3)

**ตัวเลือก A: ใช้ Rule Engine**

Node Path: `Manipulation → Column → Convert & Replace → Rule Engine`

```
$DL Prediction (default_numeric)$ >= 0.5 => 1
TRUE => 0
```

Output column name: `DL_Class_Prediction`
Output type: Integer

**ตัวเลือก B: ใช้ Math Formula**

Node Path: `Manipulation → Column → Convert & Replace → Math Formula`

Formula: `floor($DL Prediction (default_numeric)$ + 0.5)`
Output column: `DL_Class_Prediction`

กด **OK** แล้ว **Execute**

---

### Node 5: Scorer

**ชื่อ Node:** Scorer
**Node Path:** `Scoring → Scorer`
**วัตถุประสงค์:** ประเมิน Accuracy, Precision, Recall, F1 ของ Neural Network

**การเชื่อมต่อ:**
- Input port: ต่อจาก DL to Table / Rule Engine (Node 4)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Scorer**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| First column (Actual) | `default_numeric` |
| Second column (Predicted) | `DL_Class_Prediction` |
| Sorting strategy | **Insertion order** |

กด **OK** แล้ว **Execute**

**ดูผลลัพธ์:**
1. คลิกขวาที่ Scorer → **Open First Output Table** → Confusion Matrix
2. คลิกขวาที่ Scorer → **Open Second Output Table** → Accuracy Statistics

**เมตริกที่ควรบันทึก:**

| เมตริก | ค่าที่ได้ |
| ------- | --------- |
| Accuracy | ? |
| Recall (Class 1 = Default) | ? |
| Precision | ? |
| F1 Score | ? |

---

### Node 6: ROC Curve

**ชื่อ Node:** ROC Curve
**Node Path:** `Scoring → ROC Curve`
**วัตถุประสงค์:** วัด AUC (Area Under Curve) เพื่อประเมิน model discrimination

**การเชื่อมต่อ:**
- Input port: ต่อจาก Keras Network Predictor (Node 3) — ใช้ probability ก่อนแปลงเป็น class

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Class column | `default_numeric` |
| Positive class | `1` |
| Prediction column (probability) | `DL Prediction (default_numeric)` |
| Curve columns | เลือก column probability ที่ต้องการ plot |

กด **OK** แล้ว **Execute**

**ดูผลลัพธ์:** คลิกขวา → **Open First Output Table** หรือ **Expand Port View** เพื่อดูกราฟ ROC

---

### ตารางเปรียบเทียบ ML vs Deep Learning

บันทึกผลจากทั้ง Day 2 และ Day 3:

| โมเดล | Accuracy | Recall (Default) | F1 | AUC | ตีความได้ |
| ----- | -------- | ---------------- | -- | --- | --------- |
| Naive Bayes | ? | ? | ? | ? | ✅ สูง |
| Decision Tree | ? | ? | ? | ? | ✅✅ สูงมาก |
| k-NN (k=3) | ? | ? | ? | ? | ⚠️ ปานกลาง |
| SVM (RBF) | ? | ? | ? | ? | ❌ ต่ำ |
| **Neural Network** | ? | ? | ? | ? | ❌ ต่ำ |

> **Audit Insight:** Neural Network อาจให้ Accuracy สูงกว่า แต่สำหรับงาน Internal Audit ที่ต้องการ explainability — Decision Tree มีความเหมาะสมกว่าในการอธิบายเหตุผลการตัดสินใจต่อผู้บริหาร

---

### สรุป Lab 2 — Deep Learning Classification

| Node | ชื่อ | หน้าที่ |
| ---- | ---- | -------- |
| 1 | DL Python Network Creator | สร้าง Keras Sequential Model (64→32→16→1) |
| 2 | Keras Network Learner | Train model, Epoch=50, Batch=32, Adam |
| 3 | Keras Network Predictor | ทำนายผลบน Test set |
| 4 | Rule Engine | แปลง probability → class label (threshold 0.5) |
| 5 | Scorer | วัด Accuracy, Precision, Recall, F1 |
| 6 | ROC Curve | วัด AUC |

---

## Day3_Lab3_CNN_Image_Classification (Concept Lab — Demo)

**ชื่อ Lab:** CNN Image Classification — Concept Lab
**วัตถุประสงค์:** เข้าใจกระบวนการ CNN ผ่าน demo ตัวอย่าง Document Classification
**ตำแหน่งใน Schedule:** Section 4 — 13:00–14:00 น.
**รูปแบบ:** Demo โดยวิทยากร — ไม่ต้องทำตามทุกขั้นตอน

> **หมายเหตุ:** Lab นี้เป็น Concept Lab เพื่อให้เห็นภาพรวม CNN ใน KNIME  
> ในการใช้งานจริงต้องมีชุดรูปภาพ Training ที่ Label แล้ว และ GPU สำหรับ train  
> วิทยากรจะ demo workflow และอธิบาย concept ให้เห็นภาพ

---

### Overview Workflow

```
[Node 1]           [Node 2]           [Node 3]              [Node 4]
Image Loader  →  Image Resizer  →  Image to DL Tensor  →  DL Python
(โหลดรูปภาพ)    (224×224 px)      (แปลงเป็น Tensor)       Network Creator
                                                                │
[Node 5]           [Node 6]           [Node 7]
Keras Network  →  Keras Network  →   Class Label
Learner (CNN)     Predictor          (ประเภทเอกสาร)
```

---

### Node 1: Image Loader

**ชื่อ Node:** Image Loader
**Node Path:** `IO → Read → Image Reader (Table)`
**วัตถุประสงค์:** โหลดรูปภาพเอกสารหลายรูปเข้า KNIME เป็น Table

**การตั้งค่า:**

**Tab: Options**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Input location | เลือก folder ที่มีรูปภาพ (ต้องมีโครงสร้าง subfolder ตามประเภท) |
| File pattern | `*.jpg`, `*.png` |
| File name column | ✅ เพิ่ม column ชื่อไฟล์ |
| Path column | ✅ เพิ่ม column path |

**โครงสร้าง Folder ที่ต้องการ:**
```
documents/
  ├── receipt/        ← รูปใบเสร็จ
  ├── contract/       ← รูปสัญญา
  └── authorization/  ← รูปหนังสือมอบอำนาจ
```

กด **OK** แล้ว **Execute**

---

### Node 2: Image Resizer

**ชื่อ Node:** Image Resizer
**Node Path:** `Image Processing → Transforms → Image Resizer`
**วัตถุประสงค์:** ปรับขนาดรูปภาพให้เท่ากัน (224×224 pixels) — CNN ต้องการ input ขนาดเดียวกันทุกรูป

**การตั้งค่า:**

**Tab: Resizing**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Width | `224` |
| Height | `224` |
| Interpolation | **Bilinear** |

กด **OK** แล้ว **Execute**

---

### Node 3: Image to DL Tensor

**ชื่อ Node:** Image to DL Tensor
**Node Path:** `Deep Learning → Image Processing → Image to DL Tensor`
**วัตถุประสงค์:** แปลงรูปภาพเป็น Tensor ที่ Neural Network ต้องการ

**การตั้งค่า:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Image column | เลือก column ที่มีรูปภาพ |
| Pixel type | **Float** |
| Normalize | ✅ Normalize pixel values to [0, 1] |

กด **OK** แล้ว **Execute**

---

### Node 4: DL Python Network Creator (CNN)

**ชื่อ Node:** DL Python Network Creator
**Node Path:** `Deep Learning → DL Python Network Creator`
**วัตถุประสงค์:** สร้าง CNN Architecture สำหรับ Document Classification

**Python Code สำหรับ CNN:**

```python
from tensorflow import keras

# CNN Architecture สำหรับ Image Classification
# Input: รูปภาพ 224×224×3 (RGB)
model = keras.Sequential([
    # Convolution Block 1
    keras.layers.Conv2D(32, (3, 3), activation="relu", input_shape=(224, 224, 3)),
    keras.layers.MaxPooling2D(2, 2),

    # Convolution Block 2
    keras.layers.Conv2D(64, (3, 3), activation="relu"),
    keras.layers.MaxPooling2D(2, 2),

    # Convolution Block 3
    keras.layers.Conv2D(128, (3, 3), activation="relu"),
    keras.layers.MaxPooling2D(2, 2),

    # Fully Connected
    keras.layers.Flatten(),
    keras.layers.Dense(128, activation="relu"),
    keras.layers.Dropout(0.5),
    keras.layers.Dense(3, activation="softmax")  # 3 ประเภทเอกสาร
])

model.compile(
    optimizer="adam",
    loss="categorical_crossentropy",
    metrics=["accuracy"]
)

output_network = model
```

> **อธิบาย Architecture:**
> - **Conv2D(32)**: ดึง 32 pattern เบื้องต้น (edges, corners)
> - **MaxPooling2D(2,2)**: ลดขนาด feature map ครึ่งหนึ่ง
> - **Conv2D(64), Conv2D(128)**: ดึง patterns ที่ซับซ้อนขึ้นในแต่ละชั้น
> - **Flatten**: แปลง 2D → 1D เพื่อเข้า Dense layer
> - **Dense(3, softmax)**: Output 3 class ด้วย Softmax

---

### Node 5: Keras Network Learner (CNN)

**การตั้งค่า:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Target column | `label` (ชื่อประเภทเอกสาร) |
| Epochs | `20` (Demo ใช้น้อยก่อน) |
| Training batch size | `16` |
| Optimizer | **Adam** |
| Loss function | **categorical_crossentropy** |

> **หมายเหตุ:** การ train CNN บน CPU ใช้เวลานาน — Demo จะใช้ Pre-trained model แทน

---

### Node 6: Keras Network Predictor

**การตั้งค่า:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Feature columns | Image tensor columns |
| Prediction column | `CNN_Prediction` |

---

### Node 7: Class Label (Rule Engine หรือ String to Document Type)

แปลง numeric prediction (0, 1, 2) กลับเป็นชื่อประเภทเอกสาร:

```
$CNN_Prediction$ = 0 => "ใบเสร็จ (Receipt)"
$CNN_Prediction$ = 1 => "สัญญา (Contract)"
$CNN_Prediction$ = 2 => "หนังสือมอบอำนาจ (Authorization)"
TRUE => "Unknown"
```

---

### Use Cases CNN สำหรับงาน Internal Audit

| Use Case | การนำไปใช้ |
| -------- | ---------- |
| **OCR เอกสาร** | อ่านใบเสร็จ, slip โอนเงิน, เอกสารสัญญาอัตโนมัติ |
| **Signature Verification** | ตรวจสอบลายเซ็นในเอกสารทางการ |
| **Stamp Detection** | ตรวจสอบตราประทับบนเอกสารว่าถูกต้องหรือไม่ |
| **Document Classification** | แยกประเภทเอกสาร audit โดยอัตโนมัติ |

---

## Day3_Lab4_LSTM_Time_Series_Prediction

**ชื่อ Lab:** LSTM สำหรับพยากรณ์ยอดธุรกรรมรายวัน
**Dataset:** `time_series_daily_transactions.csv` (730 วัน)
**วัตถุประสงค์:** สร้างโมเดล LSTM พยากรณ์ยอดธุรกรรมธนาคาร และตรวจจับวันที่มียอดผิดปกติ
**ตำแหน่งใน Schedule:** Section 5 — 14:15–15:00 น.

---

### Overview Workflow

```
[Node 1]     [Node 2]    [Node 3]          [Node 4]
CSV Reader → Sort    → Column Filter  →  Normalizer
             (by date)  (total_amount_thb)

[Node 5]          [Node 6]             [Node 7]
Lag Column    →  Column Filter     →  Row Filter
(lag1...lag7)    (lag cols + target)  (Train: date < 2024-09-01)

[Node 8]                     [Node 9]                    [Node 10]
DL Python Network Creator → Keras Network Learner     → Keras Network Predictor
(LSTM Architecture)          (Epochs=100, Batch=16)

[Node 11]         [Node 12]
Denormalizer  →  Line Chart
               (Actual vs Predicted)
```

---

### Node 1: CSV Reader

**ชื่อ Node:** CSV Reader
**Node Path:** `IO → Read → CSV Reader`
**วัตถุประสงค์:** โหลดข้อมูล time_series_daily_transactions.csv

**การตั้งค่า:**

**Tab: Settings**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| File/URL | `C:\TrainingDocument\Machine Learning and Deep Learning with KNIME - Krungsri\dataset\time_series_daily_transactions.csv` |
| Column delimiter | `,` |
| Quote char | `"` |

**Tab: Transformation**

ตรวจสอบ column types:
- `date` → String (จะ parse ด้วย String to Date&Time)
- `total_amount_thb` → Double
- `transaction_count` → Integer
- `branch_id` → String

**Tab: Advanced Settings**
- ✅ Has column header

กด **OK** แล้ว **Execute**

---

### Node 1b: String to Date&Time (Optional — ถ้า date เป็น String)

**ชื่อ Node:** String to Date&Time
**Node Path:** `Manipulation → Column → Convert & Replace → String to Date&Time`

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Selected column | `date` |
| Date format | `yyyy-MM-dd` |
| New column name | `date` (แทนที่ column เดิม) |

กด **OK** แล้ว **Execute**

---

### Node 2: Sort

**ชื่อ Node:** Sort
**Node Path:** `Manipulation → Row → Transform → Sort`
**วัตถุประสงค์:** เรียงข้อมูลตามวันที่จากเก่าไปใหม่ — สำคัญมากสำหรับ Time Series!

**การเชื่อมต่อ:**
- Input port: ต่อจาก CSV Reader (Node 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Sorting**

1. คลิก **Add** เพื่อเพิ่ม sort criteria
2. เลือก column: `date`
3. Sort order: ● **Ascending** (เก่าก่อน)

กด **OK** แล้ว **Execute**

> **ทำไมต้อง Sort?** Time Series ต้องการข้อมูลเรียงตามลำดับเวลา — การสร้าง Lag features ต้องอาศัยลำดับที่ถูกต้อง

---

### Node 3: Column Filter

**ชื่อ Node:** Column Filter
**Node Path:** `Manipulation → Column → Filter → Column Filter`
**วัตถุประสงค์:** เลือกเฉพาะ columns ที่จำเป็น — `date` และ `total_amount_thb`

**การเชื่อมต่อ:**
- Input port: ต่อจาก Sort (Node 2)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Manual Selection**

- **Include columns** (เก็บไว้): `date`, `total_amount_thb`
- **Exclude columns** (ลบออก): ที่เหลือทั้งหมด (`transaction_count`, `branch_id`, ฯลฯ)

กด **OK** แล้ว **Execute**

---

### Node 4: Normalizer

**ชื่อ Node:** Normalizer
**Node Path:** `Manipulation → Column → Transform → Normalizer`
**วัตถุประสงค์:** Normalize ยอดธุรกรรม [0,1] ก่อนสร้าง Lag features — LSTM ต้องการข้อมูล normalized

**การเชื่อมต่อ:**
- Input port: ต่อจาก Column Filter (Node 3)

**การตั้งค่า:**

**Method:** ● **Min-Max Normalization**
- New minimum: `0.0`
- New maximum: `1.0`

**Column Selection:** เพิ่ม `total_amount_thb` เท่านั้น

> **สำคัญมาก:** Port 2 ของ Normalizer คือ **Normalization Model** — ต้องต่อเข้า Denormalizer (Node 11) เพื่อคืนค่า THB จริงหลัง predict

กด **OK** แล้ว **Execute**

---

### Node 5: Lag Column

**ชื่อ Node:** Lag Column
**Node Path:** `Manipulation → Row → Transform → Lag Column`
**วัตถุประสงค์:** สร้าง Sliding Window features — ใช้ยอดธุรกรรม 7 วันก่อนหน้าเป็น input ทำนายวันถัดไป

**การเชื่อมต่อ:**
- Input port: ต่อจาก Normalizer (Node 4)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Options**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Lag column | `total_amount_thb` |
| Lag interval | `1` |
| Maximum lag | `7` |
| Column name prefix | `lag_` |

> **ผลลัพธ์:** สร้าง columns ใหม่: `lag_1`, `lag_2`, `lag_3`, `lag_4`, `lag_5`, `lag_6`, `lag_7`
> - `lag_1` = total_amount_thb ของวันก่อนหน้า 1 วัน
> - `lag_7` = total_amount_thb ของวันก่อนหน้า 7 วัน
> - 7 แถวแรกจะมี Missing Value (ยังไม่มีข้อมูล 7 วันก่อน)

กด **OK** แล้ว **Execute**

**หลัง Execute:** ต่อ Missing Value Node เพื่อลบแถวที่มี null:

**Missing Value Node (เพิ่มเติม):**
- เปิด Dialog → Tab: Default
- Column Type: Double → Method: **Remove row** (ลบแถวที่มี null)

---

### Node 6: Column Filter (เลือก Feature columns)

**ชื่อ Node:** Column Filter
**Node Path:** `Manipulation → Column → Filter → Column Filter`
**วัตถุประสงค์:** เลือกเฉพาะ Lag columns สำหรับ input และ target column

**การเชื่อมต่อ:**
- Input port: ต่อจาก Lag Column (Node 5)

**การตั้งค่า:**

**Include columns (เก็บไว้):**
- `lag_1`, `lag_2`, `lag_3`, `lag_4`, `lag_5`, `lag_6`, `lag_7` (Features สำหรับ LSTM)
- `total_amount_thb` (Target — วันที่จะ Predict)
- `date` (เก็บไว้สำหรับ Row Filter และ Line Chart)

**Exclude columns:** ที่เหลือทั้งหมด

กด **OK** แล้ว **Execute**

---

### Node 7: Row Filter (แบ่ง Train/Test ตามเวลา)

**ชื่อ Lab 4 จำเป็นต้องสร้าง 2 Row Filter nodes:**

#### Node 7a: Row Filter — Training Set

**ชื่อ Node:** Row Filter
**Node Path:** `Manipulation → Row → Filter → Row Filter`
**วัตถุประสงค์:** เลือกข้อมูลก่อน 1 กันยายน 2567 เป็น Training Set (~80%)

**การตั้งค่า:**

**Tab: Row Filter**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Filter by attribute value | ✅ เลือก |
| Column name | `date` |
| Use pattern matching | ✅ เลือก |
| Filter criterion | `< 2024-09-01` |

หรือถ้า date เป็น type Date&Time:
- Tab: **Row Filter**
- Column: `date`
- ● **Use range checking**
- Lower bound: (ว่าง)
- Upper bound: `2024-08-31`
- ✅ Check upper bound

กด **OK** แล้ว **Execute**

#### Node 7b: Row Filter — Test Set

**การตั้งค่า เหมือนข้างบน แต่:**
- Lower bound: `2024-09-01`
- Upper bound: (ว่าง)
- ✅ Check lower bound

> **ทำไมไม่ใช้ Random Split?** Time Series ต้องแบ่งตาม **เวลา** เสมอ:
> - Training = ข้อมูลอดีต → Test = ข้อมูลอนาคต
> - ถ้าใช้ Random split — model จะ "เห็นอนาคต" ระหว่าง training → ผล accuracy สูงเกินจริง (Data Leakage!)

---

### Node 8: DL Python Network Creator (LSTM)

**ชื่อ Node:** DL Python Network Creator
**Node Path:** `Deep Learning → DL Python Network Creator`
**วัตถุประสงค์:** สร้าง LSTM Architecture สำหรับ Time Series Prediction

**การตั้งค่า — Python Code:**

```python
from tensorflow import keras

# LSTM สำหรับ Time Series
# Input shape: (timesteps=7, features=1)
model = keras.Sequential([
    keras.layers.LSTM(
        50,
        return_sequences=True,
        input_shape=(7, 1)
    ),
    keras.layers.Dropout(0.2),
    keras.layers.LSTM(
        25,
        return_sequences=False
    ),
    keras.layers.Dropout(0.2),
    keras.layers.Dense(1)  # Output: ยอดธุรกรรมวันถัดไป
])

model.compile(
    optimizer="adam",
    loss="mse"  # Mean Squared Error สำหรับ Regression
)

output_network = model
```

> **คำอธิบาย LSTM Architecture:**
> - **LSTM(50, return_sequences=True)**: ชั้นแรก 50 units — ส่ง sequence ทั้งหมดต่อไปชั้นต่อไป
> - **Dropout(0.2)**: ป้องกัน Overfitting 20%
> - **LSTM(25, return_sequences=False)**: ชั้นสอง 25 units — ส่งเฉพาะ output สุดท้าย
> - **Dense(1)**: Output layer — ทำนายค่าเดียว (ยอดธุรกรรมวันถัดไป)
> - **loss="mse"**: ใช้ Mean Squared Error สำหรับ Regression (ไม่ใช่ Binary Classification)

กด **OK** แล้ว **Execute**

---

### Node 9: Keras Network Learner (LSTM)

**ชื่อ Node:** Keras Network Learner
**Node Path:** `Deep Learning → Keras Network Learner`
**วัตถุประสงค์:** Train LSTM ด้วยข้อมูล Training set

**การเชื่อมต่อ:**
- Input port 1 (Network): ต่อจาก DL Python Network Creator (Node 8)
- Input port 2 (Table): ต่อจาก Row Filter Training Set (Node 7a)

**การตั้งค่า:**

**Tab: Learning**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Target columns | `total_amount_thb` |
| Feature columns | `lag_1`, `lag_2`, `lag_3`, `lag_4`, `lag_5`, `lag_6`, `lag_7` |
| Epochs | `100` |
| Training batch size | `16` |
| Optimizer | **Adam** |
| Learning rate | `0.001` |
| Loss function | **mse** |

**Tab: Advanced**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Shuffle training data | ❌ ปิด (Time Series ต้องรักษาลำดับ!) |
| Use validation data | ✅ เปิด |
| Validation split | `0.1` (10% ของ training) |

> **สำคัญ:** ต้อง **ปิด Shuffle** สำหรับ Time Series — การสลับลำดับ data จะทำให้ LSTM เรียนรู้ pattern ผิดพลาด

กด **OK** แล้ว **Execute**

> **ระหว่าง Train:** ใช้เวลาประมาณ 2-5 นาทีบน CPU สำหรับ 100 epochs  
> ดู Loss ลดลงใน KNIME Console — Loss ควรลดลงอย่างต่อเนื่อง

---

### Node 10: Keras Network Predictor (LSTM)

**ชื่อ Node:** Keras Network Predictor
**Node Path:** `Deep Learning → Keras Network Predictor`
**วัตถุประสงค์:** ใช้ LSTM ที่ Train แล้วพยากรณ์ยอดธุรกรรม Test set

**การเชื่อมต่อ:**
- Input port 1 (Network): ต่อจาก Keras Network Learner (Node 9), Port 1
- Input port 2 (Table): ต่อจาก Row Filter Test Set (Node 7b)

**การตั้งค่า:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Feature columns | `lag_1` ถึง `lag_7` (เหมือนกับที่ Train) |
| Batch size | `16` |
| Prediction column name | `LSTM_Predicted_normalized` |

กด **OK** แล้ว **Execute**

---

### Node 11: Denormalizer

**ชื่อ Node:** Denormalizer
**Node Path:** `Manipulation → Column → Transform → Denormalizer`
**วัตถุประสงค์:** คืนค่า Predicted ยอดธุรกรรมจาก [0,1] กลับเป็นค่า THB จริง

**การเชื่อมต่อ:**
- Input port 1 (Table): ต่อจาก Keras Network Predictor (Node 10)
- Input port 2 (Normalization Model): ต่อจาก Normalizer (Node 4), **Port 2**

**การตั้งค่า:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Column to denormalize | `LSTM_Predicted_normalized` |
| New column name | `LSTM_Predicted_THB` |

> **หมายเหตุ:** Denormalizer ต้องการ Normalization Model จาก Normalizer เดิม (Node 4 Port 2) เพื่อรู้ค่า Min/Max ที่ใช้ตอน Normalize

กด **OK** แล้ว **Execute**

---

### Node 12: Line Chart

**ชื่อ Node:** Line Chart (Numbers)
**Node Path:** `Views → Chart → Line Chart (Numbers)`
**วัตถุประสงค์:** แสดงกราฟเปรียบเทียบ Actual vs Predicted ยอดธุรกรรม

**การเชื่อมต่อ:**
- Input port: ต่อจาก Denormalizer (Node 11)

**การตั้งค่า:**

**Tab: Chart Options**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| X-axis column | `date` |
| Y-axis columns | เพิ่ม `total_amount_thb` และ `LSTM_Predicted_THB` |
| Chart title | `LSTM Prediction: Actual vs Predicted Daily Transactions` |
| X-axis label | `Date` |
| Y-axis label | `Transaction Amount (THB)` |

กด **OK** แล้ว **Execute**

ดูกราฟ: คลิกขวา → **Open View** หรือ **Expand Port View**

---

### การประเมินผล LSTM Model

**เมตริกที่ใช้สำหรับ Time Series Regression:**

เพิ่ม Math Formula Nodes เพื่อคำนวณ:

**MAE (Mean Absolute Error):**
```
abs($total_amount_thb$ - $LSTM_Predicted_THB$)
```
แล้วใช้ GroupBy → Aggregation → Mean

**MAPE (Mean Absolute Percentage Error):**
```
abs(($total_amount_thb$ - $LSTM_Predicted_THB$) / $total_amount_thb$) * 100
```

**เกณฑ์การประเมิน:**

| MAPE | ระดับความแม่นยำ |
| ---- | --------------- |
| < 5% | ดีมาก |
| 5-10% | ดี |
| 10-20% | พอใช้ |
| > 20% | ต้องปรับปรุง |

> **Audit Application:** วันไหนที่ยอดธุรกรรมจริงต่างจากที่ LSTM คาดการณ์เกิน 20% → ควร flag เป็น "วันที่ต้องตรวจสอบเพิ่มเติม"

---

### สรุป Lab 4 — LSTM Time Series Prediction

| Node | ชื่อ | หน้าที่ |
| ---- | ---- | -------- |
| 1 | CSV Reader | โหลด time_series_daily_transactions.csv |
| 2 | Sort | เรียงตาม date Ascending |
| 3 | Column Filter | เลือก date + total_amount_thb |
| 4 | Normalizer | Normalize [0,1] + บันทึก Model ไว้ |
| 5 | Lag Column | สร้าง lag_1 ถึง lag_7 (Sliding Window) |
| 6 | Column Filter | เลือก lag_1-7 + total_amount_thb + date |
| 7a | Row Filter | แบ่ง Training: date < 2024-09-01 |
| 7b | Row Filter | แบ่ง Test: date >= 2024-09-01 |
| 8 | DL Python Network Creator | สร้าง LSTM(50)→LSTM(25)→Dense(1) |
| 9 | Keras Network Learner | Train: Epoch=100, Batch=16, MSE, Adam |
| 10 | Keras Network Predictor | ทำนายผลบน Test set |
| 11 | Denormalizer | คืนค่า normalized กลับเป็น THB |
| 12 | Line Chart | กราฟ Actual vs Predicted |

---

## สรุป Day 3 — Deep Learning Labs

### สิ่งที่ได้เรียนรู้และฝึกปฏิบัติ

| Lab | หัวข้อ | เทคนิคหลัก |
| --- | ------- | ----------- |
| Lab 1 | Data Prep for DL | One-Hot Encoding, Normalization, Target Encoding, Stratified Split |
| Lab 2 | Neural Network Classification | Keras Sequential, Dense+Dropout, Binary Cross-entropy, Scorer, ROC |
| Lab 3 | CNN Concept (Demo) | Conv2D, MaxPooling, Flatten, Document Classification |
| Lab 4 | LSTM Time Series | Lag Column, Temporal Split, LSTM+Dropout, MSE, Denormalizer |

---

### Node Paths สรุปสำหรับ Deep Learning

| Node | Path ใน KNIME 5.3 |
| ---- | ----------------- |
| CSV Reader | `IO → Read → CSV Reader` |
| Missing Value | `Manipulation → Column → Transform → Missing Value` |
| Category to Number | `Other Data Mining → Binary Classification → Category to Number` |
| Normalizer | `Manipulation → Column → Transform → Normalizer` |
| Rule Engine | `Manipulation → Column → Convert & Replace → Rule Engine` |
| Partitioning | `Manipulation → Row → Transform → Partitioning` |
| Sort | `Manipulation → Row → Transform → Sort` |
| Column Filter | `Manipulation → Column → Filter → Column Filter` |
| Row Filter | `Manipulation → Row → Filter → Row Filter` |
| Lag Column | `Manipulation → Row → Transform → Lag Column` |
| DL Python Network Creator | `Deep Learning → DL Python Network Creator` |
| Keras Network Learner | `Deep Learning → Keras Network Learner` |
| Keras Network Predictor | `Deep Learning → Keras Network Predictor` |
| Denormalizer | `Manipulation → Column → Transform → Denormalizer` |
| Scorer | `Scoring → Scorer` |
| ROC Curve | `Scoring → ROC Curve` |
| Line Chart | `Views → Chart → Line Chart (Numbers)` |
| Image Reader | `IO → Read → Image Reader (Table)` |
| Image Resizer | `Image Processing → Transforms → Image Resizer` |
| Image to DL Tensor | `Deep Learning → Image Processing → Image to DL Tensor` |

---

### Python Environment Checklist

ก่อนเริ่ม Lab ทุกครั้ง ตรวจสอบ:

```
1. KNIME → Preferences → KNIME → Python Deep Learning
   → Python executable: C:\knime_python_env\python.exe ✅

2. Extensions ติดตั้งครบ:
   → KNIME Deep Learning - Keras Integration ✅
   → KNIME Deep Learning - TensorFlow 2 Integration ✅
   → KNIME Python Integration ✅

3. ทดสอบ DL Python Network Creator:
   → วาง Node บน Canvas → Execute
   → ถ้า Execute สำเร็จ = พร้อมใช้งาน ✅
```

---

> **ขอบคุณทุกท่านที่เข้าร่วมหลักสูตร Machine Learning and Deep Learning with KNIME** 🙏
>
> หวังว่าความรู้จาก 3 วันนี้จะเป็นประโยชน์ในการยกระดับงาน Internal Audit ของกลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา
>
> **อาจารย์สามิตร โกยม | IT Genius Institute**
