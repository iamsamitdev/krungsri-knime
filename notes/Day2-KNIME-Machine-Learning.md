## Workshop: Machine Learning and Deep Learning with KNIME — Day 2

## Machine Learning ด้วย KNIME: Classification, Association Rules และ Clustering

---

### บทนำ (Introduction)

วันที่ 2 เราจะก้าวเข้าสู่โลกของ **Machine Learning (ML)** อย่างเต็มตัว โดยใช้ข้อมูลที่เตรียมสะอาดแล้วจาก Day 1 มาสร้างโมเดลที่ช่วยให้ **กลุ่มงานตรวจสอบภายใน (Internal Audit Group)** ของธนาคารกรุงศรีอยุธยาสามารถ:

- **คาดการณ์** ว่าธุรกรรมหรือลูกค้าใดมีความเสี่ยงสูง (**Classification**)
- **ค้นพบรูปแบบ** ว่าสินค้า/บริการใดมักถูกใช้ร่วมกัน (**Association Rules**)
- **จัดกลุ่ม** ลูกค้าตามพฤติกรรม เพื่อวางแผนการตรวจสอบ (**Clustering**)

Machine Learning ไม่ใช่เรื่องของนักวิทยาศาสตร์ข้อมูลเท่านั้น — ด้วย KNIME ผู้ตรวจสอบสามารถสร้างและใช้งาน ML model ได้โดยตรงโดยไม่ต้องเขียนโค้ด

---

### สถาบันผู้จัดอบรม

| รายละเอียด  | ข้อมูล |
| ----------- | ------- |
| สถานที่อบรม | กลุ่มงานตรวจสอบภายใน (Internal Audit Group) ธนาคารกรุงศรีอยุธยา |
| วิทยากร     | อาจารย์สามิตร โกยม |
| สถาบัน      | สถาบันไอทีจีเนียส (IT Genius) |
| วันอบรม Day 2 | วันจันทร์ที่ 19 พฤษภาคม 2569 |
| เวลา        | 09:00–16:00 น. (6 ชั่วโมง) |
| รูปแบบ      | Concept 30% · Hands-on Lab 70% |
| Dataset หลัก | loan_default.csv, customer_churn.csv, market_basket.csv |

---

### วัตถุประสงค์ Day 2

1. เข้าใจแนวคิดหลักของ Machine Learning: Supervised vs Unsupervised, Train/Test Split
2. สามารถอ่านและแปลผล Confusion Matrix, Accuracy, Precision, Recall, F1-Score, AUC-ROC
3. สร้างโมเดล Classification ด้วย Naive Bayes, Decision Tree, k-NN และ SVM ใน KNIME
4. เปรียบเทียบประสิทธิภาพอัลกอริทึมและเลือกโมเดลที่เหมาะสม
5. ค้นหา Association Rules ด้วยหลักการ Support, Confidence, Lift
6. สร้างโมเดล Clustering ด้วย k-Means, Hierarchical Clustering และ DBSCAN

---

### ตารางเวลา — Day 2: วันจันทร์ที่ 19 พฤษภาคม 2569

| Section | เวลา        | หัวข้อ                                              | รูปแบบ        |
| ------- | ----------- | --------------------------------------------------- | ------------- |
| S1      | 09:00–10:00 | Introduction to Machine Learning                    | Concept + Demo |
| S2      | 10:00–10:45 | Evaluation of Algorithms — วัดผลโมเดล               | Concept + Lab |
| —       | 10:45–11:00 | พักเบรก                                             | —             |
| S3      | 11:00–12:00 | Classification I: Naive Bayes & Decision Tree       | Hands-on Lab  |
| —       | 12:00–13:00 | พักรับประทานอาหาร                                   | —             |
| S4      | 13:00–14:00 | Classification II: k-NN & SVM + เปรียบเทียบ         | Hands-on Lab  |
| —       | 14:00–14:15 | พักเบรก                                             | —             |
| S5      | 14:15–15:00 | Association Rule Mining                             | Hands-on Lab  |
| S6      | 15:00–16:00 | Clustering Techniques + Workshop                    | Hands-on Lab  |

---

## Section 1: Introduction to Machine Learning

`09:00–10:00 (60 นาที) | Concept + Demo`

### 1.1 Machine Learning คืออะไร?

**Machine Learning (ML)** คือการทำให้คอมพิวเตอร์ **"เรียนรู้"** จากข้อมูล โดยไม่ต้องเขียนกฎเกณฑ์ทุกอย่างด้วยมือ:

```
Traditional Programming:
┌──────────┐   ┌──────────┐   ┌──────────┐
│  Rules   │──▶│ Computer │──▶│  Output  │
│ (กฎที่   │   │          │   │          │
│ เขียนเอง) │   └──────────┘   └──────────┘
└──────────┘

Machine Learning:
┌──────────┐   ┌──────────┐   ┌──────────┐
│  Data    │──▶│   ML     │──▶│  Rules   │
│ (ข้อมูล  │   │ Algorithm│   │ (Model)  │
│ + คำตอบ) │   └──────────┘   └──────────┘
```

**ตัวอย่างในงาน Internal Audit:**
- ดู transaction หลายล้านรายการ → ML เรียนรู้ว่า pattern ไหนคือ Fraud
- ดูประวัติลูกค้า → ML คาดการณ์ว่าใครจะผิดนัดชำระหนี้

---

### 1.2 ประเภทของ Machine Learning

```
Machine Learning
├── Supervised Learning (มีคำตอบ/label)
│   ├── Classification → ทำนายหมวดหมู่ (Fraud / Not Fraud)
│   └── Regression → ทำนายตัวเลข (ยอดสินเชื่อที่ควรอนุมัติ)
│
├── Unsupervised Learning (ไม่มีคำตอบ)
│   ├── Clustering → จัดกลุ่มข้อมูลที่คล้ายกัน
│   └── Association Rules → ค้นหาความสัมพันธ์
│
└── Reinforcement Learning (เรียนรู้จากรางวัล)
    └── ไม่ครอบคลุมในหลักสูตรนี้
```

**Supervised vs Unsupervised:**

