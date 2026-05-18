# Day 2 — Labs คำแนะนำการตั้งค่าทุก Node อย่างละเอียด
## Machine Learning with KNIME — กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา
**วิทยากร:** อาจารย์สามิตร โกยม | **สถาบัน:** IT Genius | **วันที่:** 19 พฤษภาคม 2569

---

## 🧪 Lab 1: เตรียมข้อมูล loan_default.csv สำหรับ ML

**วัตถุประสงค์:** นำเข้า ทำความสะอาด และแบ่งข้อมูลให้พร้อมสำหรับการสร้างโมเดล Machine Learning

**Workflow ที่จะสร้าง:**
```
CSV Reader → Rule Engine → Column Filter → Number to String → Partitioning
                                                                 ↓          ↓
                                                            Training     Test
```

---

### Node 1: CSV Reader

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog → ตั้งค่าดังนี้:

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **File/URL** | `loan_default.csv` (คลิก Browse เลือกไฟล์) | ไฟล์ข้อมูลหลักสำหรับ Day 2 |
| **Column delimiter** | `,` (comma) | ตัวคั่นข้อมูลแต่ละ column |
| **Row delimiter** | `\n` (newline) | ตัวคั่นแต่ละแถว |
| **Has column header** | ✅ ติ๊ก | บรรทัดแรกเป็นชื่อ column |
| **Has row ID** | ❌ ไม่ติ๊ก | ไม่มี column Row ID ในไฟล์ |
| **Limit rows** | ❌ ไม่ติ๊ก | อ่านข้อมูลทุกแถว |

**แท็บ Transformation:** ไม่ต้องแก้ไข ใช้ค่า default

กด **OK** → คลิกขวาที่ node → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table** → ต้องเห็น **400 rows, 22 columns**

---

### Node 2: Rule Engine

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Rule Engine`

**จุดประสงค์:** แปลง column `default` จากตัวเลข (0/1) เป็นข้อความ ("No"/"Yes") เพราะ KNIME ต้องการ Target column เป็น String สำหรับ Classification

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Rule Engine

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Rule Editor:**

ในช่องด้านบน (Rule Editor) พิมพ์กฎดังนี้ (พิมพ์ทีละบรรทัด):

```
$default$ = 1 => "Yes"
TRUE => "No"
```

> **คำอธิบายกฎ:**
> - บรรทัดที่ 1: ถ้า column `default` มีค่าเท่ากับ 1 → ให้ผลลัพธ์เป็น "Yes" (ผิดนัดชำระ)
> - บรรทัดที่ 2: กรณีอื่นทั้งหมด (TRUE) → ให้ผลลัพธ์เป็น "No" (ไม่ผิดนัด)
> - กฎจะถูก evaluate จากบนลงล่าง — แถวแรกที่ match จะหยุดทันที

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Append Column** | ✅ เลือก | เพิ่ม column ใหม่ (ไม่ทับ column เดิม) |
| **New column name** | `default_label` | ชื่อ column ใหม่ที่ได้ |
| **Replace Column** | ❌ ไม่เลือก | ยังคงเก็บ column `default` ตัวเลขไว้ |

กด **OK** → **Execute**

**ตรวจสอบผล:** Column ใหม่ `default_label` ปรากฏท้ายตาราง มีค่า "Yes" หรือ "No"

---

### Node 3: Column Filter

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**จุดประสงค์:** เลือกเฉพาะ columns ที่เกี่ยวข้องกับการสร้างโมเดล ตัดคอลัมน์ที่ไม่จำเป็นออก

**เชื่อมต่อ:** Output ของ Rule Engine → Input ของ Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

ฝั่ง **Exclude** (ลากหรือคลิก `>` เพื่อย้ายออก):
- `loan_id`
- `customer_id`
- `gender`
- `marital_status`
- `education`
- `years_employed`
- `monthly_income`
- `interest_rate`
- `loan_purpose`
- `default` ← column ตัวเลข (เราใช้ `default_label` แทน)

ฝั่ง **Include** (ต้องเหลือ 13 columns นี้):

| # | Column | ประเภท | คำอธิบาย |
|---|--------|--------|----------|
| 1 | `age` | Integer | อายุผู้กู้ |
| 2 | `employment_type` | String | ประเภทการจ้างงาน |
| 3 | `annual_income` | Double | รายได้ต่อปี |
| 4 | `loan_amount` | Double | จำนวนเงินกู้ |
| 5 | `loan_term_months` | Integer | ระยะเวลากู้ (เดือน) |
| 6 | `credit_score` | Integer | คะแนนเครดิต |
| 7 | `existing_loans` | Integer | จำนวนสินเชื่อที่มีอยู่ |
| 8 | `collateral_type` | String | ประเภทหลักประกัน |
| 9 | `debt_to_income` | Double | อัตราส่วนหนี้ต่อรายได้ |
| 10 | `missed_payments_6m` | Integer | การชำระผิดนัด 6 เดือน |
| 11 | `months_with_bank` | Integer | ระยะเวลาเป็นลูกค้า (เดือน) |
| 12 | `num_bank_products` | Integer | จำนวนผลิตภัณฑ์ธนาคาร |
| 13 | `default_label` | String | **Target** — Yes/No |

กด **OK** → **Execute**

---

### Node 4: Number to String

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → Number To String`

