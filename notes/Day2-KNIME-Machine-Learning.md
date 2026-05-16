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
┌─────────────────────────────────────────────┐
│                                             │
│  Training Set 80% (320 rows)   Test Set 20% (80 rows)│
│  ┌─────────────────────────┐   ┌───────────────┐      │
│  │ โมเดลเรียนรู้จากชุดนี้  │   │ ประเมินผล     │      │
│  │ ❌ ห้ามดูข้อมูล test    │   │ ❌ ห้อง train │      │
│  └─────────────────────────┘   └───────────────┘      │
└─────────────────────────────────────────────┐         │
                                              └─────────┘
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

**วัตถุประสงค์:** สร้างและประเมินโมเดล Classification แรก

**Workflow ที่จะสร้าง:**
```
loan_default.csv
      │
      ▼
CSV Reader → Rule Engine (default → "Yes"/"No") → Column Filter → Normalizer
      │
      ▼
Partitioning (80/20, Stratified)
      │
 ┌────┴────┐
 Training  Test
 │          │
 ▼          │
Naive Bayes ├──▶ Naive Bayes Predictor ──▶ Scorer ──▶ ROC Curve
Learner     │
            │
Decision    ├──▶ Decision Tree Predictor ──▶ Scorer
Tree Learner│
            └──▶ Decision Tree to Image (ดูภาพต้นไม้)
```

**ขั้นตอนที่ 1:** เพิ่ม Naive Bayes Learner
```
Node Repository → Analytics → Mining → Naive Bayes → Naive Bayes Learner
เชื่อมกับ Training port ของ Partitioning
ดับเบิ้ลคลิก → Target column: default
Execute
```

**ขั้นตอนที่ 2:** เพิ่ม Naive Bayes Predictor
```
Naive Bayes Predictor → เชื่อม:
• Input port 1 ← Output ของ Naive Bayes Learner (Model)
• Input port 2 ← Test port ของ Partitioning
Execute
```

**ขั้นตอนที่ 3:** เพิ่ม Scorer
```
Scorer → เชื่อมกับ Naive Bayes Predictor
ตั้งค่า: First column = default (actual), Second column = Prediction
Execute → ดู Confusion Matrix
```

**ขั้นตอนที่ 4:** สร้าง Decision Tree
```
Decision Tree Learner → เชื่อมกับ Training port
ตั้งค่า: Target = default, Max Depth = 5, Min Rows = 5
Execute

Decision Tree to Image → เชื่อมกับ Learner
ดูภาพต้นไม้การตัดสินใจ
```

**ขั้นตอนที่ 5:** เปรียบเทียบผล
```
บันทึกค่า Accuracy, Recall ของทั้ง 2 โมเดล
โมเดลไหนเหมาะกับงาน Audit มากกว่า? เพราะอะไร?
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
                   ├──▶ k-NN Learner ──────────▶ kNN Predictor ──▶ Scorer (kNN)
                   │
                   └──▶ SVM Learner ────────────▶ SVM Predictor ──▶ Scorer (SVM)
                                                                          │
                                               ┌──────────────────────────┘
                                               ▼
                                    ROC Curve (เปรียบเทียบ 4 เส้น)
```

**ตารางเปรียบเทียบผล (กรอกขณะทำ Lab):**

| โมเดล | Accuracy | Precision | Recall | F1 | AUC | เวลา Train |
| ----- | -------- | --------- | ------ | -- | --- | ---------- |
| Naive Bayes | ? | ? | ? | ? | ? | ? |
| Decision Tree | ? | ? | ? | ? | ? | ? |
| k-NN (k=5) | ? | ? | ? | ? | ? | ? |
| SVM (RBF) | ? | ? | ? | ? | ? | ? |

---

### 🧪 Lab 4: สร้างโมเดล k-NN และ SVM

**ขั้นตอนที่ 1:** สร้าง k-NN Learner
```
Node Repository → Analytics → Mining → k-Nearest Neighbor → k-NN Learner
เชื่อมกับ Training port
ตั้งค่า: k = 5, Distance: Euclidean
Target column: default
Execute
```

**ขั้นตอนที่ 2:** k-NN Predictor + Scorer
```
k-NN Predictor → เชื่อม Model + Test Data
Scorer → คำนวณ Confusion Matrix
บันทึกค่า Accuracy, Recall
```

**ขั้นตอนที่ 3:** SVM Learner
```
Node Repository → Analytics → Mining → SVM → SVM Learner
เชื่อมกับ Training port
ตั้งค่า: Kernel: RBF, C: 1.0
Target column: default
Execute
```

**ขั้นตอนที่ 4:** ROC Curve เปรียบเทียบ
```
ROC Curve node → เชื่อมกับ Scorer ทั้ง 4 ตัว
Execute → ดูเส้น ROC ของทุกโมเดลในกราฟเดียว
เปรียบเทียบ AUC ของแต่ละโมเดล
```

**ขั้นตอนที่ 5:** customer_churn.csv
```
ทดลองเปลี่ยนมาใช้ customer_churn.csv
Target column: churn
สังเกตว่าผลเปลี่ยนไปอย่างไร?
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

**วัตถุประสงค์:** ค้นหาบริการที่ลูกค้ามักใช้ร่วมกัน

**Workflow:**
```
market_basket.csv
      │
      ▼
CSV Reader
      │
      ▼
Pivot (แปลงเป็น One-Hot: basket_id เป็น rows, product เป็น columns)
      │
      ▼
One-to-Many Association Rule Learner (Apriori)
• Min Support:    0.1 (10%)
• Min Confidence: 0.5 (50%)
      │
      ▼
Association Rule Filter
• Min Lift: 1.1
      │
      ▼