| ลักษณะ | Supervised | Unsupervised |
| ------ | ---------- | ------------ |
| มี Label/คำตอบ | ✅ ใช่ | ❌ ไม่มี |
| ตัวอย่าง | Fraud = 0 หรือ 1 | ไม่รู้ว่ากลุ่มไหน |
| ใช้ใน Day 2 | Classification (S3, S4) | Clustering (S6), Association (S5) |

---

### 1.3 Dataset สำหรับ Classification

วันนี้ใช้ **loan_default.csv** เป็น dataset หลักสำหรับ Classification:

```
loan_default.csv — 400 รายการ, 22 columns

Features (Input):
┌─────────────────────────────────────────────────────────┐
│ age, gender, employment_type, annual_income,            │
│ loan_amount, loan_term_months, credit_score,           │
│ debt_to_income, missed_payments_6m, ...                │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
                   ML Model (เรียนรู้ pattern)
                          │
                          ▼
Target (Output):
┌─────────────────────────────────────────────────────────┐
│ default = 0 → ไม่ผิดนัดชำระ (ปกติ)                     │
│ default = 1 → ผิดนัดชำระ (เสี่ยง)                      │
└─────────────────────────────────────────────────────────┘
```

---

### 1.4 Train/Test Split — ทำไมต้องแบ่ง?

**หลักการสำคัญ:** ห้ามใช้ข้อมูลชุดเดียวกันทั้ง train และ test โมเดล!

```
Data ทั้งหมด (400 rows)
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Training Set 80% (320 rows)   Test Set 20% (80 rows)   │
│  ┌─────────────────────────┐   ┌───────────────┐        │ 
│  │ โมเดลเรียนรู้จากชุดนี้         │   │ ประเมินผล      │        │
│  │ ❌ ห้ามดูข้อมูล test       │  │  ❌ ห้อง train │         │
│  └─────────────────────────┘   └───────────────┘        │
└─────────────────────────────────────────────────────────┘
```

**ทำไมต้องแยก?** เพราะต้องการรู้ว่าโมเดลทำงานได้ดีแค่ไหน **กับข้อมูลใหม่ที่ไม่เคยเห็น**

**Node ใน KNIME: Partitioning**
```
Partitioning Settings:
• Method: Relative — 80% Training / 20% Test
• Sampling: Stratified (เพื่อให้ class สมดุล)
• Random seed: 42 (เพื่อ reproducibility)
```

---

### 1.5 Data Leakage คืออะไร? (สำคัญมาก!)

**Data Leakage** คือการที่ข้อมูลจาก test set "รั่ว" เข้าไปใน training process ทำให้ผลประเมินดีเกินความจริง:

| ประเภท | ตัวอย่าง | ผลที่ตามมา |
| ------ | -------- | ---------- |
| **Target Leakage** | ใช้ feature ที่รู้ค่าหลังเกิดเหตุการณ์ | Accuracy 99% แต่ใช้จริงไม่ได้ |
| **Train/Test Contamination** | Normalize ก่อน split | ค่า mean/std จาก test ปน train |
| **Time Leakage** | ใช้ข้อมูลอนาคตใน training | โมเดลไม่สะท้อนความจริง |

> **กฎทอง:** **Split ก่อน** แล้วค่อย Normalize/Transform — อย่า Normalize ก่อน Split!

---

### 🧪 Lab 1: เตรียมข้อมูล loan_default.csv สำหรับ ML

**ขั้นตอนที่ 1:** นำเข้าข้อมูล
```
CSV Reader → loan_default.csv → Execute
ตรวจสอบ: 400 rows, 22 columns
```

**ขั้นตอนที่ 2:** ตรวจสอบและแปลงประเภทข้อมูล
```
Data Type Caster:
• default → String (KNIME ต้องการ target เป็น String สำหรับ Classification)
→ ใช้ Rule Engine: $default$ = 1 => "Yes" ; TRUE => "No"
```

**ขั้นตอนที่ 3:** เลือก Features ที่เกี่ยวข้อง
```
Column Filter → เก็บ:
age, employment_type, annual_income, loan_amount,
loan_term_months, credit_score, existing_loans,
collateral_type, debt_to_income, missed_payments_6m,
months_with_bank, num_bank_products, default
```

**ขั้นตอนที่ 4:** แบ่ง Train/Test
```
Partitioning:
• Method: Relative, 80% Training
• Sampling: Stratified sampling — column "default"
• Seed: 42
Execute
```

---

## Section 2: Evaluation of Algorithms

`10:00–10:45 (45 นาที) | Concept + Lab`

### 2.1 Confusion Matrix — หัวใจของการประเมิน Classification

```
                        Predicted
                   ┌─────────┬─────────┐
                   │  No (0) │ Yes (1) │
           ┌───────┼─────────┼─────────┤
  Actual   │ No(0) │   TN    │   FP    │
           ├───────┼─────────┼─────────┤
           │ Yes(1)│   FN    │   TP    │
           └───────┴─────────┴─────────┘

TN = True Negative  → ทำนาย No, จริงๆ ก็ No   ✅
TP = True Positive  → ทำนาย Yes, จริงๆ ก็ Yes  ✅
FP = False Positive → ทำนาย Yes, จริงๆ เป็น No ❌ (Type I Error)
FN = False Negative → ทำนาย No, จริงๆ เป็น Yes ❌ (Type II Error)
```

**ตัวอย่างในงาน Audit:**
- **FP (False Positive):** ทำนายว่า Fraud แต่จริงๆ ปกติ → ลูกค้าถูกระงับบัญชีเกินไป
- **FN (False Negative):** ทำนายว่าปกติ แต่จริงๆ Fraud → **อันตรายมาก!** ปล่อย Fraud ผ่าน

---

### 2.2 Metrics สำคัญสำหรับการประเมินโมเดล

| Metric | สูตร | ความหมาย | ดีเมื่อ |
| ------ | ---- | -------- | ------- |
| **Accuracy** | (TP+TN) / All | ทำนายถูกทั้งหมดกี่ % | ข้อมูล balanced |
| **Precision** | TP / (TP+FP) | ในที่ที่ทำนายว่า Yes มีจริงกี่ % | ต้องการลด FP |
| **Recall (Sensitivity)** | TP / (TP+FN) | ในของจริง Yes จับได้กี่ % | ต้องการลด FN |
| **F1-Score** | 2×(P×R)/(P+R) | สมดุลระหว่าง Precision และ Recall | ข้อมูล imbalanced |
| **Specificity** | TN / (TN+FP) | ในของจริง No จับถูกกี่ % | ต้องการลด FP |