**จุดประสงค์:** แปลง `default_label` เป็น String type อย่างเป็นทางการ เพื่อให้ Partitioning node มองเห็นใน Stratified sampling dropdown

> ⚠️ **หมายเหตุ:** ถ้า `default_label` ที่ได้จาก Rule Engine เป็น String อยู่แล้ว ให้ข้ามขั้นตอนนี้ไปได้เลย

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Number to String

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Include** | เลือกเฉพาะ `default_label` | แปลงเฉพาะ column นี้ |
| **Exclude** | columns อื่นทั้งหมด | ไม่แตะ column อื่น |

กด **OK** → **Execute**

---

### Node 5: Partitioning

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Partitioning`

**จุดประสงค์:** แบ่งข้อมูลเป็น Training Set (70%) และ Test Set (30%)

**เชื่อมต่อ:** Output ของ Number to String (หรือ Column Filter) → Input ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ First partition:**

| การตั้งค่า | ค่า | คำอธิบาย |
|-----------|-----|----------|
| **Method** | ✅ Relative[%] | แบ่งเป็นเปอร์เซ็นต์ |
| **ค่าเปอร์เซ็นต์** | `70` | Training = 70% (280 rows), Test = 30% (120 rows) |
| **Sampling** | ✅ Stratified sampling | รักษาสัดส่วน class ทั้ง 2 ฝั่ง |
| **Column** | `default_label` | แบ่งโดยรักษาสัดส่วน Yes:No ให้เท่ากัน |
| **Use random seed** | ✅ ติ๊ก | ให้ผลลัพธ์คงที่ทุกครั้ง Execute |
| **Seed value** | `42` | ค่า seed มาตรฐานในวงการ Data Science |

กด **OK** → **Execute**

**Output Ports:**
- **Port บน (▶ บน)** = Training Set (280 rows) → ต่อไปยัง Learner nodes
- **Port ล่าง (▶ ล่าง)** = Test Set (120 rows) → ต่อไปยัง Predictor nodes

---

## 🧪 Lab 2: ทำความเข้าใจ Confusion Matrix

**วัตถุประสงค์:** คำนวณและแปลผล Metrics จาก Confusion Matrix ด้วยมือ ก่อนให้ KNIME คำนวณให้

**โจทย์:** โมเดลทำนาย Loan Default (test set 80 รายการ)
- TP = 30, TN = 42, FP = 5, FN = 3

**คำนวณด้วยตัวเอง:**

| Metric | สูตร | การคำนวณ | ผลลัพธ์ |
|--------|------|----------|---------|
| Accuracy | (TP+TN) / All | (30+42) / 80 | **90.0%** |
| Precision | TP / (TP+FP) | 30 / (30+5) | **85.7%** |
| Recall | TP / (TP+FN) | 30 / (30+3) | **90.9%** |
| F1-Score | 2×(P×R)/(P+R) | 2×(85.7%×90.9%)/(85.7%+90.9%) | **88.2%** |
| Specificity | TN / (TN+FP) | 42 / (42+5) | **89.4%** |

> **คำถามอภิปราย:** สำหรับงาน Internal Audit ที่ต้องจับการผิดนัดชำระให้ได้มากที่สุด ควรเน้น **Recall (90.9%)** เพราะ FN (พลาด Default จริง) มีผลกระทบต่อธนาคารมากกว่า FP (แจ้งเตือนเกิน)

---

## 🧪 Lab 3: สร้างโมเดล Naive Bayes และ Decision Tree

**วัตถุประสงค์:** สร้างและประเมินโมเดล Classification 2 ตัวแรก

**Workflow สมบูรณ์:**
```
[Training Port ของ Partitioning]
          │
          ├──▶ Naive Bayes Learner ──▶ [Model]──▶ Naive Bayes Predictor ──▶ Scorer (NB)
          │                                               ▲
          └──▶ Decision Tree Learner ──▶ [Model]──▶ DT Predictor ──▶ Scorer (DT)
                        │                                  ▲
                        └──▶ Decision Tree to Image        │
                                                           │