Association Rules Viewer (ดูกฎ)
```

**ขั้นตอนที่ 1:** อ่านข้อมูล
```
CSV Reader → market_basket.csv → Execute
ตรวจสอบ: 778 rows
```

**ขั้นตอนที่ 2:** แปลงข้อมูลเป็น Pivot format
```
Pivot node:
• Row columns: basket_id
• Column columns: product_service
• Aggregation: Count
Execute → ได้ 250 rows (1 row ต่อ 1 basket)
```

**ขั้นตอนที่ 3:** สร้าง Association Rules
```
Association Rule Learner:
Min Support: 0.10
Min Confidence: 0.50
Execute
```

**ขั้นตอนที่ 4:** กรองและดูผล
```
Association Rule Filter:
• Min Lift: 1.1
Execute → ดูกฎที่น่าสนใจ

ตัวอย่างผลที่อาจได้:
{Savings Account} → {Mobile Banking}  Support=0.42, Conf=0.78, Lift=1.31
{Home Loan} → {Life Insurance}        Support=0.25, Conf=0.65, Lift=1.45
{Credit Card} → {Travel Insurance}    Support=0.18, Conf=0.72, Lift=1.62
```

> **Audit Application:** ถ้าลูกค้ามี Home Loan แต่ไม่มี Life Insurance → อาจเป็น risk ที่ต้องตรวจสอบ

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

**วัตถุประสงค์:** แบ่งกลุ่มลูกค้าด้วย customer_churn.csv เพื่อวางแผน Audit

**Dataset:** customer_churn.csv (350 rows, 21 columns)

**Features สำหรับ Clustering:**
- `credit_score`, `tenure_months`, `account_balance`
- `num_products`, `login_count_30d`, `transactions_3m`
- `nps_score`, `complaint_count`

**Workflow:**
```
customer_churn.csv
      │
      ▼
CSV Reader → Column Filter (เลือก features ที่ระบุ)
      │
      ▼
Normalizer (Min-Max) ← สำคัญ! ต้อง Normalize ก่อน Clustering
      │
 ┌────┴──────────────────────────┐
 ▼                               ▼
k-Means (k=3)           DBSCAN (ε=0.3, MinPts=5)
      │                          │
      ▼                          ▼
Cluster Assigner            Cluster Assigner
      │                          │
      ▼                          ▼
Color Manager               Scatter Plot
      │
      ▼
Scatter Plot (เห็น 3 กลุ่ม)
      │
      ▼
GroupBy (by Cluster) → Statistics ต่อกลุ่ม
```

**ขั้นตอนที่ 1:** k-Means Clustering
```
k-Means node:
• Number of Clusters: 3
• Max Iterations: 100
• Distance: Euclidean
Execute → ดู cluster assignment ของแต่ละ customer
```

**ขั้นตอนที่ 2:** วิเคราะห์แต่ละ Cluster
```
GroupBy (by Cluster):
• Mean: credit_score, account_balance, complaint_count, churn
Execute → ดูลักษณะเฉพาะของแต่ละกลุ่ม

ตีความผล (ตัวอย่าง):
Cluster 0: credit สูง, balance สูง, complaint น้อย → "Premium Low Risk"
Cluster 1: credit ปานกลาง, balance ปานกลาง        → "Standard Medium Risk"
Cluster 2: credit ต่ำ, complaint สูง, churn สูง    → "High Risk - Attention!"
```

**ขั้นตอนที่ 3:** หา Optimum k ด้วย Elbow Method
```
k-Means node → เปลี่ยน k เป็น 2, 3, 4, 5, 6
บันทึก Within-Cluster Sum of Squares (WCSS) ของแต่ละ k
วาดกราฟ → หา Elbow point
```

**ขั้นตอนที่ 4:** DBSCAN
```
DBSCAN node:
• Epsilon: 0.3
• MinPts: 5
Execute → สังเกตว่ามี Outlier (Noise) จำนวนเท่าใด
Outlier เหล่านี้ = ลูกค้าพฤติกรรมผิดปกติ → Audit Focus!
```

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

- ✅ สร้างโมเดล Naive Bayes ด้วย Naive Bayes Learner/Predictor/Scorer
- ✅ สร้างและดู Decision Tree ที่อธิบายได้ด้วย Decision Tree to Image
- ✅ ประเมินและเปรียบเทียบโมเดลด้วย Scorer

**Section 4: Classification II**

- ✅ สร้างโมเดล k-NN และ SVM ด้วย KNIME nodes
- ✅ เปรียบเทียบ 4 อัลกอริทึมในตาราง Metrics เดียวกัน
- ✅ เลือกโมเดลที่เหมาะกับงาน Audit ได้อย่างมีเหตุผล

**Section 5: Association Rule Mining**

- ✅ เข้าใจ Support, Confidence, Lift
- ✅ สร้าง Association Rules จาก market_basket.csv
- ✅ ตีความกฎและประยุกต์ใช้กับงาน Cross-sell และ Audit

**Section 6: Clustering**

- ✅ สร้าง k-Means Clustering พร้อม Elbow Method หา Optimum k
- ✅ อ่านและตีความ Hierarchical Clustering Dendrogram
- ✅ ใช้ DBSCAN ค้นหา Outlier สำหรับ Audit Investigation
- ✅ ตีความลักษณะของแต่ละ Cluster สำหรับ Customer Segmentation

> **วันที่ 3 (Day 3):** เราจะก้าวสู่ **Deep Learning** — Neural Networks, TensorFlow, CNN และ LSTM ซึ่งเป็นเทคนิคที่ทรงพลังที่สุดสำหรับข้อมูลซับซ้อนเช่น รูปภาพและ time series ครับ