**ตัวอย่างเปรียบเทียบ:**

```
สมมติผลการทำนาย Fraud Detection (100 transactions):
TP=45, TN=40, FP=10, FN=5

Accuracy  = (45+40)/100 = 85%
Precision = 45/(45+10) = 81.8%
Recall    = 45/(45+5)  = 90%    ← สำคัญที่สุดสำหรับ Fraud!
F1-Score  = 2×(81.8%×90%)/(81.8%+90%) = 85.7%
```

> **สำหรับ Internal Audit:** ควรเน้น **Recall สูง** เพราะ "พลาด Fraud (FN)" อันตรายกว่า "แจ้งเตือนเกิน (FP)"

---

### 2.3 ROC Curve และ AUC

**ROC Curve** (Receiver Operating Characteristic) คือ graph แสดงความสามารถของโมเดลในทุกระดับ threshold:

```
      1.0 ┤   ___________________
          │  /
    TPR   │ /  ◀ โมเดลดี (AUC สูง)
  (Recall)│/
      0.5 ┤ /
          │/  ◀ โมเดลสุ่ม (AUC = 0.5)
      0.0 ┼─────────────────────
          0.0   0.5   1.0
                FPR (1-Specificity)

AUC (Area Under Curve):
• AUC = 1.0  → โมเดลสมบูรณ์แบบ
• AUC = 0.9+ → โมเดลดีเยี่ยม
• AUC = 0.7-0.9 → โมเดลดี
• AUC = 0.5  → เดาสุ่ม (ไม่มีประโยชน์)
```

**Node ใน KNIME: ROC Curve** (ในกลุ่ม Analytics → Statistics)

---

### 🧪 Lab 2: ทำความเข้าใจ Confusion Matrix

**วัตถุประสงค์:** อ่านและแปลผล Confusion Matrix ได้อย่างถูกต้อง

**ตัวอย่างโจทย์:**
```
โมเดลทำนาย Loan Default (test set 80 รายการ):
TP = 30, TN = 42, FP = 5, FN = 3

คำนวณ:
Accuracy  = ?
Precision = ?
Recall    = ?
F1-Score  = ?

คำถาม: สำหรับงาน Audit ที่ต้องจับผิดนัดชำระให้ได้มากที่สุด
ควรเน้น Metric ใดมากที่สุด? เพราะอะไร?
```

> **เฉลย Recall = 30/(30+3) = 90.9%** — สูงดีสำหรับงาน Audit

---

## Section 3: Classification Algorithms I

`11:00–12:00 (60 นาที) | Hands-on Lab`

### 3.1 Naive Bayes — อัลกอริทึมความน่าจะเป็น

**Naive Bayes** ใช้ **Bayes Theorem** คำนวณความน่าจะเป็นของแต่ละ class:

```
P(Default=Yes | features) ∝ P(Default=Yes) × ∏ P(feature_i | Default=Yes)

ตัวอย่างง่ายๆ:
• P(Fraud | ยอดสูง, ต่างประเทศ) 
  = P(ยอดสูง | Fraud) × P(ต่างประเทศ | Fraud) × P(Fraud)
```

**ข้อดี-ข้อเสีย:**

| ข้อดี | ข้อเสีย |
| ----- | ------- |
| เร็วมาก, เหมาะข้อมูลใหญ่ | สมมติว่า features เป็นอิสระกัน (Naive) |
| ใช้หน่วยความจำน้อย | ไม่เหมาะกับ features ที่ correlate กัน |
| อธิบายผลได้ง่าย | อาจ accuracy ต่ำถ้าข้อมูล features สัมพันธ์กัน |
| ทำงานได้ดีกับ text | — |

**Workflow ใน KNIME:**
```
Training Data ──▶ Naive Bayes Learner ──▶ Model
                                            │
Test Data ──────────────────────────────────▶ Naive Bayes Predictor
                                                    │
                                                    ▼
                                              Scorer (Confusion Matrix)
```

---

### 3.2 Decision Tree — ต้นไม้การตัดสินใจ

**Decision Tree** สร้างกฎการตัดสินใจในรูปแบบต้นไม้ที่อ่านและอธิบายได้:

```
                     [credit_score]
                    /              \
             < 500                >= 500
              /                       \
    [missed_payments_6m]           [debt_to_income]
    /              \               /              \
  >= 3            < 3          > 0.7           <= 0.7
  /                 \           /                  \
Default=Yes      [collateral]  Default=Yes       Default=No
                /           \
            None          Real Estate
              |                |
          Default=Yes      Default=No
```

**Parameters สำคัญ:**

| Parameter | ความหมาย | ผลกระทบ |
| --------- | -------- | ------- |
| **Max Depth** | ความลึกสูงสุดของต้นไม้ | ลึกมาก → Overfit |
| **Min Rows per Node** | แถวขั้นต่ำต่อ node | น้อยมาก → Overfit |
| **Quality Measure** | Gini / Gain Ratio | วิธีเลือก split |

**ข้อดี-ข้อเสีย:**

| ข้อดี | ข้อเสีย |
| ----- | ------- |
| **ตีความได้ง่ายที่สุด** ← เหมาะสำหรับ Audit! | Overfitting ถ้าต้นไม้ลึกเกิน |
| ไม่ต้อง Normalize ข้อมูล | ไม่เสถียร (เปลี่ยนข้อมูลนิดเดียวต้นไม้เปลี่ยนมาก) |
| รองรับทั้ง Numeric และ Categorical | — |
| มองเห็น Feature Importance | — |

> **สำหรับ Internal Audit:** Decision Tree คือ ML model ที่เหมาะที่สุดเพราะ **อธิบายได้** ว่าทำไมถึงทำนายว่า Default/Fraud

---

### 🧪 Lab 3: สร้างโมเดล Naive Bayes และ Decision Tree

**วัตถุประสงค์:** สร้างและประเมินโมเดล Classification สองตัวแรก พร้อมแก้ Class Imbalance ด้วย SMOTE