[Test Port ของ Partitioning] ──────────────────────────────┘
```

---

### Node 6: Naive Bayes Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → Naive Bayes → Naive Bayes Learner`

**เชื่อมต่อ:** Training Port ของ Partitioning → Input ของ Naive Bayes Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Classification Column** | `default_label` | Target column ที่ต้องการทำนาย |
| **Default probability** | `0.0001` | ความน่าจะเป็น default เมื่อไม่เคยเห็น value นั้น ป้องกัน probability = 0 |
| **Minimum standard deviation** | `0.0001` | ป้องกัน std = 0 สำหรับ Gaussian distribution |
| **Threshold standard deviation** | `0.0` | ถ้า std ต่ำกว่านี้ใช้ Minimum std แทน |
| **Maximum unique nominal values** | `20` | จำกัดจำนวน unique values ของ categorical columns |
| **Ignore missing values** | ❌ ไม่ติ๊ก | รายงาน error ถ้าพบค่าว่าง |
| **Create PMML 4.2 compatible model** | ❌ ไม่ติ๊ก | ไม่จำเป็นสำหรับการใช้งานภายใน KNIME |

กด **OK** → **Execute**

**Output:** Model port (สี่เหลี่ยมสีน้ำเงิน) = โมเดล Naive Bayes พร้อมใช้

---

### Node 7: Naive Bayes Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → Naive Bayes → Naive Bayes Predictor`

**เชื่อมต่อ:**
- **Input Port 1** (สี่เหลี่ยม) ← Model output ของ Naive Bayes Learner
- **Input Port 2** (สามเหลี่ยม) ← Test Port ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Prediction column** | `Prediction (default_label)` (ค่า default) | ชื่อ column ที่เก็บผลทำนาย |
| **Append posterior probabilities** | ✅ ติ๊ก | เพิ่ม column ความน่าจะเป็นของแต่ละ class (ใช้สำหรับ ROC Curve) |
| **Normalize probabilities** | ✅ ติ๊ก | ให้ผลรวม probability = 1.0 |
| **Use laplace correction** | ✅ ติ๊ก | ป้องกัน probability = 0 เมื่อพบ value ที่ไม่เคยเห็นใน training |

กด **OK** → **Execute**

**ตรวจสอบผล:** Output table มี column ใหม่ `Prediction (default_label)` และ `P(Yes)`, `P(No)`

---

### Node 8: Scorer (สำหรับ Naive Bayes)

**ค้นหาใน Node Repository:** `Analytics → Statistics → Scorer`

**เชื่อมต่อ:** Output ของ Naive Bayes Predictor → Input ของ Scorer

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **First column** | `default_label` | ค่าจริง (Actual) |
| **Second column** | `Prediction (default_label)` | ค่าที่โมเดลทำนาย |
| **Sorting strategy** | `Lexical` | เรียง class ตามตัวอักษร |
| **Use Last Column as Class** | ❌ ไม่ติ๊ก | ไม่จำเป็น |

กด **OK** → **Execute**

**ดูผล:** คลิกขวาที่ node → **Accuracy Statistics** → เห็น Confusion Matrix, Accuracy, Recall, Precision, F1

---

### Node 9: Decision Tree Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → Decision Tree → Decision Tree Learner`

**เชื่อมต่อ:** Training Port ของ Partitioning → Input ของ Decision Tree Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `default_label` | Target column ที่ต้องการทำนาย |
| **Quality measure** | `Gini index` | วิธีเลือกจุดแตกกิ่ง — Gini เหมาะกับ Binary Classification |
| **Pruning method** | `MDL` | ตัดกิ่งอัตโนมัติเพื่อลด Overfitting |
| **Reduced Error Pruning** | ❌ ไม่ติ๊ก | ใช้ MDL แทน |
| **Min number records** | `5` | แต่ละ leaf ต้องมีอย่างน้อย 5 rows — ป้องกัน Overfit |
| **Number of levels (Max Depth)** | `5` | ความลึกสูงสุดของต้นไม้ — ค่า 5 ให้ความสมดุลดี |
| **No true child strategy** | `Return null` | ถ้าไม่มี data ใน branch ให้คืน null |
| **Binary nominal splits** | ❌ ไม่ติ๊ก | อนุญาตให้ Categorical columns แตกได้หลายกิ่ง |
| **Skip nominal columns without domain** | ✅ ติ๊ก | ข้าม column ที่ไม่มี domain information |
| **Use local storage (streamed)** | ❌ ไม่ติ๊ก | ไม่จำเป็นสำหรับ dataset ขนาดนี้ |

กด **OK** → **Execute**

> **ทำไม Max Depth = 5?**
> ถ้าลึกเกินไป (เช่น 20) ต้นไม้จะจำ Training data ทุก row → Overfitting
> ถ้าตื้นเกิน (เช่น 2) ต้นไม้ไม่เก็บ pattern เพียงพอ → Underfitting
> ค่า 5 เหมาะกับข้อมูล 280 rows

---

### Node 10: Decision Tree to Image

**ค้นหาใน Node Repository:** `Analytics → Mining → Decision Tree → Decision Tree to Image`

**เชื่อมต่อ:** Model output ของ Decision Tree Learner → Input ของ Decision Tree to Image

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Scaling** | `100%` | ขนาดภาพต้นไม้ (ลดเหลือ 75% ถ้าต้นไม้ใหญ่เกิน) |
| **Color nodes** | ✅ ติ๊ก | ระบายสีแต่ละ node ตาม majority class |

กด **OK** → **Execute** → คลิกขวา → **View: Decision Tree View** เพื่อดูภาพต้นไม้

---

### Node 11: Decision Tree Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → Decision Tree → Decision Tree Predictor`

**เชื่อมต่อ:**
- **Input Port 1** (สี่เหลี่ยม) ← Model output ของ Decision Tree Learner
- **Input Port 2** (สามเหลี่ยม) ← Test Port ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Prediction column** | `Prediction (default_label)` | ชื่อ column ผลทำนาย |
| **Append class probabilities** | ✅ ติ๊ก | เพิ่ม probability ของแต่ละ class สำหรับ ROC Curve |
| **Class probability suffix** | `(Prediction)` | suffix ต่อท้ายชื่อ probability column |
| **Use winner takes all** | ✅ ติ๊ก | ทำนาย class ที่มี probability สูงสุด |

กด **OK** → **Execute**

---

### Node 12: Scorer (สำหรับ Decision Tree)

**ตั้งค่าเหมือน Scorer ของ Naive Bayes ทุกประการ:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **First column** | `default_label` |
| **Second column** | `Prediction (default_label)` |

กด **OK** → **Execute**

**ตารางเปรียบเทียบ (กรอกขณะทำ Lab):**

| โมเดล | Accuracy | Precision | Recall | F1 |
|-------|----------|-----------|--------|-----|
| Naive Bayes | ___% | ___% | ___% | ___% |
| Decision Tree | ___% | ___% | ___% | ___% |

> **คำถาม:** โมเดลไหนให้ค่า Recall สูงกว่า? สำหรับงาน Audit ควรเลือกโมเดลไหน?

---

## 🧪 Lab 4: สร้างโมเดล k-NN และ SVM

**วัตถุประสงค์:** สร้างโมเดล Classification อีก 2 ตัว และเปรียบเทียบทั้ง 4 อัลกอริทึม

**ข้อควรรู้ก่อน Lab:**
> ⚠️ **k-NN ต้อง Normalize ข้อมูลก่อน!** เพราะ k-NN ใช้ Euclidean Distance
> ถ้า `annual_income` มีค่า 500,000 แต่ `existing_loans` มีค่า 1-5
> Distance จะถูก dominate โดย `annual_income` อย่างไม่ยุติธรรม

**ต้องเพิ่ม Normalizer node ก่อน Partitioning:**

---

### Node 13: Normalizer (เพิ่มก่อน Partitioning สำหรับ k-NN)

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`

**สำคัญ:** ต่อ Normalizer **ก่อน** Partitioning เพื่อหลีกเลี่ยง Data Leakage

**เชื่อมต่อ:** Output ของ Column Filter → Normalizer → Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Method** | `Min-Max Normalization` | แปลงทุก column ให้อยู่ในช่วง [0, 1] |
| **New minimum** | `0.0` | ค่าต่ำสุดหลัง Normalize |
| **New maximum** | `1.0` | ค่าสูงสุดหลัง Normalize |

**ใน Columns to normalize:** ติ๊กเฉพาะ Numeric columns:
- `age`, `annual_income`, `loan_amount`, `loan_term_months`
- `credit_score`, `existing_loans`, `debt_to_income`
- `missed_payments_6m`, `months_with_bank`, `num_bank_products`

> **ไม่ต้อง Normalize:** `employment_type`, `collateral_type`, `default_label` (String columns)

กด **OK** → **Execute**

---

### Node 14: k-NN Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → k-Nearest Neighbor → k-NN Learner`

**เชื่อมต่อ:** Training Port ของ Partitioning → Input ของ k-NN Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Number of Neighbors (k)** | `5` | ดู 5 รายการที่ใกล้เคียงที่สุด — ค่าที่พบบ่อยในการเริ่มต้น |
| **Distance measure** | `Euclidean` | วัดระยะทางด้วยสูตรปีทาโกรัส — เหมาะกับข้อมูลทั่วไป |
| **Class column** | `default_label` | Target column |