**Workflow ที่จะสร้าง (13 Nodes):**
```
CSV Reader → Missing Value → Rule Engine → Column Filter → Normalizer → Partitioning
                                                                  │
                                              ┌───────────────────┴───────────────┐
                                              ▼ training set                      ▼ test set
                                           SMOTE (แก้ Imbalance)            (ส่งตรง Predictors)
                                              │
                                    ┌─────────┴──────────┐
                                    ▼                     ▼
                            NB Learner             DT Learner
                                    │                     │
                            NB Predictor ◄── test    DT Predictor ◄── test
                                    │                     │
                               Scorer (NB)           Scorer (DT)
```

**ขั้นตอนที่ 1:** เพิ่ม Missing Value node
```
Node Repository → Manipulation → Column → Transform → Missing Value
เชื่อมต่อ: CSV Reader → Missing Value
แท็บ Default: String → Fix Value → "None" | Integer/Double → Do nothing
Execute
```

**ขั้นตอนที่ 2:** Rule Engine + Column Filter + Normalizer + Partitioning
```
(ตั้งค่าเหมือน Lab 1 ทุกประการ)
Rule Engine: $default$ = 1 => "Yes" ; TRUE => "No" → Append: default_label
Column Filter: เก็บ 13 columns (รวม default_label, ไม่รวม default ตัวเลข)
Normalizer: Min-max [0,1] สำหรับ numeric columns ทั้งหมด
Partitioning: 80% Training / 20% Test, Stratified → default_label, Seed: 42
```

**ขั้นตอนที่ 3:** เพิ่ม SMOTE (แก้ Class Imbalance)
```
Node Repository → Other Data Mining → Imbalanced Data → SMOTE
เชื่อมต่อ: Training port (บน) ของ Partitioning → SMOTE
ตั้งค่า:
  • Class column: S default_label
  • # Nearest neighbor: 5
  • ● Oversample minority classes
  • Enable static seed: ☐ ไม่ติ๊ก
Execute → ตรวจสอบ: จำนวน "Yes" rows เพิ่มขึ้นใกล้เคียง "No" rows
⚠️ SMOTE ต้องอยู่หลัง Partitioning เท่านั้น — ห้ามใส่ก่อน
```

**ขั้นตอนที่ 4:** เพิ่ม Naive Bayes Learner
```
Node Repository → Analytics → Mining → Naive Bayes → Naive Bayes Learner
เชื่อมต่อ: Output ของ SMOTE → NB Learner
ตั้งค่า (แท็บ Options):
  • Classification Column: S default_label
  • Default probability: 0.0001
  • Minimum standard deviation: 0.0001
Execute → ได้ Model port (สี่เหลี่ยมสีน้ำเงิน)
```

**ขั้นตอนที่ 5:** เพิ่ม Naive Bayes Predictor
```
Node Repository → Analytics → Mining → Naive Bayes → Naive Bayes Predictor
เชื่อมต่อ:
  • Input Port 1 ← Model output ของ NB Learner
  • Input Port 2 ← Test port (ล่าง) ของ Partitioning
ตั้งค่า:
  • ☑ Change prediction column name → "Prediction (default_label)"
  • ☐ Append columns with normalized class distribution
Execute
```

**ขั้นตอนที่ 6:** เพิ่ม Scorer (Naive Bayes)
```
Node Repository → Analytics → Statistics → Scorer
เชื่อมต่อ: NB Predictor → Scorer
ตั้งค่า (แท็บ Scorer):
  • First Column:  S default_label
  • Second Column: S Prediction (default_label)
  • Sorting strategy: Lexical
  • Missing values: ● Ignore
Execute → ดู Confusion Matrix และ Accuracy Statistics
```

**ขั้นตอนที่ 7:** สร้าง Decision Tree Learner
```
Node Repository → Analytics → Mining → Decision Tree → Decision Tree Learner
เชื่อมต่อ: Output ของ SMOTE → DT Learner
ตั้งค่า (แท็บ Options):
  • Class column: S default_label
  • Quality measure: Gini index
  • Pruning method: MDL
  • Min number records per node: 5
  • ☑ Average split point
Execute
```

**ขั้นตอนที่ 8:** Decision Tree Predictor + Scorer
```
Node Repository → Analytics → Mining → Decision Tree → Decision Tree Predictor
เชื่อมต่อ:
  • Input Port 1 ← Model output ของ DT Learner
  • Input Port 2 ← Test port ของ Partitioning
ตั้งค่า: ☑ Change prediction column name → "Prediction (default_label)"
Execute

Scorer (DT):
  • First Column:  S default_label
  • Second Column: S Prediction (default_label)
  • Sorting strategy: Insertion order
  • Missing values: ● Ignore
Execute → เปรียบเทียบผลกับ Naive Bayes
```

**ขั้นตอนที่ 9:** เปรียบเทียบผล
```
บันทึก Recall ของ class "Yes" ทั้ง 2 โมเดล
โมเดลไหน Recall สูงกว่า? → เหมาะกับงาน Loan Default Detection มากกว่า
```

---

## Section 4: Classification Algorithms II

`13:00–14:00 (60 นาที) | Hands-on Lab`

### 4.1 k-Nearest Neighbor (k-NN)

**k-NN** ทำนายโดยดูจาก **k รายการที่ใกล้เคียงที่สุด** ในข้อมูล training:

```
ทำนาย loan ใหม่:
•  = Default=Yes (training)
○  = Default=No (training)
★  = ลูกค้าใหม่ที่ต้องการทำนาย

credit_score
    ↑
700 │  ○   ○
    │   ○  ○
600 │    ○  ★  ←ลูกค้าใหม่
    │      •  •
500 │    •  •
    └──────────→ missed_payments
              0  1  2  3

k=3: ดู 3 รายการที่ใกล้ที่สุด = 2 Yes + 1 No → ทำนาย Default=Yes
```

**Parameter k:**

| k | ผลกระทบ |
| - | ------- |
| k เล็ก (1-3) | Overfitting — ไวต่อ noise |
| k พอดี (5-15) | สมดุลดี |
| k ใหญ่เกิน | Underfitting — ไม่จับ pattern |

**ข้อควรระวัง:** k-NN **ต้อง Normalize** ข้อมูลก่อนเสมอ — เพราะ distance sensitive ต่อสเกล!