> **ทำไม k = 5?**
> k เล็กเกิน (1-2): Overfit ไวต่อ noise
> k ใหญ่เกิน (50): Underfit ไม่จับ pattern
> k = 5 เป็นค่าเริ่มต้นที่ดี — ลอง k = 3, 7, 11 เพื่อเปรียบเทียบ

กด **OK** → **Execute**

---

### Node 15: k-NN Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → k-Nearest Neighbor → k-NN Predictor`

**เชื่อมต่อ:**
- **Input Port 1** ← Model output ของ k-NN Learner
- **Input Port 2** ← Test Port ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Add class probabilities** | ✅ ติ๊ก | เพิ่ม probability column สำหรับ ROC Curve |
| **Prediction column** | `Prediction (default_label)` | ชื่อ column ผลทำนาย |

กด **OK** → **Execute**

---

### Node 16: Scorer (สำหรับ k-NN)

ตั้งค่าเหมือน Scorer ก่อนหน้า:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **First column** | `default_label` |
| **Second column** | `Prediction (default_label)` |

กด **OK** → **Execute** → บันทึก Accuracy, Recall

---

### Node 17: SVM Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → SVM → SVM Learner`

**เชื่อมต่อ:** Training Port ของ Partitioning → Input ของ SVM Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `default_label` | Target column |
| **Kernel type** | `RBF (Gaussian)` | Kernel ที่ใช้บ่อยที่สุด เหมาะกับข้อมูลทั่วไป |
| **C (Regularization)** | `1.0` | ควบคุม margin — ค่าสูง: ยอมรับ error น้อย แต่ Overfit ง่าย |
| **Gamma** | `Auto` | ควบคุมขอบเขต RBF kernel |
| **Tolerance** | `0.001` | เกณฑ์การหยุด optimization |
| **Max iteration** | `1000000` | จำนวน iteration สูงสุด |
| **Cache size** | `200` MB | หน่วยความจำ cache สำหรับ kernel matrix |

> **Kernel RBF คืออะไร?**
> RBF = Radial Basis Function — สร้างเส้นแบ่ง class แบบโค้งได้
> เหมาะกับข้อมูลที่ไม่สามารถแบ่งด้วยเส้นตรงได้ (เช่น loan default ที่มีปัจจัยซับซ้อน)

กด **OK** → **Execute** (อาจใช้เวลานานกว่า node อื่น)

---

### Node 18: SVM Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → SVM → SVM Predictor`

**เชื่อมต่อ:**
- **Input Port 1** ← Model output ของ SVM Learner
- **Input Port 2** ← Test Port ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Add probabilities** | ✅ ติ๊ก (ถ้ามี) | เพิ่ม probability column |
| **Prediction column** | `Prediction (default_label)` | ชื่อ column ผลทำนาย |

กด **OK** → **Execute**

---

### Node 19: ROC Curve (เปรียบเทียบ 4 โมเดล)

**ค้นหาใน Node Repository:** `Analytics → Statistics → ROC Curve`

**เชื่อมต่อ:** ต่อจาก Scorer node หลายตัว (หรือจาก Predictor โดยตรง)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `default_label` | column ค่าจริง |
| **Positive class value** | `Yes` | class ที่เราสนใจ (Default = Yes) |
| **Columns to sort by** | `P(Yes)` | probability column จากแต่ละ Predictor |

กด **OK** → **Execute** → คลิกขวา → **View: ROC Curve** เพื่อดูกราฟเปรียบเทียบ

**ตารางเปรียบเทียบ 4 โมเดล (กรอกขณะทำ Lab):**

| โมเดล | Accuracy | Precision | Recall | F1 | AUC |
|-------|----------|-----------|--------|-----|-----|
| Naive Bayes | ___% | ___% | ___% | ___% | ___ |
| Decision Tree | ___% | ___% | ___% | ___% | ___ |
| k-NN (k=5) | ___% | ___% | ___% | ___% | ___ |
| SVM (RBF) | ___% | ___% | ___% | ___% | ___ |

> **สรุป:** โมเดลที่ให้ Recall สูงสุด = _______ → เหมาะสำหรับงาน Audit ธนาคารมากที่สุด

---

## 🧪 Lab 5: Association Rule Mining บนข้อมูลบริการธนาคาร

**วัตถุประสงค์:** ค้นหาบริการที่ลูกค้ามักใช้ร่วมกัน เพื่อวางแผน Cross-sell และ Audit

**Workflow:**
```
market_basket.csv → CSV Reader → Pivot → Association Rule Learner → Association Rule Filter → Association Rules Viewer
```

---

### Node 20: CSV Reader (market_basket.csv)

**ตั้งค่าเหมือน Node 1** แต่เปลี่ยน File เป็น `market_basket.csv`

**ตรวจสอบผล:** 778 rows, 2 columns (`basket_id`, `product_service`)

---

### Node 21: Pivot

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Pivot`

**จุดประสงค์:** แปลงข้อมูลจาก Long format (1 row ต่อ 1 item) เป็น Wide format (1 row ต่อ 1 basket)

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Pivot

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ส่วน | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Group columns** | `basket_id` | แต่ละ basket จะเป็น 1 row |
| **Pivot columns** | `product_service` | แต่ละ product จะเป็น 1 column |
| **Aggregation** | `Count` บน column ใดก็ได้ | นับว่า product นั้นอยู่ใน basket หรือไม่ |
| **Make aggregation column name** | ✅ ติ๊ก | ตั้งชื่อ column อัตโนมัติ |

กด **OK** → **Execute**

**ตรวจสอบผล:** ได้ประมาณ 250 rows (1 row ต่อ basket) — แต่ละ column คือ product

---

### Node 22: Association Rule Learner (Apriori)

**ค้นหาใน Node Repository:** `Analytics → Mining → Association Rules → Association Rule Learner`

**เชื่อมต่อ:** Output ของ Pivot → Input ของ Association Rule Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Minimum support** | `0.10` | itemset ต้องปรากฏอย่างน้อย 10% ของ baskets ทั้งหมด |
| **Minimum confidence** | `0.50` | กฎต้องมีความน่าเชื่อถืออย่างน้อย 50% |
| **Maximum number of itemsets** | `10000` | จำกัดจำนวน itemset ที่คำนวณ |
| **Output association rules** | ✅ ติ๊ก | แสดงผลเป็น Association Rules |
| **Output frequent item sets** | ❌ ไม่ติ๊ก | ไม่ต้องแสดง itemsets |

> **ทำไม Min Support = 0.10?**
> ถ้าต่ำกว่านี้ — ได้กฎมากเกินไป ส่วนใหญ่ไม่น่าสนใจ
> ถ้าสูงกว่า 0.30 — ได้กฎน้อยเกินไป อาจพลาด pattern สำคัญ
> ค่า 0.10 (10%) เหมาะสำหรับข้อมูล 250 baskets

กด **OK** → **Execute**

---

### Node 23: Association Rule Filter

**ค้นหาใน Node Repository:** `Analytics → Mining → Association Rules → Association Rule Filter`

**เชื่อมต่อ:** Output ของ Association Rule Learner → Input ของ Association Rule Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Minimum lift** | `1.1` | กรองเฉพาะกฎที่ความสัมพันธ์เกินความบังเอิญ 10% |
| **Minimum support** | `0.0` | ไม่กรองเพิ่มด้วย support (กรองไว้แล้วใน Learner) |
| **Minimum confidence** | `0.0` | ไม่กรองเพิ่มด้วย confidence |
| **Maximum number of antecedents** | `10` | จำกัดความซับซ้อนของกฎ (จำนวน items ใน "ถ้า") |

> **Lift > 1 หมายความว่าอะไร?**
> Lift = 1.1 หมายถึง A และ B เกิดร่วมกันบ่อยกว่าถ้าเกิดโดยอิสระ 10%
> Lift = 1.0 = ความบังเอิญล้วนๆ ไม่มีความสัมพันธ์จริง

กด **OK** → **Execute**

---

### Node 24: Association Rules Viewer

**ค้นหาใน Node Repository:** `Analytics → Mining → Association Rules → Association Rules to Table`

**เชื่อมต่อ:** Output ของ Association Rule Filter → Input ของ Association Rules to Table

กด **Execute** → คลิกขวา → **View: Sorted Table** เพื่อดูกฎ

**ตัวอย่างผลที่คาดหวัง:**

| กฎ | Support | Confidence | Lift |
|---|---------|------------|------|
| {Savings Account} → {Mobile Banking} | 0.42 | 0.78 | 1.31 |
| {Home Loan} → {Life Insurance} | 0.25 | 0.65 | 1.45 |
| {Credit Card} → {Travel Insurance} | 0.18 | 0.72 | 1.62 |

> **ประยุกต์ใช้กับงาน Audit:**
> ลูกค้าที่มี Home Loan แต่ **ไม่มี** Life Insurance = อาจมีความเสี่ยงด้านการตรวจสอบ → ควร Flag ไว้

---

## 🧪 Lab 6: Customer Segmentation ด้วย Clustering

**วัตถุประสงค์:** จัดกลุ่มลูกค้าด้วย k-Means และ DBSCAN เพื่อวางแผน Audit

**Dataset:** `customer_churn.csv` (350 rows, 21 columns)

**Workflow:**
```
customer_churn.csv → CSV Reader → Column Filter → Normalizer
                                                        │
                                        ┌───────────────┤
                                        ▼               ▼
                                  k-Means (k=3)       DBSCAN
                                        │               │
                                  Cluster Assigner  Cluster Assigner
                                        │               │
                                  Color Manager    Scatter Plot
                                        │
                                  Scatter Plot
                                        │
                                  GroupBy (Statistics ต่อกลุ่ม)