---

### 4.2 Support Vector Machine (SVM)

**SVM** หาเส้น (hyperplane) ที่แบ่ง class ออกจากกันด้วย **margin สูงสุด**:

```
credit_score
    ↑
700 │  ○○○  │ Margin    • = Default=Yes
    │  ○○   │←────────▶ ○ = Default=No
600 │  ○    │           
    │────── Hyperplane ────
    │       │   •
500 │       │  ••
    └───────────────→ debt_to_income
               
    Margin ← Hyperplane → Margin
    (กว้างมากที่สุด = ดีที่สุด)
```

**Kernel Types:**

| Kernel | ใช้เมื่อ |
| ------ | -------- |
| **Linear** | ข้อมูล linearly separable |
| **RBF (Gaussian)** | ข้อมูลทั่วไป — ใช้บ่อยที่สุด |
| **Polynomial** | ข้อมูลที่มีความสัมพันธ์แบบ polynomial |

---

### 4.3 Workshop: เปรียบเทียบ 4 อัลกอริทึม

**วัตถุประสงค์:** เปรียบเทียบ Naive Bayes, Decision Tree, k-NN, SVM บนข้อมูลเดียวกัน

**Workflow สมบูรณ์:**
```
Training Data ─────┬──▶ Naive Bayes Learner ──▶ NB Predictor ──▶ Scorer (NB)
                   │
                   ├──▶ Decision Tree Learner ──▶ DT Predictor ──▶ Scorer (DT)
                   │
                   ├──▶ K Nearest Neighbor ──────────────────────▶ Scorer (kNN)
                   │
                   └──▶ SVM Learner ────────────▶ SVM Predictor ──▶ Scorer (SVM)
```

**ตารางเปรียบเทียบผล (กรอกขณะทำ Lab):**

| โมเดล | Accuracy | Precision | Recall | F1 | AUC | เวลา Train |
| ----- | -------- | --------- | ------ | -- | --- | ---------- |
| Naive Bayes | ? | ? | ? | ? | ? | ? |
| Decision Tree | ? | ? | ? | ? | ? | ? |
| k-NN (k=3) | ? | ? | ? | ? | ? | ? |
| SVM (RBF) | ? | ? | ? | ? | ? | ? |

---

### 🧪 Lab 4: สร้างโมเดล k-NN และ SVM

**วัตถุประสงค์:** เพิ่ม K Nearest Neighbor และ SVM เข้า Workflow ของ Lab 3 และเปรียบเทียบ 4 อัลกอริทึม

> 📝 **Nodes 1-13 ใน Lab 4 เหมือน Lab 3 ทุกประการ** — ดำเนินการต่อจาก Workflow ของ Lab 3 โดยเพิ่ม Node 14-18

**ขั้นตอนที่ 1:** เพิ่ม K Nearest Neighbor
```
Node Repository → Analytics → Mining → k-Nearest Neighbor → K Nearest Neighbor
⚠️ K Nearest Neighbor ใน KNIME รวม Learner+Predictor ไว้ในตัวเดียว (2 Input Ports)
เชื่อมต่อ:
  • Input Port 1 ← Output ของ SMOTE (balanced training set)
  • Input Port 2 ← Test port (ล่าง) ของ Partitioning
ตั้งค่า (แท็บ Standard settings):
  • Column with class labels: S default_label
  • Number of neighbours to consider (k): 3
  • Weight neighbours by distance: ☐ ไม่ติ๊ก
  • Output class probabilities: ☐ ไม่ติ๊ก
Execute → Output มี column "Class [kNN]"
```

**ขั้นตอนที่ 2:** Scorer (kNN)
```
Node Repository → Analytics → Statistics → Scorer
เชื่อมต่อ: K Nearest Neighbor → Scorer
ตั้งค่า:
  • First Column:  S default_label
  • Second Column: S Class [kNN]   ← ชื่อ default ของ kNN output
  • Sorting strategy: Insertion order
  • Missing values: ● Ignore
Execute → บันทึก Recall ของ "Yes"
```

**ขั้นตอนที่ 3:** SVM Learner
```
Node Repository → Analytics → Mining → SVM → SVM Learner
เชื่อมต่อ: Output ของ SMOTE → SVM Learner
ตั้งค่า (แท็บ Options):
  • Class column: S default_label
  • Overlapping penalty: 1.0
  • Kernel: ● RBF → sigma: 0.5
Execute → ได้ SVM Model port
```

**ขั้นตอนที่ 4:** SVM Predictor + Scorer
```
Node Repository → Analytics → Mining → SVM → SVM Predictor
เชื่อมต่อ:
  • Input Port 1 ← Model output ของ SVM Learner
  • Input Port 2 ← Test port ของ Partitioning
ตั้งค่า: ☑ Change prediction column name → "Prediction (default_label)"
Execute

Scorer (SVM):
  • First Column:  S default_label
  • Second Column: S Prediction (default_label)
  • Sorting strategy: Insertion order
  • Missing values: ● Ignore
Execute
```

**ขั้นตอนที่ 5:** เปรียบเทียบ 4 โมเดล
```
บันทึกผลทั้ง 4 Scorer ลงตาราง:

| โมเดล          | Accuracy | Recall (Yes) | Cohen's κ |
|----------------|----------|-------------|-----------|
| Naive Bayes    |          |             |           |
| Decision Tree  |          |             |           |
| K Nearest Nbr  |          |             |           |
| SVM (RBF)      |          |             |           |

→ โมเดลที่ Recall "Yes" สูงสุด = เหมาะกับงาน Internal Audit ที่สุด
```

---

## Section 5: Association Rule Mining

`14:15–15:00 (45 นาที) | Hands-on Lab`

### 5.1 Association Rules คืออะไร?

**Association Rule Mining** คือการค้นหาว่า "ถ้ามี A มักจะมี B ด้วย" ในชุดของรายการ (itemset):

```
ตัวอย่าง Market Basket Analysis (ธนาคาร):
• 70% ของลูกค้าที่เปิด "Savings Account" มักจะใช้ "Mobile Banking" ด้วย
• 60% ของลูกค้าที่กู้ "Home Loan" มักจะซื้อ "Life Insurance" ด้วย

→ ธนาคารสามารถใช้ข้อมูลนี้:
  • Cross-sell สินค้าที่เหมาะสม
  • ตรวจสอบว่าลูกค้าได้รับบริการครบหรือไม่
  • วางแผน Audit ตามกลุ่มผลิตภัณฑ์
```