```

---

### Node 25: CSV Reader (customer_churn.csv)

ตั้งค่าเหมือน Node 1 แต่เปลี่ยน File เป็น `customer_churn.csv`

**ตรวจสอบผล:** 350 rows, 21 columns

---

### Node 26: Column Filter (สำหรับ Clustering)

**เก็บเฉพาะ 8 columns สำหรับ Clustering:**

| Column | คำอธิบาย |
|--------|----------|
| `credit_score` | คะแนนเครดิต |
| `tenure_months` | ระยะเวลาเป็นลูกค้า |
| `account_balance` | ยอดเงินในบัญชี |
| `num_products` | จำนวนผลิตภัณฑ์ที่ใช้ |
| `login_count_30d` | ความถี่ล็อกอิน 30 วัน |
| `transactions_3m` | จำนวน transactions 3 เดือน |
| `nps_score` | คะแนนความพึงพอใจ |
| `complaint_count` | จำนวนข้อร้องเรียน |

กด **OK** → **Execute**

---

### Node 27: Normalizer (สำหรับ Clustering)

**ตั้งค่าเหมือน Node 13** แต่ Normalize **ทุก column** ที่เลือกไว้ทั้ง 8 columns

> ⚠️ **สำคัญมาก:** Clustering ไวต่อ scale มากกว่า Classification
> `account_balance` อาจมีค่าหลักล้าน แต่ `complaint_count` มีค่า 0-10
> ถ้าไม่ Normalize — ผล Cluster จะถูก dominate โดย `account_balance` ทั้งหมด

| Method | `Min-Max Normalization` |
|--------|------------------------|
| New minimum | `0.0` |
| New maximum | `1.0` |

กด **OK** → **Execute**

---

### Node 28: k-Means

**ค้นหาใน Node Repository:** `Analytics → Mining → Clustering → k-Means`

**เชื่อมต่อ:** Output ของ Normalizer → Input ของ k-Means

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Number of clusters (k)** | `3` | จำนวนกลุ่มที่ต้องการ — เริ่มที่ 3 กลุ่ม |
| **Max number of iterations** | `100` | จำนวน iteration สูงสุดก่อนหยุด |
| **Enable static random seed** | ✅ ติ๊ก | ให้ผลเหมือนกันทุกครั้ง |
| **Static random seed** | `42` | ค่า seed |
| **Distance measure** | `Euclidean` | วัดระยะทางระหว่าง data point กับ centroid |

> **ทำไมเริ่มที่ k = 3?**
> สำหรับ Audit: ต้องการแบ่งลูกค้าเป็น Low / Medium / High Risk
> ในขั้นตอนถัดไปจะใช้ Elbow Method หาค่า k ที่เหมาะสมที่สุด

กด **OK** → **Execute**

---

### Node 29: Color Manager (ระบายสี Cluster)

**ค้นหาใน Node Repository:** `Views → Property → Color Manager`

**เชื่อมต่อ:** Output ของ k-Means → Input ของ Color Manager

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Column** | `Cluster` (column ที่ k-Means สร้าง) | ระบายสีตาม cluster |
| **Color scheme** | `Set1` หรือ `Set2` | ชุดสีที่แยกแยะกลุ่มได้ชัดเจน |

กด **OK** → **Execute**

---

### Node 30: Scatter Plot

**ค้นหาใน Node Repository:** `Views → Chart → Scatter Plot`

**เชื่อมต่อ:** Output ของ Color Manager → Input ของ Scatter Plot

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **X column** | `credit_score` | แกน X — คะแนนเครดิต |
| **Y column** | `account_balance` | แกน Y — ยอดเงิน |
| **Dot size** | `3` | ขนาด dot ในกราฟ |
| **Show color legend** | ✅ ติ๊ก | แสดง legend สีของแต่ละ cluster |

กด **OK** → **Execute** → คลิกขวา → **View: Scatter Plot** เพื่อดูกราฟ

---

### Node 31: GroupBy (วิเคราะห์แต่ละ Cluster)

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → GroupBy`

**เชื่อมต่อ:** Output ของ k-Means → Input ของ GroupBy

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Groups:**
- **Group column(s):** เพิ่ม `Cluster`

**แท็บ Manual Aggregation:**

| Column | Aggregation |
|--------|-------------|
| `credit_score` | `Mean` |
| `account_balance` | `Mean` |
| `complaint_count` | `Mean` |
| `num_products` | `Mean` |
| `login_count_30d` | `Mean` |

กด **OK** → **Execute**

**ตีความผลลัพธ์ (ตัวอย่าง):**

| Cluster | Credit Score เฉลี่ย | Balance เฉลี่ย | Complaint เฉลี่ย | ความหมาย |
|---------|---------------------|----------------|-----------------|----------|
| Cluster 0 | สูง (700+) | สูง | น้อย (0-1) | **Premium Low Risk** |
| Cluster 1 | ปานกลาง (550-700) | ปานกลาง | ปานกลาง (1-3) | **Standard Medium Risk** |
| Cluster 2 | ต่ำ (<550) | ต่ำ | สูง (4+) | **High Risk — Audit Focus!** |

---

### Node 32: DBSCAN

**ค้นหาใน Node Repository:** `Analytics → Mining → Clustering → DBSCAN`

**เชื่อมต่อ:** Output ของ Normalizer → Input ของ DBSCAN (parallel กับ k-Means)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Epsilon** | `0.3` | รัศมีของ neighborhood — ระยะสูงสุดที่ถือว่า "ใกล้กัน" |
| **Minimum points** | `5` | จำนวน data point ขั้นต่ำใน neighborhood เพื่อเป็น Core Point |
| **Distance measure** | `Euclidean` | วิธีวัดระยะทาง |

> **Epsilon = 0.3 หมายความว่าอะไร?**
> เนื่องจาก Normalize ข้อมูลเป็น [0,1] แล้ว
> Epsilon = 0.3 หมายถึง ใน 8 มิติ data point ที่อยู่ภายในรัศมี 30% ของ range ถือว่า "ใกล้กัน"
> ถ้า Epsilon สูงเกิน → ทุกอย่างอยู่ใน cluster เดียว (ไม่ detect outlier)
> ถ้า Epsilon ต่ำเกิน → ทุกอย่างกลายเป็น Outlier

> **MinPts = 5 หมายความว่าอะไร?**
> ต้องมีอย่างน้อย 5 data points ใกล้กัน จึงจะถือว่าเป็น "กลุ่ม"
> Data point ที่ไม่มีเพื่อน 5 คน = **Noise/Outlier** → สำคัญสำหรับ Audit!

กด **OK** → **Execute**

**ดูผลลัพธ์:** Output table มี column `Cluster` — data point ที่ DBSCAN ถือว่าเป็น Noise จะมีค่า `Noise`

**กรองหา Outlier:**
เพิ่ม **Row Filter** node ต่อจาก DBSCAN:
- Filter column: `Cluster`
- Operator: `equals`
- Value: `Noise`

→ **Execute** → ได้รายชื่อลูกค้าพฤติกรรมผิดปกติ = **Audit Target!**

---

## สรุป Lab ทั้งหมด Day 2

| Lab | Node หลัก | จำนวน Node | Output สำคัญ |
|-----|-----------|-----------|--------------|
| Lab 1 | CSV Reader, Rule Engine, Column Filter, Partitioning | 5 | Train/Test Data |
| Lab 2 | (คำนวณด้วยมือ) | — | Confusion Matrix |
| Lab 3 | NB Learner/Predictor/Scorer, DT Learner/Predictor/Scorer | 7 | Accuracy, Recall |
| Lab 4 | k-NN, SVM, Normalizer, ROC Curve | 8 | เปรียบเทียบ 4 โมเดล |
| Lab 5 | Pivot, Association Rule Learner, Filter, Viewer | 5 | กฎความสัมพันธ์ |
| Lab 6 | k-Means, Color Manager, Scatter Plot, GroupBy, DBSCAN | 8 | Cluster segments + Outliers |

> **คำแนะนำสำหรับการสอน:** ให้ผู้เรียนบันทึกค่า Accuracy และ Recall ของแต่ละโมเดลลงในตาราง แล้วอภิปรายร่วมกันว่าโมเดลไหนเหมาะกับงาน Internal Audit ของธนาคารกรุงศรี — คำตอบไม่มีถูกผิด ขึ้นอยู่กับการให้ความสำคัญ False Negative vs False Positive

---

*จัดทำโดย: อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.*
*สำหรับ: กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา | Day 2: 19 พฤษภาคม 2569*