---

### 5.2 3 ตัวชี้วัดสำคัญ: Support, Confidence, Lift

**Support** — ความถี่ที่ itemset ปรากฏในข้อมูลทั้งหมด:
```
Support(A → B) = จำนวน transactions ที่มีทั้ง A และ B
                  ─────────────────────────────────────
                       จำนวน transactions ทั้งหมด

ตัวอย่าง: ลูกค้า 250 ราย, 100 รายมีทั้ง Savings + Mobile Banking
Support = 100/250 = 0.40 = 40%
```

**Confidence** — ความน่าเชื่อถือของกฎ:
```
Confidence(A → B) = P(B | A) = Support(A ∩ B) / Support(A)

ตัวอย่าง: 130 รายมี Savings, 100 รายมีทั้ง Savings + Mobile
Confidence = 100/130 = 76.9%
→ ถ้ามี Savings มี 76.9% โอกาสที่จะมี Mobile Banking ด้วย
```

**Lift** — ความสัมพันธ์เกินความบังเอิญ:
```
Lift(A → B) = Confidence(A → B) / Support(B)

Lift > 1 → A และ B มีความสัมพันธ์กัน (positive association)
Lift = 1 → ไม่มีความสัมพันธ์ (เป็นความบังเอิญ)
Lift < 1 → A และ B มีความสัมพันธ์แบบตรงข้าม

ตัวอย่าง: Support(Mobile) = 60%
Lift = 76.9% / 60% = 1.28 → มีความสัมพันธ์เกินความบังเอิญ
```

---

### 5.3 โครงสร้างข้อมูลสำหรับ Association Rules

**market_basket.csv** มีโครงสร้างแบบ Transaction-Item Format:

```
basket_id | product_service
BID00001  | Savings Account
BID00001  | Mobile Banking       ← Basket เดียว, หลาย items
BID00001  | E-Statement
BID00002  | Home Loan
BID00002  | Life Insurance
BID00002  | Fire Insurance
...

→ KNIME ต้องการให้แปลงเป็น One-Hot Format ก่อน:

basket_id | Savings | Mobile | Home Loan | Life Ins | ...
BID00001  |   1     |   1    |     0     |    0     | ...
BID00002  |   0     |   0    |     1     |    1     | ...
```

---

### 🧪 Lab 5: Association Rule Mining บนข้อมูลบริการธนาคาร

**วัตถุประสงค์:** ค้นหาบริการที่ลูกค้ามักใช้ร่วมกัน เพื่อวางแผน Cross-sell และ Audit

**Workflow (5 Nodes):**
```
CSV Reader (market_basket.csv)
      │
      ▼
Pivot (Long format → Wide format: 1 row ต่อ basket)
      │
      ▼
Association Rule Learner
• Min Support:    0.10 (10%)
• Min Confidence: 0.50 (50%)
      │
      ▼
Association Rule Filter
• Min Lift: 1.1
      │
      ▼
Association Rules to Table (แสดงกฎ พร้อม Support/Confidence/Lift)
```

**ขั้นตอนที่ 1:** อ่านข้อมูล
```
Node Repository → IO → Read → CSV Reader
File: C:\KNIME_MASTER\datasets\Mockup Resources\market_basket.csv
Execute → ตรวจสอบ: 778 rows, 2 columns (basket_id, product_service)
```

**ขั้นตอนที่ 2:** แปลงข้อมูลเป็น Wide format
```
Node Repository → Manipulation → Row → Transform → Pivot
เชื่อมต่อ: CSV Reader → Pivot
แท็บ Pivoting:
  • Group columns: basket_id
  • Pivot columns: product_service
แท็บ Manual Aggregation:
  • product_service → Count
Execute → ได้ ~250 rows (1 row ต่อ basket, แต่ละ column คือ product)
```

**ขั้นตอนที่ 3:** สร้าง Association Rules
```
Node Repository → Analytics → Mining → Association Rules → Association Rule Learner
เชื่อมต่อ: Pivot → Association Rule Learner
ตั้งค่า:
  • Min Support:    0.10
  • Min Confidence: 0.50
  • ☑ Output association rules
Execute
```

**ขั้นตอนที่ 4:** กรองกฎด้วย Lift
```
Node Repository → Analytics → Mining → Association Rules → Association Rule Filter
เชื่อมต่อ: Association Rule Learner → Association Rule Filter
ตั้งค่า:
  • Min Lift: 1.1   ← กรองเฉพาะกฎที่เกินความบังเอิญ 10%
Execute
```

**ขั้นตอนที่ 5:** ดูกฎในรูปแบบ Table
```
Node Repository → Analytics → Mining → Association Rules → Association Rules to Table
เชื่อมต่อ: Association Rule Filter → Association Rules to Table
Execute → คลิกขวา → Open View

ตัวอย่างผลที่อาจได้:
Antecedent              Consequent          Support  Confidence  Lift
{Savings Account}  →  {Mobile Banking}      0.42      0.78       1.31
{Home Loan}        →  {Life Insurance}      0.25      0.65       1.45
{Credit Card}      →  {Travel Insurance}    0.18      0.72       1.62
```

> 💡 **Audit Application:**
> - ลูกค้าที่มี Home Loan แต่ **ไม่มี** Life Insurance → Compliance risk → Flag ไว้ตรวจสอบ
> - กฎที่ Lift สูง = ความสัมพันธ์จริง ไม่ใช่ความบังเอิญ — นำไปวางแผน Audit เป็น segment

---

## Section 6: Clustering Techniques

`15:00–16:00 (60 นาที) | Hands-on Lab`

### 6.1 Clustering คืออะไร?

**Clustering** คือการจัดกลุ่มข้อมูลที่คล้ายกันเข้าด้วยกัน โดยไม่มี label กำหนด:

```
Before Clustering (ไม่รู้กลุ่ม):    After Clustering (แบ่งกลุ่มได้):
    •   •   •                         ●   ●   ■
   • •   • •  •                      ● ●   ■ ■  ▲
  •   •   •                         ●   ●   ■
      •     •  •   •                    ■     ▲  ▲   ▲

                              Cluster 1 (●) = ลูกค้า Premium Risk-Low
                              Cluster 2 (■) = ลูกค้า Standard Risk-Med
                              Cluster 3 (▲) = ลูกค้า Basic Risk-High
```

**ประโยชน์ในงาน Audit:**
- แบ่งกลุ่มลูกค้าตามพฤติกรรม → วางแผน Audit เป็น segment
- ค้นหา outlier ที่ไม่เข้ากลุ่มใด → สงสัยว่าผิดปกติ

---

### 6.2 k-Means Clustering

**k-Means** คืออัลกอริทึม Clustering ที่นิยมที่สุด:

**หลักการ:**
```
1. เลือก k centroids (จุดศูนย์กลาง) แบบสุ่ม
2. กำหนดแต่ละ data point ไปยัง centroid ที่ใกล้ที่สุด
3. คำนวณ centroid ใหม่จากค่าเฉลี่ยของแต่ละกลุ่ม
4. ทำซ้ำ 2-3 จนกว่า centroid ไม่เปลี่ยน

Iteration 1:     Iteration 2:     Converged:
  ●  ■            ●  ■             ●  ■
● × ■           ●× ■            ●× ■
  ●  ■            ●  ■             ●  ■
     ×                ×               ×
  ▲  ▲            ▲  ▲             ▲  ▲
```

**วิธีหา k ที่เหมาะสม — Elbow Method:**
```
Within-Cluster Sum of Squares (WCSS)
    ↑
    │•
    │  •
    │    •
    │      •
    │        •_____•_____•___
    └──────────────────────→ k
    1  2  3  4  5  6  7  8

เลือก k ตรง "ข้อศอก" (Elbow) ที่ WCSS ลดลงน้อยลงมาก
ตัวอย่าง: k=3 หรือ k=4 ดูเหมือน elbow point
```

---

### 6.3 Hierarchical Clustering

**Hierarchical Clustering** สร้างต้นไม้ (Dendrogram) แสดงลำดับการรวมกลุ่ม:

```
Dendrogram:
             ┌───────────────────────┐
             │                       │
       ┌─────┴─────┐           ┌─────┴─────┐
       │           │           │           │
   ┌───┴───┐   ┌───┴───┐   ┌───┴───┐   ┌───┴───┐
   CUS001 CUS002 CUS003 CUS004 CUS005 CUS006 CUS007

→ ตัด Dendrogram ที่ความสูงต่างกัน = ได้จำนวน cluster ต่างกัน
→ ไม่ต้องกำหนด k ล่วงหน้า!
```

**Linkage Methods:**

| Method | วิธีวัดระยะห่างระหว่าง cluster |
| ------ | ----------------------------- |
| **Single Linkage** | ระยะสั้นสุดระหว่างสมาชิก |
| **Complete Linkage** | ระยะไกลสุดระหว่างสมาชิก |
| **Average Linkage** | ระยะเฉลี่ยระหว่างสมาชิก (นิยมที่สุด) |
| **Ward's Method** | ลด variance ภายใน cluster |

---

### 6.4 DBSCAN — Density-Based Clustering

**DBSCAN** จัดกลุ่มตามความหนาแน่นของข้อมูล — **ไม่ต้องกำหนด k** และค้นพบ outlier ได้:

```
Parameters:
• ε (epsilon) = รัศมีของ neighborhood
• MinPts = จำนวน point ขั้นต่ำใน neighborhood

     ε
  ←──→
• • • •          Core Point: มี ≥ MinPts ภายใน ε
•  •  •          Border Point: อยู่ใน ε ของ Core แต่มี point น้อยกว่า MinPts
              ×  Noise/Outlier: ไม่อยู่ใน ε ของ Core Point ใดเลย
```

**เปรียบเทียบ Clustering Algorithms:**

| Feature | k-Means | Hierarchical | DBSCAN |
| ------- | ------- | ------------ | ------ |
| ต้องกำหนด k | ✅ ใช่ | ❌ ไม่ต้อง | ❌ ไม่ต้อง |
| รูปร่าง cluster | วงกลม | ยืดหยุ่น | ยืดหยุ่นมาก |
| ตรวจ Outlier | ❌ | ❌ | ✅ ดีมาก |
| ขนาดข้อมูล | ใหญ่ได้ | เล็ก-กลาง | กลาง |
| อธิบายได้ | ปานกลาง | ดี (Dendrogram) | ปานกลาง |

---

### 🧪 Lab 6: Customer Segmentation ด้วย Clustering

**วัตถุประสงค์:** จัดกลุ่มลูกค้าด้วย k-Means และ DBSCAN เพื่อหา pattern และ Outlier สำหรับ Internal Audit

**Dataset:** customer_churn.csv (350 rows, 21 columns)

**Features สำหรับ Clustering (8 columns):**
`credit_score`, `tenure_months`, `account_balance`, `num_products`,
`login_count_30d`, `transactions_3m`, `nps_score`, `complaint_count`

**Workflow (8 Nodes):**
```
CSV Reader (customer_churn.csv)
      │
      ▼
Column Filter (เลือก 8 columns)
      │
      ▼
Normalizer (Min-Max [0,1]) ← สำคัญ! Clustering ไวต่อ scale มาก
      │
 ┌────┴──────────────────────────────────────┐
 ▼                                           ▼
k-Means (k=3, seed=42)               DBSCAN (ε=0.3, MinPts=5)
      │                                      │
      ▼                                      ▼
Color Manager                         Row Filter (กรอง Noise)
      │                                      │
      ▼                                      ▼
Scatter Plot                          รายชื่อ Outlier → Audit Focus!
      │
      ▼
GroupBy (by Cluster) → วิเคราะห์แต่ละกลุ่ม
```

**ขั้นตอนที่ 1:** เตรียมข้อมูล
```
Node Repository → IO → Read → CSV Reader
File: C:\KNIME_MASTER\datasets\Mockup Resources\customer_churn.csv
Execute → ตรวจสอบ: 350 rows, 21 columns

Node Repository → Manipulation → Column → Filter → Column Filter
เก็บเฉพาะ 8 columns ที่ระบุข้างต้น → กด Ok and Execute

Node Repository → Manipulation → Column → Transform → Normalizer
Includes: ทุก column ทั้ง 8 ตัว | Method: Min-max [0,1]
กด Ok → Execute
```

**ขั้นตอนที่ 2:** k-Means Clustering
```
Node Repository → Analytics → Mining → Clustering → k-Means
เชื่อมต่อ: Normalizer → k-Means
ตั้งค่า (แท็บ k-Means Settings):
  • Number of clusters (k): 3
  • Max number of iterations: 100
  • ☑ Enable static random seed → 42
  • Distance measure: Euclidean
Execute → Output มี column "Cluster" (cluster_0, cluster_1, cluster_2)
```

**ขั้นตอนที่ 3:** แสดงผลด้วย Color Manager + Scatter Plot
```
Node Repository → Views → Property → Color Manager
เชื่อมต่อ: k-Means → Color Manager
ตั้งค่า: Column → Cluster | Mapping: Nominal
Execute

Node Repository → Views → Chart → Scatter Plot
เชื่อมต่อ: Color Manager → Scatter Plot
ตั้งค่า: X = credit_score | Y = account_balance
Execute → คลิกขวา → Open View → ดู 3 กลุ่มแยกสี
```

**ขั้นตอนที่ 4:** วิเคราะห์แต่ละ Cluster ด้วย GroupBy
```
Node Repository → Manipulation → Row → Transform → GroupBy
เชื่อมต่อ: k-Means → GroupBy
แท็บ Groups: Group column → Cluster
แท็บ Manual Aggregation → Mean ของ:
  credit_score, account_balance, complaint_count,
  num_products, login_count_30d
Execute → ตีความผล:

Cluster 0: credit สูง, balance สูง, complaint น้อย → "Premium — Low Risk"
Cluster 1: ค่าปานกลางทุกด้าน                       → "Standard — Medium Risk"
Cluster 2: credit ต่ำ, complaint สูง               → "High Risk 🔴 — Audit Focus!"
```

**ขั้นตอนที่ 5:** DBSCAN — ค้นหา Outlier
```
Node Repository → Analytics → Mining → Clustering → DBSCAN
เชื่อมต่อ: Normalizer → DBSCAN (ต่อขนานกับ k-Means)
ตั้งค่า:
  • Epsilon (ε): 0.3
  • Minimum points (MinPts): 5
  • Distance measure: Euclidean
Execute → Output มี column "Cluster" — data ที่ผิดปกติ = ค่า "Noise"

เพิ่ม Row Filter ต่อจาก DBSCAN:
  • Filter column: Cluster | Operator: equals | Value: Noise
Execute → ได้รายชื่อลูกค้าพฤติกรรมผิดปกติ = Audit Investigation Targets!
```

> 💡 **k-Means vs DBSCAN สำหรับ Internal Audit:**
> - **k-Means:** จัดกลุ่ม Risk Level (Low/Medium/High) → วางแผน Audit เป็น Segment
> - **DBSCAN:** ค้นหา Anomaly ที่ไม่เข้ากลุ่มใด → สงสัย Fraud หรือพฤติกรรมผิดปกติ

---

### สรุป Day 2

เมื่อจบวันที่ 2 ผู้เรียนได้เรียนรู้และฝึกปฏิบัติครบดังนี้:

**Section 1: Introduction to Machine Learning**

- ✅ เข้าใจ Supervised vs Unsupervised Learning
- ✅ ทำ Train/Test Split ด้วย Partitioning node อย่างถูกต้อง
- ✅ หลีกเลี่ยง Data Leakage: Split ก่อน Normalize เสมอ

**Section 2: Evaluation of Algorithms**

- ✅ อ่านและแปลผล Confusion Matrix (TP, TN, FP, FN)
- ✅ คำนวณ Accuracy, Precision, Recall, F1-Score
- ✅ ดูและแปลผล ROC Curve และ AUC
- ✅ เลือก metric ที่เหมาะกับ business problem (Recall สำหรับ Fraud/Default)

**Section 3: Classification I**

- ✅ จัดการ Missing Values ด้วย Missing Value node
- ✅ แก้ Class Imbalance ด้วย SMOTE ใน training set
- ✅ สร้างโมเดล Naive Bayes ด้วย Naive Bayes Learner/Predictor/Scorer
- ✅ สร้างโมเดล Decision Tree (Gini, MDL Pruning) และประเมินด้วย Scorer
- ✅ เข้าใจว่าทำไม Recall ของ class "Yes" สำคัญกว่า Accuracy

**Section 4: Classification II**

- ✅ สร้างโมเดล K Nearest Neighbor (k=3) — combined learner+predictor node
- ✅ สร้างโมเดล SVM (RBF kernel, sigma=0.5, C=1.0)
- ✅ เปรียบเทียบ 4 อัลกอริทึม: NB / DT / k-NN / SVM ด้วย Recall เป็น KPI หลัก
- ✅ เลือกโมเดลที่เหมาะกับงาน Audit ได้อย่างมีเหตุผล

**Section 5: Association Rule Mining**

- ✅ เข้าใจ Support, Confidence, Lift
- ✅ แปลง Long format เป็น Wide format ด้วย Pivot node
- ✅ สร้าง Association Rules ด้วย Association Rule Learner (Apriori)
- ✅ กรองกฎด้วย Association Rule Filter (Min Lift=1.1)
- ✅ แสดงผลด้วย Association Rules to Table และตีความกฎสำหรับ Audit

**Section 6: Clustering**

- ✅ สร้าง k-Means (k=3) จัดกลุ่มลูกค้าเป็น Low/Medium/High Risk
- ✅ แสดงผล Cluster ด้วย Color Manager และ Scatter Plot
- ✅ วิเคราะห์แต่ละ Cluster ด้วย GroupBy (Mean per Cluster)
- ✅ ใช้ DBSCAN (ε=0.3, MinPts=5) ค้นหา Outlier → Audit Investigation Targets

> **วันที่ 3 (Day 3):** เราจะก้าวสู่ **Deep Learning** — Neural Networks, TensorFlow, CNN และ LSTM ซึ่งเป็นเทคนิคที่ทรงพลังที่สุดสำหรับข้อมูลซับซ้อนเช่น รูปภาพและ time series ครับ
