# Day 2 — Labs คำแนะนำการตั้งค่าทุก Node อย่างละเอียด
## Machine Learning with KNIME — กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา
**วิทยากร:** อาจารย์สามิตร โกยม | **สถาบัน:** IT Genius | **วันที่:** 19 พฤษภาคม 2569

---

## 🧪 Day2_Lab1_Data_Preparation

**ชื่อเต็ม:** เตรียมข้อมูล loan_default.csv สำหรับ Machine Learning

**วัตถุประสงค์:** นำเข้า ทำความสะอาด และแบ่งข้อมูลให้พร้อมสำหรับการสร้างโมเดล Machine Learning

**Workflow ที่จะสร้าง:**
```
CSV Reader → Rule Engine → Column Filter → Normalizer → Partitioning
                                                              ↓              ↓
                                                     Training (320)    Test (80)
```

> 📝 **Node ใน Workflow นี้มี 5 nodes:** CSV Reader (9:1) → Rule Engine (9:2) → Column Filter (9:3) → Normalizer (9:12) → Partitioning (9:4)

---

### Node 1: CSV Reader

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บดังนี้:
**Settings | Transformation | Advanced Settings | Limit Rows | Encoding | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Settings:**

**ส่วน Input location:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Read from** | `Local File System` (dropdown) | อ่านจากไฟล์ในเครื่อง |
| **Mode** | ● **File** ○ Files in folder | อ่านทีละ 1 ไฟล์ |
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\loan_default.csv` | คลิก **Browse...** เพื่อเลือกไฟล์ |

**ส่วน Reader options / Format:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Autodetect format** | คลิกปุ่มนี้ก่อน | KNIME จะตรวจจับ delimiter และ format อัตโนมัติ |
| **Column delimiter** | `,` (comma) | ตัวคั่นข้อมูลแต่ละ column |
| **Row delimiter** | ● **Line break** ○ Custom | ตัวคั่นแต่ละแถว |
| **Quote char** | `"` (double quote) | อักขระ quote |
| **Quote escape char** | `"` (double quote) | อักขระ escape สำหรับ quote ภายใน quote |
| **Comment char** | `#` | บรรทัดที่ขึ้นต้นด้วย # จะถูกข้ามไป |
| **Has column header** | ☑ ติ๊ก | บรรทัดแรกเป็นชื่อ column |
| **Has RowID** | ☐ ไม่ติ๊ก | ไม่มี column Row ID ในไฟล์ |
| **Support short data rows** | ☐ ไม่ติ๊ก | ไม่จำเป็น |
| **Prepend file index to RowID** | ☐ ไม่ติ๊ก | ไม่จำเป็น |

**ส่วน Preview:**
> 💡 **KNIME 5.3:** ด้านล่างจะแสดง Preview ข้อมูลอัตโนมัติ พร้อมข้อความ *"The suggested column types are based on the first 10000 rows only. See 'Advanced Settings' tab."* ตรวจสอบว่าเห็น column ครบ 22 columns ก่อนกด OK

**แท็บ Transformation:** ตรวจสอบ Data Type ของแต่ละ column — ไม่ต้องแก้ไข ใช้ค่า default

กด **OK** → คลิกขวาที่ node → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table** → ต้องเห็น **400 rows, 22 columns**

---

### Node 2: Rule Engine

**ค้นหาใน Node Repository:** `Manipulation → Column → Calculate → Rule Engine`

**จุดประสงค์:** แปลง column `default` จากตัวเลข (0/1) เป็นข้อความ ("No"/"Yes") เพราะ KNIME ต้องการ Target column เป็น String สำหรับ Classification

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Rule Engine

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Rule Editor | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Rule Editor:**

Dialog แบ่งเป็น 3 ส่วนหลัก:

**ส่วนซ้าย — Column List และ Flow Variable List:**
- **Column List:** แสดง columns ทั้งหมดในตาราง เช่น `ROWCOUNT`, `S loan_id`, `S customer_id`, `I age`, `S gender`, `S marital_status`, `S education`, `S employment_type` เป็นต้น
  - ตัวอักษรหน้าชื่อ: `S` = String, `I` = Integer, `D` = Double
  - ดับเบิ้ลคลิกชื่อ column เพื่อแทรกลงใน Expression อัตโนมัติ
- **Flow Variable List:** แสดง flow variables ที่มี เช่น `s knime.workspace`

**ส่วนกลาง — Category, Function, Description:**
- **Category:** dropdown เลือก `All` เพื่อดู operators ทั้งหมด
- **Function list:** แสดง operators ที่ใช้ได้ เช่น `? < ?`, `? <= ?`, `? = ?`, `? > ?`, `? >= ?`, `? AND ?`, `? IN ?`, `? LIKE ?`, `? MATCHES ?`, `? OR ?`, `TRUE`, `FALSE`, `MISSING ?` เป็นต้น
- **Description:** อธิบายการใช้งานของ operator ที่เลือก

**ส่วน Expression (ด้านล่าง):** พิมพ์กฎดังนี้ (พิมพ์ทีละบรรทัด):

```
$default$ = 1 => "Yes"
TRUE => "No"
```

> **คำอธิบายกฎ:**
> - บรรทัดที่ 1: ถ้า column `default` มีค่าเท่ากับ 1 → ให้ผลลัพธ์เป็น "Yes" (ผิดนัดชำระ)
> - บรรทัดที่ 2: กรณีอื่นทั้งหมด (TRUE) → ให้ผลลัพธ์เป็น "No" (ไม่ผิดนัด)
> - กฎจะถูก evaluate จากบนลงล่าง — แถวแรกที่ match จะหยุดทันที
> - KNIME จะแสดง `S` (String) ข้างหน้าเลขบรรทัดเพื่อบอกว่าผลลัพธ์เป็น String

**ด้านล่าง Expression editor ตั้งค่า Output:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| ● **Append Column:** | `default_label` (พิมพ์ชื่อ) | เพิ่ม column ใหม่ — กดปุ่ม `S` ด้านขวาเพื่อยืนยัน type เป็น String |
| ○ **Replace Column:** | `I default` (dropdown) | ไม่เลือก — ยังคงเก็บ column `default` ตัวเลขไว้ |

กด **OK** → **Execute**

**ตรวจสอบผล:** Column ใหม่ `default_label` ปรากฏท้ายตาราง มีค่า "Yes" หรือ "No"

---

### Node 3: Column Filter

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**จุดประสงค์:** เลือกเฉพาะ columns ที่เกี่ยวข้องกับการสร้างโมเดล ตัดคอลัมน์ที่ไม่จำเป็นออก

**เชื่อมต่อ:** Output ของ Rule Engine → Input ของ Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog:

**ส่วน Column filter:**

Toggle ด้านบน: **Manual** | Wildcard | Regex | Type → เลือก **Manual** (ปุ่มสีดำ = active)

ด้านล่าง Toggle มีช่อง **Search** พร้อมปุ่ม **Aa** (Case sensitive toggle) — ใช้ค้นหา column ได้รวดเร็ว

จะเห็น 2 panel พร้อมปุ่มลูกศรตรงกลาง:
- `>` ย้าย 1 รายการที่เลือกจาก Includes → Excludes
- `>>` ย้ายทั้งหมด Includes → Excludes
- `<` ย้าย 1 รายการที่เลือกจาก Excludes → Includes
- `<<` ย้ายทั้งหมด Excludes → Includes

**Panel Excludes (ซ้าย)** — ย้าย columns เหล่านี้มาไว้ที่นี่:
- `loan_id`
- `customer_id`
- `gender`
- `marital_status`
- `education`
- `years_employed`
- `monthly_income`
- `interest_rate`
- `loan_purpose`
- `default` ← column ตัวเลขเดิม (เราใช้ `default_label` แทนแล้ว)

> 💡 ด้านล่างสุดของ Excludes panel จะเห็นข้อความ *"Any unknown column"* — หมายถึง column ใหม่ที่ไม่รู้จักจะถูก Exclude อัตโนมัติ

**Panel Includes (ขวา)** — ต้องเหลือ 13 columns นี้:

| # | Column | Data Type | คำอธิบาย |
|---|--------|-----------|----------|
| 1 | `age` | Number (double) | อายุผู้กู้ |
| 2 | `employment_type` | String | ประเภทการจ้างงาน |
| 3 | `annual_income` | Number (double) | รายได้ต่อปี |
| 4 | `loan_amount` | Number (double) | จำนวนเงินกู้ |
| 5 | `loan_term_months` | Number (double) | ระยะเวลากู้ (เดือน) |
| 6 | `credit_score` | Number (double) | คะแนนเครดิต |
| 7 | `existing_loans` | Number (double) | จำนวนสินเชื่อที่มีอยู่ |
| 8 | `collateral_type` | String | ประเภทหลักประกัน |
| 9 | `debt_to_income` | Number (double) | อัตราส่วนหนี้ต่อรายได้ |
| 10 | `missed_payments_6m` | Number (double) | การชำระผิดนัด 6 เดือน |
| 11 | `months_with_bank` | Number (double) | ระยะเวลาเป็นลูกค้า (เดือน) |
| 12 | `num_bank_products` | Number (double) | จำนวนผลิตภัณฑ์ธนาคาร |
| 13 | `default_label` | String | **Target** — Yes/No |

กด **Ok and Execute** (ปุ่มสีเหลืองมุมขวาล่าง) เพื่อบันทึกและ Execute พร้อมกัน

---

### Node 4: Normalizer

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`

**จุดประสงค์:** ปรับสเกล (Scale) ของข้อมูลตัวเลขให้อยู่ในขอบเขตมาตรฐานเดียวกัน [0, 1] เพื่อป้องกัน column ที่มีตัวเลขใหญ่ (เช่น `annual_income`) มี influence เหนือกว่า column เล็ก (เช่น `existing_loans`) ใน ML algorithms

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Normalizer

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog:

Toggle ด้านบน: **Manual** | Wildcard | Regex | Type → เลือก **Manual**

ด้านล่าง Toggle มีช่อง **Search** พร้อมปุ่ม **Aa**

**Panel Excludes (ซ้าย):** ไม่มี columns ที่นี่ (*"No columns in this list"*)

**Panel Includes (ขวา):** ย้าย Numeric columns ทั้งหมดมาที่นี่:
- `age`, `annual_income`, `loan_amount`, `loan_term_months`
- `credit_score`, `existing_loans`, `debt_to_income`
- `missed_payments_6m`, `months_with_bank`, `num_bank_products`
- *(เลือกทั้งหมดแล้วกด `<<` หรือดับเบิ้ลคลิกทีละรายการ)*

> ⚠️ **ไม่ต้อง Normalize:** `employment_type`, `collateral_type`, `default_label` (String columns — ไม่สามารถ Normalize ได้)
> KNIME จะแสดง *"Any unknown column"* ท้าย Includes panel โดยอัตโนมัติ

**ส่วน Normalization method** (Toggle ด้านล่าง panel):

| ตัวเลือก | การเลือก | คำอธิบาย |
|---------|---------|----------|
| **Min-max** | ✅ เลือก (ปุ่มสีดำ) | แปลงค่าทุก column ให้อยู่ในช่วง [0, 1] |
| Z-score | ❌ ไม่เลือก | Standardize ด้วย mean=0, std=1 |
| Decimal scaling | ❌ ไม่เลือก | หารด้วย 10^k |

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Minimum** | `0` | ค่าต่ำสุดหลัง Normalize |
| **Maximum** | `1` | ค่าสูงสุดหลัง Normalize |

กด **Ok** (ปุ่มสีเหลืองมุมขวาล่าง)

> 💡 **สังเกต:** Normalizer ใช้ปุ่ม **Ok** (ไม่มี "and Execute") — ต้อง Execute แยกโดยคลิกขวา → Execute หลังกด Ok

**ตรวจสอบผล:** Numeric columns ทุกตัวจะมีค่าอยู่ในช่วง [0, 1] — String columns ไม่เปลี่ยนแปลง

---

### Node 5: Partitioning

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Partitioning`

**จุดประสงค์:** แบ่งข้อมูลเป็น Training Set (80%) และ Test Set (20%)

**เชื่อมต่อ:** Output ของ Normalizer → Input ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**First partition | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ First partition:**

**ส่วน Choose size of first partition:**

| ตัวเลือก | การเลือก | ค่า | คำอธิบาย |
|---------|---------|-----|----------|
| ○ **Absolute** | ❌ ไม่เลือก | 100 | จำนวนแถวตายตัว |
| ● **Relative[%]** | ✅ เลือก | **`80`** | Training = 80% = 320 rows |

**ส่วน Sampling method:**

| ตัวเลือก | การเลือก | คำอธิบาย |
|---------|---------|----------|
| ○ Take from top | ❌ | เอา rows แรกๆ ตามลำดับ |
| ○ Linear sampling | ❌ | สุ่มแบบ linear |
| ○ Draw randomly | ❌ | สุ่มแบบสุ่ม |
| ● **Stratified sampling** | ✅ เลือก | รักษาสัดส่วน class Yes:No ให้เท่ากันทั้ง 2 ชุด |

**Column สำหรับ Stratified:** `S default_label` (เลือกจาก dropdown)

**Use random seed:** ☑ ติ๊ก → ใส่ค่า `42`

กด **OK** → **Execute**

**Output Ports ที่ได้:**

| Port | แท็บ | จำนวน rows | คำอธิบาย |
|------|------|-----------|----------|
| **▶ Port บน** | *1: First partition (as defined in dialog)* | **320 rows**, 13 columns | Training Set (80%) — ส่งไปยัง Learner nodes |
| **▶ Port ล่าง** | *2: Second partition (remaining rows)* | **80 rows**, 13 columns | Test Set (20%) — ส่งไปยัง Predictor nodes |

> 💡 **KNIME 5.3:** คลิกที่ node แล้วดู Output ด้านล่าง — จะเห็น 2 แท็บ *"1: First partition"* และ *"2: Second partition"* พร้อมแท็บ *"Flow Variables"* สามารถสลับดูข้อมูลแต่ละ partition ได้ทันที

---

## 🧪 Day2_Lab2_Confusion_Matrix

**ชื่อเต็ม:** ทำความเข้าใจ Confusion Matrix และ Evaluation Metrics

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

## 🧪 Day2_Lab3_Classification_NB_DT

**ชื่อเต็ม:** สร้างโมเดล Naive Bayes และ Decision Tree

**วัตถุประสงค์:** สร้างและประเมินโมเดล Classification 2 ตัวแรก บน loan default dataset

**Workflow สมบูรณ์:**
```
┌─ Data Reading ──────────────┐
│ CSV Reader (9:1)            │
└──────────────┬──────────────┘
               │
┌─ Pre-processing ───────────────────────────────────────────────────────────────┐
│ Missing Value (11:14) → Rule Engine (9:2) → Column Filter (9:3)                │
│                       → Normalizer (9:12) → Partitioning (9:4)                 │
└──────────────────────────────────────┬─────────────────────────────────────────┘
                                       │
               ┌───────────────────────┴────────────────────┐
               ▼ training set                               ▼ test set
┌─ Model Training and Evaluation ──────┐                   (ส่งตรงไป Predictors)
│ SMOTE (11:13)                        │
│   ↓ balanced training set            │
│ Naive Bayes Learner (11:5)  ──[Model]──▶ Naive Bayes Predictor (11:6) ◄── test set
│ Decision Tree Learner (11:8)──[Model]──▶ Decision Tree Predictor (11:10) ◄── test set
└──────────────────────────────────────┘
               │                                │
               ▼                                ▼
┌─ Score the Model ──────────────────────────────────────────┐
│ Scorer NB (11:7) ← NB Predictor output                     │
│ Scorer DT (11:11) ← DT Predictor output                    │
└────────────────────────────────────────────────────────────┘
```

> 📝 **ลำดับ Node ใน Workflow (Node 1-13):** CSV Reader (9:1) → Missing Value (11:14) → Rule Engine (9:2) → Column Filter (9:3) → Normalizer (9:12) → Partitioning (9:4) → SMOTE (11:13) → NB Learner (11:5) / DT Learner (11:8) → NB Predictor (11:6) / DT Predictor (11:10) → Scorer NB (11:7) / Scorer DT (11:11)

---

### Node 1: CSV Reader

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บดังนี้:
**Settings | Transformation | Advanced Settings | Limit Rows | Encoding | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Settings:**

**ส่วน Input location:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Read from** | `Local File System` (dropdown) | อ่านจากไฟล์ในเครื่อง |
| **Mode** | ● **File** ○ Files in folder | อ่านทีละ 1 ไฟล์ |
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\loan_default.csv` | คลิก **Browse...** เพื่อเลือกไฟล์ |

**ส่วน Reader options / Format:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Autodetect format** | คลิกปุ่มนี้ก่อน | KNIME จะตรวจจับ delimiter และ format อัตโนมัติ |
| **Column delimiter** | `,` (comma) | ตัวคั่นข้อมูลแต่ละ column |
| **Row delimiter** | ● **Line break** ○ Custom | ตัวคั่นแต่ละแถว |
| **Quote char** | `"` (double quote) | อักขระ quote |
| **Quote escape char** | `"` (double quote) | อักขระ escape สำหรับ quote ภายใน quote |
| **Comment char** | `#` | บรรทัดที่ขึ้นต้นด้วย # จะถูกข้ามไป |
| **Has column header** | ☑ ติ๊ก | บรรทัดแรกเป็นชื่อ column |
| **Has RowID** | ☐ ไม่ติ๊ก | ไม่มี column Row ID ในไฟล์ |

**ส่วน Preview:**
> 💡 **KNIME 5.3:** ด้านล่างจะแสดง Preview ข้อมูลอัตโนมัติ ตรวจสอบว่าเห็น column ครบ **22 columns** ก่อนกด OK

กด **OK** → คลิกขวาที่ node → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table** → ต้องเห็น **400 rows, 22 columns**

---

### Node 2: Missing Value (แก้ไขคอลัมน์ว่าง)

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Missing Value`

**จุดประสงค์:** จัดการค่าว่าง (Missing Values) ใน dataset ก่อนที่จะประมวลผลต่อ — ถ้าไม่จัดการ KNIME อาจ error หรือโมเดลทำงานผิดพลาด

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Missing Value

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Default | Column Settings | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Default** (ตั้งค่า default สำหรับทุก column ตาม data type):

| Data Type | ค่าที่ตั้ง | คำอธิบาย |
|-----------|-----------|----------|
| **String** | `Fix Value` → Value: `None` | แทนค่าว่างในทุก String column ด้วยข้อความ "None" |
| **Number (integer)** | `Do nothing` | ไม่แตะค่าว่างใน Integer columns |
| **Number (double)** | `Do nothing` | ไม่แตะค่าว่างใน Double columns |

> 💡 **แท็บ Column Settings:** ใช้ตั้งค่าเฉพาะเจาะจงรายคอลัมน์ได้ — เช่น ถ้าต้องการแทนค่าว่างใน `credit_score` ด้วยค่า Mean แทน Do nothing สามารถระบุได้ที่นี่ ในภาพนี้ใช้ค่า Default tab เท่านั้น

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวาที่ node → ดู Output table — ค่าว่างใน String columns จะถูกแทนด้วย "None"

---

### Node 3: Rule Engine

**ค้นหาใน Node Repository:** `Manipulation → Column → Calculate → Rule Engine`

**จุดประสงค์:** แปลง column `default` จากตัวเลข (0/1) เป็นข้อความ ("No"/"Yes") เพราะ KNIME ต้องการ Target column เป็น String สำหรับ Classification

**เชื่อมต่อ:** Output ของ Missing Value → Input ของ Rule Engine

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Rule Editor | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Rule Editor:**

**ส่วน Expression (ด้านล่าง):** พิมพ์กฎดังนี้ (พิมพ์ทีละบรรทัด):

```
$default$ = 1 => "Yes"
TRUE => "No"
```

> **คำอธิบายกฎ:**
> - บรรทัดที่ 1: ถ้า column `default` มีค่าเท่ากับ 1 → ให้ผลลัพธ์เป็น "Yes" (ผิดนัดชำระ)
> - บรรทัดที่ 2: กรณีอื่นทั้งหมด (TRUE) → ให้ผลลัพธ์เป็น "No" (ไม่ผิดนัด)
> - กฎจะถูก evaluate จากบนลงล่าง — แถวแรกที่ match จะหยุดทันที

**ด้านล่าง Expression editor ตั้งค่า Output:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| ● **Append Column:** | `default_label` (พิมพ์ชื่อ) | เพิ่ม column ใหม่ — กดปุ่ม `S` ด้านขวาเพื่อยืนยัน type เป็น String |
| ○ **Replace Column:** | `I default` (dropdown) | ไม่เลือก — ยังคงเก็บ column `default` ตัวเลขไว้ |

กด **OK** → **Execute**

**ตรวจสอบผล:** Column ใหม่ `default_label` ปรากฏท้ายตาราง มีค่า "Yes" หรือ "No"

---

### Node 4: Column Filter

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**จุดประสงค์:** เลือกเฉพาะ columns ที่เกี่ยวข้องกับการสร้างโมเดล ตัดคอลัมน์ที่ไม่จำเป็นออก

**เชื่อมต่อ:** Output ของ Rule Engine → Input ของ Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog:

Toggle ด้านบน: **Manual** | Wildcard | Regex | Type → เลือก **Manual** (ปุ่มสีดำ = active)

**Panel Excludes (ซ้าย)** — ย้าย columns เหล่านี้มาไว้ที่นี่:
- `loan_id`, `customer_id`, `gender`, `marital_status`, `education`
- `years_employed`, `monthly_income`, `interest_rate`, `loan_purpose`
- `default` ← column ตัวเลขเดิม (เราใช้ `default_label` แทนแล้ว)

**Panel Includes (ขวา)** — ต้องเหลือ 13 columns นี้:

| # | Column | Data Type | คำอธิบาย |
|---|--------|-----------|----------|
| 1 | `age` | Number (double) | อายุผู้กู้ |
| 2 | `employment_type` | String | ประเภทการจ้างงาน |
| 3 | `annual_income` | Number (double) | รายได้ต่อปี |
| 4 | `loan_amount` | Number (double) | จำนวนเงินกู้ |
| 5 | `loan_term_months` | Number (double) | ระยะเวลากู้ (เดือน) |
| 6 | `credit_score` | Number (double) | คะแนนเครดิต |
| 7 | `existing_loans` | Number (double) | จำนวนสินเชื่อที่มีอยู่ |
| 8 | `collateral_type` | String | ประเภทหลักประกัน |
| 9 | `debt_to_income` | Number (double) | อัตราส่วนหนี้ต่อรายได้ |
| 10 | `missed_payments_6m` | Number (double) | การชำระผิดนัด 6 เดือน |
| 11 | `months_with_bank` | Number (double) | ระยะเวลาเป็นลูกค้า (เดือน) |
| 12 | `num_bank_products` | Number (double) | จำนวนผลิตภัณฑ์ธนาคาร |
| 13 | `default_label` | String | **Target** — Yes/No |

กด **Ok and Execute** (ปุ่มสีเหลืองมุมขวาล่าง) เพื่อบันทึกและ Execute พร้อมกัน

---

### Node 5: Normalizer

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`

**จุดประสงค์:** ปรับสเกล (Scale) ของข้อมูลตัวเลขให้อยู่ในขอบเขตมาตรฐานเดียวกัน [0, 1] เพื่อป้องกัน column ที่มีตัวเลขใหญ่ (เช่น `annual_income`) มี influence เหนือกว่า column เล็ก (เช่น `existing_loans`)

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Normalizer

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog:

Toggle ด้านบน: **Manual** | Wildcard | Regex | Type → เลือก **Manual**

**Panel Includes (ขวา):** ย้าย Numeric columns ทั้งหมดมาที่นี่:
- `age`, `annual_income`, `loan_amount`, `loan_term_months`
- `credit_score`, `existing_loans`, `debt_to_income`
- `missed_payments_6m`, `months_with_bank`, `num_bank_products`

> ⚠️ **ไม่ต้อง Normalize:** `employment_type`, `collateral_type`, `default_label` (String columns)

**ส่วน Normalization method** (Toggle ด้านล่าง panel):

| ตัวเลือก | การเลือก | คำอธิบาย |
|---------|---------|----------|
| **Min-max** | ✅ เลือก (ปุ่มสีดำ) | แปลงค่าทุก column ให้อยู่ในช่วง [0, 1] |
| Z-score | ❌ ไม่เลือก | Standardize ด้วย mean=0, std=1 |
| Decimal scaling | ❌ ไม่เลือก | หารด้วย 10^k |

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Minimum** | `0` |
| **Maximum** | `1` |

กด **Ok** (ปุ่มสีเหลืองมุมขวาล่าง) → คลิกขวาที่ node → **Execute**

> 💡 **สังเกต:** Normalizer ใช้ปุ่ม **Ok** (ไม่มี "and Execute") — ต้อง Execute แยก

---

### Node 6: Partitioning

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Partitioning`

**จุดประสงค์:** แบ่งข้อมูลเป็น Training Set (80%) และ Test Set (20%)

**เชื่อมต่อ:** Output ของ Normalizer → Input ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**First partition | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ First partition:**

**ส่วน Choose size of first partition:**

| ตัวเลือก | การเลือก | ค่า | คำอธิบาย |
|---------|---------|-----|----------|
| ○ **Absolute** | ❌ ไม่เลือก | 100 | จำนวนแถวตายตัว |
| ● **Relative[%]** | ✅ เลือก | **`80`** | Training = 80% = 320 rows |

**ส่วน Sampling method:**

| ตัวเลือก | การเลือก | คำอธิบาย |
|---------|---------|----------|
| ● **Stratified sampling** | ✅ เลือก | รักษาสัดส่วน class Yes:No ให้เท่ากันทั้ง 2 ชุด |

**Column สำหรับ Stratified:** `S default_label` (เลือกจาก dropdown)

**Use random seed:** ☑ ติ๊ก → ใส่ค่า `42`

กด **OK** → **Execute**

**Output Ports ที่ได้:**

| Port | จำนวน rows | คำอธิบาย |
|------|-----------|----------|
| **▶ Port บน** | **320 rows** | Training Set (80%) → ส่งไปยัง SMOTE |
| **▶ Port ล่าง** | **80 rows** | Test Set (20%) → ส่งตรงไปยัง Predictor nodes |

---

### Node 7: SMOTE

**ค้นหาใน Node Repository:** `Other Data Mining → Imbalanced Data → SMOTE`

> 💡 หรือพิมพ์ `SMOTE` ในช่อง Search ของ Node Repository ได้เลย

**จุดประสงค์:** สร้างข้อมูล synthetic ของ class "Yes" (minority class) โดยการ interpolate ระหว่าง data points ที่มีอยู่ — ทำให้ training set มีสัดส่วน Yes:No สมดุลมากขึ้น

**เชื่อมต่อ:** Training Port (port บน) ของ Partitioning → Input ของ SMOTE
*(port ล่าง = test set ส่งตรงไป Predictors โดยไม่ผ่าน SMOTE)*

> ⚠️ **หลักการสำคัญ:** SMOTE ต้องใส่ **หลัง Partitioning** และ **ก่อน Learner เท่านั้น**
> ห้ามใส่ก่อน Partitioning เด็ดขาด — เพราะ test set ต้องเป็นข้อมูลจริง 100% ไม่มีข้อมูล synthetic

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Settings | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `S default_label` (dropdown) | column ที่ใช้ระบุ minority class — SMOTE จะ oversample class "Yes" |
| **# Nearest neighbor** | `5` | จำนวน neighbors ที่ใช้ interpolate เพื่อสร้าง synthetic data point ใหม่ |
| ● **Oversample minority classes** | ✅ เลือก | SMOTE จะสร้าง synthetic data อัตโนมัติจนกว่า minority class มีสัดส่วนเท่ากับ majority class |
| **Enable static seed** | ☐ ไม่ติ๊ก | ใช้ random seed แบบสุ่ม |

> **SMOTE ทำงานอย่างไร?**
> 1. หา k nearest neighbors ของแต่ละ data point ใน minority class ("Yes")
> 2. สุ่มเลือก neighbor 1 ตัว
> 3. สร้าง data point ใหม่บนเส้นตรงระหว่าง original point กับ neighbor
> 4. ทำซ้ำจนกว่า minority class มีจำนวนเท่ากับ majority class
>
> ผลลัพธ์: Training set ที่ได้จะมี Yes:No ≈ 1:1 (Balanced)

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวาที่ SMOTE node → ดู output table — จำนวน "Yes" rows ควรเพิ่มขึ้นใกล้เคียงกับ "No" rows

---

### Node 8: Naive Bayes Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → Naive Bayes → Naive Bayes Learner`

**เชื่อมต่อ:** Output ของ SMOTE (balanced training set) → Input ของ Naive Bayes Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Classification Column** | `S default_label` (dropdown) | Target column ที่ต้องการทำนาย |
| **Default probability** | `0.0001` | ค่าความน่าจะเป็น default — ป้องกัน probability = 0 เมื่อไม่เคยเห็น value นั้นใน training |
| **Minimum standard deviation** | `0.0001` | ป้องกัน std = 0 สำหรับ Gaussian distribution |
| **Threshold standard deviation** | `0.0` | ถ้า std ต่ำกว่าค่านี้จะใช้ Minimum std แทน |
| **Maximum number of unique nominal values per attribute** | `20` | จำกัด unique values ของ categorical columns |
| **Ignore missing values** | ☐ ไม่ติ๊ก | รายงาน error ถ้าพบค่าว่าง |
| **Create PMML 4.2 compatible model** | ☐ ไม่ติ๊ก | ไม่จำเป็นสำหรับการใช้งานภายใน KNIME |

กด **OK** → **Execute**

**Output:** Model port (สี่เหลี่ยมสีน้ำเงิน) = โมเดล Naive Bayes พร้อมใช้

---

### Node 9: Decision Tree Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → Decision Tree → Decision Tree Learner`

**เชื่อมต่อ:** Output ของ SMOTE (balanced training set) → Input ของ Decision Tree Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | PMMLSettings | Flow Variables | Job Manager Selection**

**แท็บ Options:**

**ส่วน General:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `S default_label` (dropdown) | Target column ที่ต้องการทำนาย |
| **Quality measure** | `Gini index` (dropdown) | วิธีเลือกจุดแตกกิ่ง — Gini เหมาะกับ Binary Classification |
| **Pruning method** | `MDL` (dropdown) | ตัดกิ่งอัตโนมัติเพื่อลด Overfitting — MDL = Minimum Description Length |
| **Reduced Error Pruning** | ☐ ไม่ติ๊ก | ใช้ MDL แทน |
| **Min number records per node** | `5` | แต่ละ leaf node ต้องมีอย่างน้อย 5 rows — ป้องกัน Overfit |
| **Number records to store for view** | `10,000` | จำนวน record ที่เก็บไว้แสดงผล Decision Tree View |
| **Average split point** | ☑ ติ๊ก | ใช้ค่าเฉลี่ยของ split point แทนค่าขอบเขต |
| **Number threads** | `12` | จำนวน CPU threads ที่ใช้ในการ train |
| **Skip nominal columns without domain information** | ☑ ติ๊ก | ข้าม categorical columns ที่ไม่มี domain info |

**ส่วน Root split:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Force root split column** | ☐ ไม่ติ๊ก | ให้อัลกอริทึมเลือก root split เอง |
| **Root split column** | `D num_bank_products` (greyed out) | ค่า default — ใช้งานได้เมื่อ Force root split = ติ๊ก |

**ส่วน Binary nominal splits:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Binary nominal splits** | ☐ ไม่ติ๊ก | อนุญาตให้ categorical columns แตกได้หลายกิ่ง (ไม่จำกัด 2 กิ่ง) |
| **Max #nominal** | `10` | จำกัด cardinality ของ nominal attributes |
| **Filter invalid attribute values in child nodes** | ☐ (greyed out) | ใช้งานเมื่อ Binary nominal splits = ติ๊ก |

กด **OK** → **Execute**

---

### Node 10: Naive Bayes Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → Naive Bayes → Naive Bayes Predictor`

**เชื่อมต่อ:**
- **Input Port 1** (สี่เหลี่ยมสีน้ำเงิน) ← Model output ของ Naive Bayes Learner
- **Input Port 2** (สามเหลี่ยม) ← Test Port (port ล่าง) ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Change prediction column name** | ☑ ติ๊ก | เปิดใช้งานการตั้งชื่อ column ผลทำนาย |
| *(ช่องชื่อ column)* | `Prediction (default_label)` | ชื่อ column ที่เก็บผลทำนาย |
| **Append columns with normalized class distribution** | ☐ ไม่ติ๊ก | ไม่ต้องการ probability columns ใน Lab นี้ |
| **Suffix for probability columns** | *(ว่าง — greyed out)* | ใช้ได้เมื่อติ๊ก Append ด้านบน |

กด **OK** → **Execute**

**ตรวจสอบผล:** Output table มี column ใหม่ `Prediction (default_label)` เพิ่มเข้ามา

---

### Node 11: Decision Tree Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → Decision Tree → Decision Tree Predictor`

**เชื่อมต่อ:**
- **Input Port 1** (สี่เหลี่ยมสีน้ำเงิน) ← Model output ของ Decision Tree Learner
- **Input Port 2** (สามเหลี่ยม) ← Test Port (port ล่าง) ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Maximum number of stored patterns for HiLite-ing** | `10,000` | จำนวน pattern ที่เก็บไว้สำหรับ HiLite feature |
| **Change prediction column name** | ☑ ติ๊ก | เปิดใช้งานการตั้งชื่อ column ผลทำนาย |
| *(ช่องชื่อ column)* | `Prediction (default_label)` | ชื่อ column ที่เก็บผลทำนาย |
| **Append columns with normalized class distribution** | ☐ ไม่ติ๊ก | ไม่ต้องการ probability columns ใน Lab นี้ |
| **Suffix for probability columns** | *(ว่าง — greyed out)* | ใช้ได้เมื่อติ๊ก Append ด้านบน |

กด **OK** → **Execute**

---

### Node 12: Scorer (สำหรับ Naive Bayes)

**ค้นหาใน Node Repository:** `Analytics → Statistics → Scorer`

**เชื่อมต่อ:** Output ของ Naive Bayes Predictor → Input ของ Scorer (11:7)

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Scorer | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Scorer:**

**ส่วน First Column:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **First Column** | `S default_label` (dropdown) | ค่าจริง (Actual class) |

**ส่วน Second Column:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Second Column** | `S Prediction (default_label)` (dropdown) | ค่าที่โมเดลทำนาย |

**ส่วน Sorting of values in tables:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Sorting strategy** | `Lexical` (dropdown) | เรียง class ตามตัวอักษร (No, Yes) |
| **Reverse order** | ☐ ไม่ติ๊ก | ไม่กลับลำดับ |

**ส่วน Missing values:**

| ตัวเลือก | การเลือก |
|---------|---------|
| ● **Ignore** | ✅ เลือก — ข้าม row ที่มีค่าว่าง |
| ○ Fail | ❌ ไม่เลือก |

กด **OK** → **Execute**

**ดูผล:** คลิกขวาที่ node → จะเห็น 2 Output ports:
- **Port 1: Confusion matrix** — ตาราง Confusion Matrix แบบ raw
- **Port 2: Accuracy statistics** — ตาราง Metrics ละเอียดต่อ class

---

### Node 13: Scorer (สำหรับ Decision Tree)

**ค้นหาใน Node Repository:** `Analytics → Statistics → Scorer`

**เชื่อมต่อ:** Output ของ Decision Tree Predictor → Input ของ Scorer (11:11)

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Scorer | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Scorer:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **First Column** | `S default_label` | ค่าจริง (Actual class) |
| **Second Column** | `S Prediction (default_label)` | ค่าที่โมเดลทำนาย |
| **Sorting strategy** | `Insertion order` (dropdown) | เรียง class ตามลำดับที่พบครั้งแรกในข้อมูล |
| **Reverse order** | ☐ ไม่ติ๊ก | |
| **Use name prefix** | ☐ ไม่ติ๊ก | |
| **Missing values** | ● **Ignore** | ข้าม row ที่มีค่าว่าง |

กด **OK** → **Execute**

---

## 📊 ผลลัพธ์และการวิเคราะห์ — Day2_Lab3

### ผลลัพธ์จาก Scorer (Accuracy Statistics)

**Naive Bayes — Scorer (11:7):**

| Class | TruePositives | FalsePositives | TrueNegatives | FalseNegatives | Recall | Precision | F-measure | Accuracy |
|-------|--------------|----------------|---------------|----------------|--------|-----------|-----------|---------|
| **No** | 45 | 22 | 8 | 5 | **0.900** | 0.672 | 0.769 | — |
| **Yes** | 8 | 5 | 45 | 22 | **0.267** | 0.615 | 0.372 | — |
| **Overall** | — | — | — | — | — | — | — | **0.662** |
| Cohen's kappa | — | — | — | — | — | — | — | **0.188** |

**Decision Tree — Scorer (11:11):**

| Class | TruePositives | FalsePositives | TrueNegatives | FalseNegatives | Recall | Precision | F-measure | Accuracy |
|-------|--------------|----------------|---------------|----------------|--------|-----------|-----------|---------|
| **No** | 42 | 18 | 12 | 8 | **0.840** | 0.700 | 0.764 | — |
| **Yes** | 12 | 8 | 42 | 18 | **0.400** | 0.600 | 0.480 | — |
| **Overall** | — | — | — | — | — | — | — | **0.675** |
| Cohen's kappa | — | — | — | — | — | — | — | **0.257** |

---

### 🔍 วิเคราะห์: ทำไมโมเดลได้คะแนนต่ำ?

**สรุปผลเปรียบเทียบ:**

| โมเดล | Accuracy | Recall (Yes/Default) | Precision (Yes) | F1 (Yes) | Cohen's κ |
|-------|----------|---------------------|-----------------|----------|----------|
| Naive Bayes | 66.2% | **26.7%** 🔴 | 61.5% | 37.2% | 0.188 |
| Decision Tree | 67.5% | **40.0%** 🟡 | 60.0% | 48.0% | 0.257 |

> ⚠️ **ปัญหาหลัก: Recall ของ class "Yes" (ผิดนัด) ต่ำมาก**
> Naive Bayes จับ Default จริงได้แค่ 26.7% → พลาด Default 73.3% ที่ควรจะตรวจเจอ!
> Decision Tree ดีกว่าเล็กน้อย แต่ก็จับได้แค่ 40%

**สาเหตุ 5 ข้อที่ทำให้ผลลัพธ์ต่ำ:**

**1. Class Imbalance (ปัญหาหลัก)**
Test set มี **No = 50 rows (62.5%)** vs **Yes = 30 rows (37.5%)**
Training set จะมีสัดส่วนใกล้เคียงกัน (~200 No vs ~120 Yes)
โมเดลเรียนรู้จาก "No" มากกว่า จึงลำเอียงไป predict "No" — Recall ของ "Yes" จึงต่ำ

**2. Naive Bayes Independence Assumption ถูกละเมิด**
Naive Bayes สมมติว่า features ทุกตัวเป็นอิสระต่อกัน แต่ใน loan data:
- `credit_score` ↔ `debt_to_income` มีความสัมพันธ์กัน
- `annual_income` ↔ `existing_loans` มีความสัมพันธ์กัน
- เมื่อ assumption ผิด → probability calculation ผิดพลาด → accuracy ต่ำ

**3. Dataset ขนาดเล็ก (400 rows)**
- Training set มีเพียง 320 rows
- Decision Tree ที่ train จาก 320 rows อาจไม่เรียนรู้ pattern ที่หลากหลายเพียงพอ
- Naive Bayes ต้องการข้อมูลมากพอสำหรับแต่ละ combination ของ feature values

**4. MDL Pruning ตัดกิ่งมากเกินไป (Decision Tree)**
MDL Pruning ป้องกัน Overfitting โดยตัดกิ่งที่ "ซับซ้อนเกิน"
แต่ด้วย training data เพียง 320 rows อาจตัดกิ่งที่จับ "Yes" class ออกไปด้วย
ผลคือ tree ที่ได้ตื้นเกินไป → Underfitting สำหรับ minority class

**5. Feature Encoding ไม่สมบูรณ์**
Categorical columns เช่น `employment_type`, `collateral_type` มีการ encode เป็น String
Decision Tree จัดการได้ดี แต่ Naive Bayes อาจไม่ถ่วงน้ำหนักความสำคัญของแต่ละ category ได้ดีนัก

**แนวทางแก้ไข (สำหรับอาจารย์เพิ่มเติมในการสอน):**

| แนวทาง | วิธี | เครื่องมือ KNIME |
|--------|------|----------------|
| แก้ Class Imbalance | SMOTE oversampling หรือ Class Weight | SMOTE node |
| เพิ่ม data | เพิ่มจาก 400 เป็น 2,000+ rows | — |
| ปรับ DT Pruning | ลด MDL severity หรือใช้ Reduced Error Pruning | Decision Tree Learner dialog |
| ลอง Random Forest | Ensemble 100 Decision Trees → Recall ดีขึ้น | Random Forest Learner |
| Threshold Tuning | ลดขีดทำนาย Yes จาก 50% → 30% เพื่อเพิ่ม Recall | ROC Curve + Threshold |

> 💡 **บทเรียนสำคัญสำหรับ Internal Audit:**
> Accuracy 66-67% **ดูสูง** แต่ Recall ของ "Yes" เพียง 27-40% **ต่ำมากสำหรับงาน Fraud Detection**
> ธนาคารควรใช้ **Recall เป็น KPI หลัก** ไม่ใช่ Accuracy
> เพราะ FN (พลาด Default จริง) มีต้นทุนสูงกว่า FP (แจ้งเตือนเกิน) มาก

---

## 🛠️ Day2_Lab3_Bonus: แก้ Class Imbalance ด้วย SMOTE

**จุดประสงค์:** เพิ่ม Node SMOTE เข้า Workflow เพื่อแก้ปัญหา Class Imbalance และพิสูจน์ว่า Recall ของ class "Yes" ดีขึ้นจริง

### Workflow ที่อัปเดตด้วย SMOTE

```
Pre-processing:
CSV Reader → Rule Engine → Column Filter → Normalizer → Partitioning
                                                              │
                                               ┌─────────────┴──────────────┐
                                               ▼ training set               ▼ test set
                                            [SMOTE]                    (ส่งตรงไป Predictors)
                                               │
                                    ┌──────────┴──────────┐
                                    ▼                     ▼
                          Naive Bayes Learner    Decision Tree Learner
                                    │                     │
                          [NB Model]             [DT Model]
                                    │                     │
                          Naive Bayes Predictor  Decision Tree Predictor ◄── test set
                                    │                     │
                              Scorer (NB)          Scorer (DT)
```

> ⚠️ **หลักการสำคัญ:** SMOTE ต้องใส่ **หลัง Partitioning** และ **ก่อน Learner เท่านั้น**
> ห้ามใส่ก่อน Partitioning เด็ดขาด — เพราะ test set ต้องเป็นข้อมูลจริง 100% ไม่มีข้อมูล synthetic

---

### Node SMOTE (11:13)

**ค้นหาใน Node Repository:** `Other Data Mining → Imbalanced Data → SMOTE`

> 💡 หรือพิมพ์ `SMOTE` ในช่อง Search ของ Node Repository ได้เลย

**จุดประสงค์:** สร้างข้อมูล synthetic ของ class "Yes" (minority class) โดยการ interpolate ระหว่าง data points ที่มีอยู่ — ทำให้ training set มีสัดส่วน Yes:No สมดุลมากขึ้น

**เชื่อมต่อ:** Training Port (port บน) ของ Partitioning → Input ของ SMOTE
*(port ล่าง = test set ส่งตรงไป Predictors โดยไม่ผ่าน SMOTE)*

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Settings | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `S default_label` (dropdown) | column ที่ใช้ระบุ minority class — SMOTE จะ oversample class "Yes" |
| **# Nearest neighbor** | `5` | จำนวน neighbors ที่ใช้ interpolate เพื่อสร้าง synthetic data point ใหม่ |
| ○ **Oversample by:** | `2` (ไม่เลือก) | สร้าง synthetic data เป็น N เท่าของ minority class (manual) |
| ● **Oversample minority classes** | ✅ เลือก | SMOTE จะสร้าง synthetic data อัตโนมัติจนกว่า minority class จะมีสัดส่วนเท่ากับ majority class |
| **Enable static seed** | ☐ ไม่ติ๊ก | ใช้ random seed แบบสุ่ม (ผลลัพธ์อาจต่างกันในแต่ละ Execute) |
| **Draw new seed** | *(ปุ่ม — ใช้เมื่อ Enable static seed = ติ๊ก)* | สุ่ม seed ใหม่สำหรับ reproducibility |

> **SMOTE ทำงานอย่างไร?**
> 1. หา k nearest neighbors ของแต่ละ data point ใน minority class ("Yes")
> 2. สุ่มเลือก neighbor 1 ตัว
> 3. สร้าง data point ใหม่บนเส้นตรงระหว่าง original point กับ neighbor
> 4. ทำซ้ำจนกว่า minority class มีจำนวนเท่ากับ majority class
>
> ผลลัพธ์: Training set ที่ได้จะมี Yes:No ≈ 1:1 (Balanced)
> — synthetic "Yes" rows เหล่านี้ไม่ใช่ข้อมูลจริง แต่ช่วยให้โมเดลเรียนรู้ pattern ของ "Yes" class ได้ดีขึ้น

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวาที่ SMOTE node → ดู output table — จำนวน "Yes" rows ควรเพิ่มขึ้นใกล้เคียงกับ "No" rows

---

### 📊 ผลลัพธ์หลังเพิ่ม SMOTE

**Naive Bayes + SMOTE (Scorer):**

| Class | TruePositives | FalsePositives | TrueNegatives | FalseNegatives | Recall | Precision | F-measure |
|-------|--------------|----------------|---------------|----------------|--------|-----------|-----------|
| **No** | 36 | 15 | 15 | 14 | 0.720 | 0.706 | 0.713 |
| **Yes** | 15 | 14 | 36 | 15 | **0.500** | 0.517 | 0.508 |
| **Overall Accuracy** | — | — | — | — | — | — | **0.637** |
| **Cohen's kappa** | — | — | — | — | — | — | **0.221** |

**Decision Tree + SMOTE (Scorer):**

| Class | TruePositives | FalsePositives | TrueNegatives | FalseNegatives | Recall | Precision | F-measure |
|-------|--------------|----------------|---------------|----------------|--------|-----------|-----------|
| **No** | 31 | 8 | 22 | 19 | 0.620 | 0.795 | 0.697 |
| **Yes** | 22 | 19 | 31 | 8 | **0.733** | 0.537 | 0.620 |
| **Overall Accuracy** | — | — | — | — | — | — | **0.662** |
| **Cohen's kappa** | — | — | — | — | — | — | **0.329** |

---

### 🔄 เปรียบเทียบ Before vs After SMOTE

| โมเดล | ก่อน SMOTE | | | หลัง SMOTE | | | ผลที่ได้ |
|-------|-----------|--|--|-----------|--|--|---------|
| | Accuracy | Recall(Yes) | κ | Accuracy | Recall(Yes) | κ | |
| **Naive Bayes** | 66.2% | 26.7% 🔴 | 0.188 | 63.7% | **50.0%** 🟡 | 0.221 | Recall ↑ +23.3% |
| **Decision Tree** | 67.5% | 40.0% 🟡 | 0.257 | 66.2% | **73.3%** 🟢 | 0.329 | Recall ↑ +33.3% |

**สรุปผลกระทบของ SMOTE:**

**✅ สิ่งที่ดีขึ้น:**
- **Decision Tree Recall (Yes) ดีขึ้นมาก:** 40% → 73.3% — จับ Default จริงได้เพิ่มขึ้นถึง +33.3%
- **Naive Bayes Recall (Yes) ดีขึ้น:** 26.7% → 50.0% — จับ Default ได้เพิ่ม +23.3%
- **Cohen's kappa ดีขึ้นทั้งคู่:** แสดงว่าโมเดลมี agreement กับ actual class ดีขึ้น ไม่ใช่แค่โชค
- โมเดลมีความ **สมดุล** มากขึ้น — ไม่ลำเอียงไปทาง "No" เพียงอย่างเดียว

**⚠️ สิ่งที่ต้องยอมรับ (Trade-off):**
- **Overall Accuracy ลดลงเล็กน้อย:** NB (66.2% → 63.7%), DT (67.5% → 66.2%)
- เกิด False Positives สำหรับ "No" class เพิ่มขึ้น — แจ้งเตือน No Default ผิดมากขึ้นเล็กน้อย
- Precision ของ "Yes" ลดลง: NB (61.5% → 51.7%), DT (60.0% → 53.7%)

> 🏦 **สรุปสำหรับงาน Internal Audit ธนาคารกรุงศรี:**
> Decision Tree + SMOTE เป็นตัวเลือกที่ดีที่สุดในขั้นนี้
> Recall 73.3% หมายถึงจับ Loan Default จริงได้ 73 ใน 100 ราย (เดิมจับได้แค่ 40 ราย)
> แม้ Accuracy จะลดเล็กน้อย แต่การ **พลาด Default น้อยลง 33%** มีมูลค่าต่อธนาคารสูงกว่า
> **ขั้นต่อไป:** ลอง Random Forest หรือ XGBoost เพื่อเพิ่ม Recall ต่อไปโดยไม่เสีย Precision มาก

---

## 🧪 Day2_Lab4_Classification_kNN_SVM

**ชื่อเต็ม:** สร้างโมเดล k-NN และ SVM พร้อมเปรียบเทียบ 4 อัลกอริทึม

**วัตถุประสงค์:** สร้างโมเดล Classification อีก 2 ตัว (k-NN และ SVM) แล้วเปรียบเทียบทั้ง 4 อัลกอริทึม (NB / DT / k-NN / SVM) บน dataset เดียวกัน

**Workflow สมบูรณ์:**
```
┌─ Data Reading ──────────────┐
│ CSV Reader (9:1)            │
└──────────────┬──────────────┘
               │
┌─ Pre-processing ───────────────────────────────────────────────────────────────┐
│ Missing Value (11:14) → Rule Engine (9:2) → Column Filter (9:3)                │
│                       → Normalizer (9:12) → Partitioning (9:4)                 │
└──────────────────────────────────────┬─────────────────────────────────────────┘
                                       │
               ┌───────────────────────┴────────────────────┐
               ▼ training set                               ▼ test set
┌─ Model Training and Evaluation ──────────────────┐   (ส่งตรงไป Predictors)
│ SMOTE (11:13) → Naive Bayes Learner (11:5)       │
│              → Decision Tree Learner (11:8)      │
│              → K Nearest Neighbor (13:16) ◄─── test set (ใส่ทั้ง 2 ports)
│              → SVM Learner (13:15) ──[Model]──▶ SVM Predictor (13:17) ◄── test set
└──────────────────────────────────────────────────┘
               │
               ▼
┌─ Score the Model ────────────────────────────────────────────────────────────┐
│ Scorer NB (11:7) | Scorer DT (11:11) | Scorer kNN (13:18) | Scorer SVM (13:19) │
└──────────────────────────────────────────────────────────────────────────────┘
```

> 📝 **Node 1-13 ใน Lab4 นี้เหมือนกับ Day2_Lab3 ทุกประการ** — ดูรายละเอียดการตั้งค่าแต่ละ node ได้ที่ Day2_Lab3
> Lab4 **เพิ่ม** เฉพาะ Node 14 (K Nearest Neighbor) และ Node 15-18 (SVM + Scorers) เท่านั้น

> 📝 **ลำดับ Node ใน Workflow (Node 1-18):** CSV Reader → Missing Value → Rule Engine → Column Filter → Normalizer → Partitioning → SMOTE → NB Learner / DT Learner / K Nearest Neighbor / SVM Learner → NB Predictor / DT Predictor / SVM Predictor → Scorer NB / Scorer DT / Scorer kNN / Scorer SVM

---

### Node 14: K Nearest Neighbor

**ค้นหาใน Node Repository:** `Analytics → Mining → k-Nearest Neighbor → K Nearest Neighbor`

> 💡 หรือพิมพ์ `KNN` หรือ `K Nearest` ในช่อง Search ของ Node Repository

**จุดประสงค์:** อัลกอริทึม k-NN ทำนาย class ของ test data โดยดูจาก k รายการที่ใกล้เคียงที่สุดใน training data แล้ว vote เลือก class ที่พบมากที่สุด

> ⚠️ **K Nearest Neighbor ใน KNIME รับ 2 Input Ports:**
> - **Port 1** (สามเหลี่ยมบน) ← Training data จาก SMOTE (balanced)
> - **Port 2** (สามเหลี่ยมล่าง) ← Test data จาก Partitioning (port ล่าง)
>
> ต่างจาก NB/DT ตรงที่ kNN รวม Learner + Predictor ไว้ใน node เดียว — ไม่มี Predictor node แยก

**เชื่อมต่อ:**
- **Input Port 1** ← Output ของ SMOTE (balanced training set)
- **Input Port 2** ← Test Port (port ล่าง) ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Standard settings | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Standard settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Column with class labels** | `S default_label` (dropdown) | Target column ที่ใช้ระบุ class — S = String type |
| **Number of neighbours to consider (k)** | `3` | จำนวน neighbors ที่ดูเพื่อ vote — k=3 เหมาะกับ dataset ขนาดเล็ก |
| **Weight neighbours by distance** | ☐ ไม่ติ๊ก | ให้ทุก neighbor มีน้ำหนักเท่ากัน (Simple vote) |
| **Output class probabilities** | ☐ ไม่ติ๊ก | ไม่ต้องการ probability columns ใน Lab นี้ |

> **ทำไม k = 3?**
> - k เล็กเกิน (1): Overfit — ไวต่อ noise มาก
> - k ใหญ่เกิน (50): Underfit — ไม่จับ pattern
> - k = 3 เหมาะกับ dataset 320 rows (training) — ลอง k = 5, 7 เพื่อเปรียบเทียบ

กด **OK** → **Execute**

**ตรวจสอบผล:** Output table มี column `Class [kNN]` (ชื่อ output column ค่า default ของ kNN node)

---

### Node 15: SVM Learner

**ค้นหาใน Node Repository:** `Analytics → Mining → SVM → SVM Learner`

> 💡 หรือพิมพ์ `SVM` ในช่อง Search ของ Node Repository

**จุดประสงค์:** Support Vector Machine หา hyperplane ที่ดีที่สุดในการแบ่ง class "Yes" กับ "No" โดยใช้ kernel function แปลง feature space

**เชื่อมต่อ:** Output ของ SMOTE (balanced training set) → Input ของ SVM Learner

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | Flow Variables | Job Manager Selection**

**แท็บ Options:**

**ส่วนบน:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Class column** | `S default_label` (dropdown) | Target column ที่ต้องการทำนาย |
| **Overlapping penalty** | `1.0` | ค่า C (Cost parameter) — ควบคุม trade-off ระหว่าง margin กว้างกับการจัด misclassification — ค่า 1.0 เป็น default ที่สมดุล |

**ส่วน Choose your kernel and parameters:**

| Kernel | การเลือก | พารามิเตอร์ | คำอธิบาย |
|--------|---------|------------|----------|
| ○ **Polynomial** | ❌ ไม่เลือก | Bias: 2.0 / Power: 2.0 / Gamma: 2.0 | เหมาะกับข้อมูลที่มีความสัมพันธ์แบบ polynomial |
| ○ **HyperTangent** | ❌ ไม่เลือก | kappa: 0.1 / delta: 0.5 | เหมาะกับ Neural Network-style problems |
| ● **RBF** | ✅ เลือก | sigma: `0.5` | Radial Basis Function — เหมาะกับ non-linear data ทั่วไป ให้ผลดีที่สุดในกรณีส่วนใหญ่ |

> **ทำไมเลือก RBF kernel?**
> - RBF (Gaussian kernel) สามารถจับ pattern ที่ซับซ้อนและ non-linear ได้ดีที่สุด
> - ไม่ต้องรู้ล่วงหน้าว่าข้อมูลมี pattern แบบไหน — RBF ปรับตัวได้หลากหลาย
> - sigma = 0.5 เป็น default ที่ดีสำหรับ normalized data [0, 1]

กด **OK** → **Execute**

**Output:** Model port (สี่เหลี่ยมสีน้ำเงิน) = SVM Model พร้อมส่งต่อไป SVM Predictor

---

### Node 16: SVM Predictor

**ค้นหาใน Node Repository:** `Analytics → Mining → SVM → SVM Predictor`

**เชื่อมต่อ:**
- **Input Port 1** (สี่เหลี่ยมสีน้ำเงิน) ← Model output ของ SVM Learner
- **Input Port 2** (สามเหลี่ยม) ← Test Port (port ล่าง) ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Change prediction column name** | ☑ ติ๊ก | เปิดใช้งานการตั้งชื่อ column ผลทำนาย |
| *(ช่องชื่อ column)* | `Prediction (default_label)` | ชื่อ column ที่เก็บผลทำนาย |
| **Append columns with normalized class distribution** | ☐ ไม่ติ๊ก | ไม่ต้องการ probability columns ใน Lab นี้ |
| **Suffix for probability columns** | *(ว่าง — greyed out)* | ใช้ได้เมื่อติ๊ก Append ด้านบน |

กด **OK** → **Execute**

**ตรวจสอบผล:** Output table มี column ใหม่ `Prediction (default_label)` เพิ่มเข้ามา

---

### Node 17: Scorer (สำหรับ K Nearest Neighbor)

**ค้นหาใน Node Repository:** `Analytics → Statistics → Scorer`

**เชื่อมต่อ:** Output ของ K Nearest Neighbor node → Input ของ Scorer (13:18)

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Scorer | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Scorer:**

**ส่วน First Column:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **First Column** | `S default_label` (dropdown) | ค่าจริง (Actual class) |

**ส่วน Second Column:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Second Column** | `S Class [kNN]` (dropdown) | ค่าที่ kNN node ทำนาย — ชื่อ default output column ของ K Nearest Neighbor |

**ส่วน Sorting of values in tables:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Sorting strategy** | `Insertion order` (dropdown) | เรียง class ตามลำดับที่พบครั้งแรกในข้อมูล |
| **Reverse order** | ☐ ไม่ติ๊ก | ไม่กลับลำดับ |

**ส่วน Provide scores as flow variables:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Use name prefix** | ☐ ไม่ติ๊ก |

**ส่วน Missing values:**

| ตัวเลือก | การเลือก |
|---------|---------|
| ● **Ignore** | ✅ เลือก — ข้าม row ที่มีค่าว่าง |
| ○ Fail | ❌ ไม่เลือก |

กด **OK** → **Execute**

---

### Node 18: Scorer (สำหรับ SVM)

**ค้นหาใน Node Repository:** `Analytics → Statistics → Scorer`

**เชื่อมต่อ:** Output ของ SVM Predictor → Input ของ Scorer (13:19)

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Scorer | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Scorer:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **First Column** | `S default_label` (dropdown) | ค่าจริง (Actual class) |
| **Second Column** | `S Prediction (default_label)` (dropdown) | ค่าที่ SVM ทำนาย |
| **Sorting strategy** | `Insertion order` (dropdown) | เรียง class ตามลำดับที่พบครั้งแรกในข้อมูล |
| **Reverse order** | ☐ ไม่ติ๊ก | |
| **Use name prefix** | ☐ ไม่ติ๊ก | |
| **Missing values** | ● **Ignore** | ข้าม row ที่มีค่าว่าง |

กด **OK** → **Execute**

---

## 📊 ผลลัพธ์และการวิเคราะห์ — Day2_Lab4

### เปรียบเทียบ 4 อัลกอริทึม

| อัลกอริทึม | Accuracy | Recall (Yes) | κ (Kappa) | จุดเด่น |
|-----------|---------|-------------|-----------|---------|
| **Naive Bayes** | 0.650 | 0.72 | 0.253 | เร็ว ง่าย interpretable |
| **Decision Tree** | 0.688 | 0.72 | 0.346 | เห็นได้ชัดว่าตัดสินใจอย่างไร |
| **K Nearest Neighbor** | 0.637 | 0.64 | 0.261 | ไม่ต้อง train — lazy learner |
| **SVM** | 0.625 | 1 | 0 | ทรงพลังกับ non-linear data |

> 💡 **บทเรียนสำคัญ:** สำหรับ Internal Audit ที่ต้องการจับ Default ให้ได้มากที่สุด
> ให้ดูที่ **Recall ของ class "Yes"** เป็นหลัก — ไม่ใช่ Accuracy โดยรวม

---


## 🧪 Day2_Lab5_Association_Rules
 
**ชื่อเต็ม:** Association Rule Mining บนข้อมูลบริการธนาคาร
**วัตถุประสงค์:** ค้นหาบริการที่ลูกค้ามักใช้ร่วมกัน เพื่อวางแผน Cross-sell และงาน Internal Audit
**Dataset:** `market_basket.csv` — **778 rows, 8 columns**
**เวอร์ชัน:** KNIME Analytics Platform 5.3.x
 
| คอลัมน์ | คำอธิบาย |
|---------|----------|
| `transaction_id` | รหัสรายการ (1 row = 1 รายการสินค้า/บริการ) |
| `customer_id` | รหัสลูกค้า |
| `basket_id` | รหัสตะกร้า — **ใช้เป็น Group column** |
| `transaction_date` | วันที่ทำรายการ |
| `product_service` | ชื่อบริการ — **ใช้เป็น Item column** |
| `category` | กลุ่มบริการ (Deposit, Loan, Insurance ฯลฯ) |
| `amount` | มูลค่ารายการ |
| `branch` | สาขา |
 
> 📌 **ข้อมูลพื้นฐานที่ควรทราบ:** ไฟล์มี **250 basket** (เฉลี่ย ~3.1 บริการ/basket), บริการที่แตกต่างกัน **27 รายการ**, แบ่งเป็น **9 category** — ตัวเลขเหล่านี้สำคัญต่อการตั้งค่า Minimum support
 
**Workflow สมบูรณ์:**
 
```
┌─ Data Reading ──────────────────────────────┐
│ CSV Reader (market_basket.csv)              │
└───────────────────┬─────────────────────────┘
                    │
┌─ Pre-processing ──────────────────────────────────────────┐
│ GroupBy (รวม product_service เป็น Collection ต่อ 1 basket)  │
└───────────────────┬───────────────────────────────────────┘
                    │
┌─ Mining & Output ─────────────────────────────────────────┐
│ Association Rule Learner                                   │
│        → Row Filter   (กรองด้วย Lift)                       │
│        → Sorter       (เรียงผลลัพธ์ตาม Lift / Confidence)    │
└────────────────────────────────────────────────────────────┘
```
 
> 📝 **ลำดับ Node ใน Workflow (Node 1–5):**
> CSV Reader → GroupBy → Association Rule Learner → Row Filter → Sorter
 
> ⚠️ **หมายเหตุสำคัญ:** KNIME Association Rule Learner **ต้องการข้อมูลในรูป Collection column** (1 คอลัมน์ที่แต่ละ cell เก็บรายการ item ของ 1 transaction) จึง **ต้องใช้ GroupBy ไม่ใช่ Pivot** — Pivot สร้างตาราง Wide ที่มีหลายคอลัมน์ ซึ่งโหนด Learner รับไม่ได้
 
---
 
### Node 1: CSV Reader (market_basket.csv)
 
**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`
 
**ตั้งค่าเหมือน Lab3 Node 1** แต่เปลี่ยน File path เป็น:
 
| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\market_basket.csv` |
 
คลิก **Autodetect format** → กด **OK** → **Execute**
 
**ตรวจสอบผล:** ต้องเห็น **778 rows, 8 columns** — ตรวจให้แน่ใจว่ามีคอลัมน์ `basket_id` และ `product_service` (สองคอลัมน์นี้คือคอลัมน์หลักที่จะใช้ในแล็บนี้)
 
---
 
### Node 2: GroupBy
 
**ค้นหาใน Node Repository:** `Manipulation → Aggregation → GroupBy` (หรือพิมพ์ค้นหา `GroupBy`)
 
**จุดประสงค์:** รวมข้อมูลจาก Long format (1 row ต่อ 1 item) ให้กลายเป็น **1 row ต่อ 1 basket** โดยรวบรายการ `product_service` ทั้งหมดของแต่ละ basket ไว้ใน **Collection column** เดียว — รูปแบบนี้คือสิ่งที่ Association Rule Learner ต้องการ
 
**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ GroupBy
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog
 
**แท็บ Groups:**
 
| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Group column(s)** | `basket_id` | แต่ละ basket_id จะกลายเป็น 1 row ใน output |
 
**แท็บ Manual Aggregation:**
 
| Column | Aggregation Method | คำอธิบาย |
|--------|-------------------|----------|
| `product_service` | `Set` | รวบรายการบริการของ basket นั้นเป็น Collection (เก็บค่าไม่ซ้ำ) |
 
> 💡 ใช้ `Set` เพื่อตัดค่าซ้ำภายใน basket เดียวกัน (เหมาะกับ Market Basket) — หากต้องการเก็บค่าซ้ำด้วยให้เลือก `List` แทน
>
> 💡 ผลลัพธ์จะได้คอลัมน์ใหม่ชื่อ `Set(product_service)` ซึ่งเป็นชนิด Collection — สังเกตได้จากไอคอนหัวคอลัมน์เป็นรูปวงเล็บปีกกา `{}`
 
กด **OK** → **Execute**
 
**ตรวจสอบผล:** Output ต้องได้ **250 rows พอดี** (1 row ต่อ 1 basket) ประกอบด้วยคอลัมน์ `basket_id` และคอลัมน์ Collection `Set(product_service)`
 
---
 
### Node 3: Association Rule Learner
 
**ค้นหาใน Node Repository:** `Analytics → Mining → Item Sets / Association Rules → Association Rule Learner`
 
> ℹ️ โหนดนี้เป็นส่วนหนึ่งของ **KNIME Base** — มีติดตั้งมาแล้วในตัว ไม่ต้องติดตั้ง Extension เพิ่ม
 
**เชื่อมต่อ:** Output ของ GroupBy → Input ของ Association Rule Learner
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Options | Flow Variables | Job Manager Selection | Memory Policy**
 
**แท็บ Options** แบ่งเป็น 3 ส่วน: **Itemset Mining / Output / Association Rules**
 
| ส่วน | ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|------|-----------|----------|
| **Itemset Mining** | Column containing transactions | `Set(product_service)` | เลือกคอลัมน์ Collection ที่ได้จาก GroupBy |
| **Itemset Mining** | Minimum support (0-1) | `0.04` | itemset ต้องปรากฏอย่างน้อย ~4% ของ baskets ทั้งหมด |
| **Itemset Mining** | Underlying data structure | `ARRAY` | ค่า default — เหมาะกับเคสนี้ (transaction เยอะ 250, item น้อย 27) |
| **Output** | Itemset type | `FREE` | FREE = itemset ทุกตัว → สร้างกฎได้ครบที่สุด |
| **Output** | Maximal itemset length | `3` | จำกัด item รวมในกฎไม่เกิน 3 (antecedent ไม่เกิน 2) |
| **Association Rules** | Output association rules | ☑ **ต้องติ๊ก** | ถ้าไม่ติ๊กจะได้แค่ frequent itemsets — ไม่ได้กฎ |
| **Association Rules** | Minimum confidence | `0.50` | กฎต้องมีความน่าเชื่อถืออย่างน้อย 50% |
 
> ⚠️ **ลำดับการตั้งค่าที่ต้องระวัง:**
> - ช่อง **Minimum support (0-1)** เป็นสัดส่วน 0–1 → กรอก `0.04` (ไม่ใช่ `4`) — ค่า default คือ `0.5` ซึ่งสูงเกินไป **ต้องแก้เสมอ**
> - ช่อง **Minimum confidence** จะเป็นสีเทา (แก้ไม่ได้) จนกว่าจะ **ติ๊ก checkbox "Output association rules"** ก่อน — ติ๊กแล้วจึงกรอก `0.50`
> - **Itemset type** มี 3 ตัวเลือก: `FREE` (ทุก itemset) / `CLOSED` (แบบกระชับ ตัด superset ที่ support เท่ากัน) / `MAXIMAL` (เฉพาะ itemset ใหญ่สุด) — สำหรับสร้างกฎให้ครบ เลือก **`FREE`**
> - โหนดนี้สร้างกฎที่ **consequent มี 1 item เสมอ**
 
> **ทำไม Min Support = 0.04 (ไม่ใช่ 0.10 หรือ 0.5)?**
> ชุดข้อมูลนี้กระจายตัวสูง — บริการที่พบบ่อยที่สุด (Current Account) มี support เพียง ~0.17 และคู่บริการที่พบบ่อยที่สุดมี support เพียง ~0.10 จากการทดสอบจริง:
> - **support ≥ 0.50** (ค่า default) → ได้ **0 itemset** (ไม่มี item เดี่ยวใดผ่านเกณฑ์ด้วยซ้ำ)
> - **support ≥ 0.30** → ได้ **0 กฎ**
> - **support ≥ 0.10** → ได้เพียง 3 frequent pair (~6 กฎ) — น้อยเกินกว่าจะนำไปวิเคราะห์
> - **support ≥ 0.04** → ได้ ~14 กฎ (เมื่อ confidence ≥ 0.50) — เหมาะสมต่อการสอนและการวิเคราะห์
>
> สรุป: สำหรับ dataset ที่ item กระจายตัว ต้องเลือก support **ต่ำ** — ค่าที่แนะนำคือ **0.03–0.05** อาจารย์สามารถปรับค่าแล้วสังเกตจำนวนกฎที่ได้เพื่อหาจุดที่เหมาะสม
 
> **ทำไม Min Confidence = 0.50?**
> Confidence 0.50 หมายถึง "เมื่อ antecedent เป็นจริง กฎนี้จะเป็นจริง 50% ของเวลา" — เป็นเกณฑ์ขั้นต่ำที่ยังถือว่าน่าเชื่อถือสำหรับการนำไปใช้งานจริง
 
กด **OK** → **Execute**
 
> 📌 โหนดนี้ให้ผลลัพธ์เป็น **ตารางพร้อมใช้งานทันที** มีคอลัมน์ Support, Confidence, Lift, รายการ Antecedent และ Consequent — **ไม่จำเป็นต้องมีโหนด "แปลงเป็นตาราง" เพิ่ม**
 
---
 
### Node 4: Row Filter (กรองด้วย Lift)
 
**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Filter`
 
> ℹ️ KNIME **ไม่มี** โหนดชื่อ "Association Rule Filter" — การกรองกฎตามค่า Lift ทำได้ด้วยโหนดกรองทั่วไป
 
**จุดประสงค์:** เก็บเฉพาะกฎที่มี **Lift ≥ 1.1** คือกฎที่บริการสองอย่างเกิดร่วมกันบ่อยกว่าความบังเอิญอย่างน้อย 10%
 
**เชื่อมต่อ:** Output ของ Association Rule Learner → Input ของ Row Filter
 
**ตั้งค่า (Row Filter — UI ใหม่ของ KNIME 5.3):**
 
1. กดปุ่ม **"Add criterion"** เพื่อสร้างเงื่อนไขใหม่
2. ตั้งค่าเงื่อนไข:
| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column** | `Lift` |
| **Operator** | `greater than or equal` (≥) |
| **Value** | `1.1` |
 
3. **Filter behavior** = `Output matching rows` (ค่า default — เก็บแถวที่ผ่านเงื่อนไข)
4. **Column domains** = `Retain` (ค่า default ใช้ได้)
5. กด **"Ok and Execute"**
> 💡 หากใน dropdown ไม่พบคอลัมน์ชื่อ `Lift` ให้เปิด Output table ของ Node 3 ตรวจชื่อคอลัมน์จริงก่อน แล้วเลือกให้ตรง
 
> **Lift คืออะไร?**
> Lift = P(A ∩ B) / (P(A) × P(B))
> - Lift = 1.0 → A และ B เป็นอิสระต่อกัน (ความบังเอิญล้วน)
> - Lift > 1.0 → A และ B เกิดร่วมกันบ่อยกว่าที่ควรเป็น = มีความสัมพันธ์จริง
> - Lift = 1.1 → เกิดร่วมกันบ่อยกว่าความบังเอิญ 10%
 
---
 
### Node 5: Sorter (เรียงลำดับผลลัพธ์)
 
**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Sorter`
 
> ℹ️ KNIME **ไม่มี** โหนดชื่อ "Association Rules to Table" — เนื่องจาก Node 3 ให้ผลเป็นตารางอยู่แล้ว ขั้นตอนนี้เพียงแค่ **จัดเรียง** ให้อ่านง่าย
 
**จุดประสงค์:** เรียงกฎจากน่าสนใจมากไปน้อย เพื่อให้เห็นกฎเด่นก่อน
 
**เชื่อมต่อ:** Output ของ Row Filter → Input ของ Sorter
 
**ตั้งค่า (Sorter — UI ใหม่ของ KNIME 5.3):**
 
1. **Criterion 1:** เปลี่ยน Column จากค่า default `RowID` → เลือก `Lift` → ตั้ง Order = **`Descending`**
2. กดปุ่ม **"Add sorting criterion"** เพื่อเพิ่มเกณฑ์ที่สอง
3. **Criterion 2:** Column = `Confidence` → Order = **`Descending`**
4. กด **"Ok and Execute"**
| ลำดับ | Column | Order |
|-------|--------|-------|
| Criterion 1 | `Lift` | Descending (มาก → น้อย) |
| Criterion 2 | `Confidence` | Descending (มาก → น้อย) |
 
จากนั้นคลิกขวาที่ Node 5 → เปิด **Output table** เพื่อดูกฎที่ค้นพบ
 
**ผลลัพธ์จริงจากการรัน Workflow** (Min support = 0.04, Min confidence = 0.50, Lift filter ≥ 1.1) — ได้ทั้งหมด **21 กฎ** เรียงตาม Lift จากมากไปน้อย:
 
| # | Antecedent — Items (ถ้า) | Consequent (แล้ว) | Support | Confidence | Lift |
|---|--------------------------|-------------------|---------|------------|------|
| 1 | Life Insurance, Home Loan | Fire Insurance | 0.048 | 0.923 | **19.231** |
| 2 | E-Statement, Savings Account | Mobile Banking | 0.052 | 1.000 | 10.000 |
| 3 | Fire Insurance, Life Insurance | Home Loan | 0.048 | 1.000 | 9.615 |
| 4 | SME Loan, Trade Finance | Cash Management | 0.048 | 0.923 | 8.547 |
| 5 | Savings Account, Mobile Banking | E-Statement | 0.052 | 0.929 | 8.005 |
| 6 | Internet Banking, Current Account | Payroll Service | 0.100 | 1.000 | 7.812 |
| 7 | E-Statement, Mobile Banking | Savings Account | 0.052 | 0.929 | 7.488 |
| 8 | Fire Insurance, Home Loan | Life Insurance | 0.048 | 1.000 | 6.250 |
| 9 | Payroll Service, Current Account | Internet Banking | 0.100 | 1.000 | 6.250 |
| 10 | Mutual Fund Bond, Fixed Deposit | Mutual Fund Money Market | 0.056 | 0.933 | 6.140 |
| 11 | SME Loan, Cash Management | Trade Finance | 0.048 | 0.857 | 6.122 |
| 12 | Trade Finance, Cash Management | SME Loan | 0.048 | 0.857 | 5.952 |
| 13 | Payroll Service, Internet Banking | Current Account | 0.100 | 1.000 | 5.814 |
| 14 | Mutual Fund Money Market, Mutual Fund Bond | Fixed Deposit | 0.056 | 0.875 | 5.757 |
| 15 | Mutual Fund Money Market, Fixed Deposit | Mutual Fund Bond | 0.056 | 0.875 | 5.335 |
| 16 | Mobile Banking | E-Statement | 0.056 | 0.560 | 4.828 |
| 17 | Mobile Banking | Savings Account | 0.056 | 0.560 | 4.516 |
| 18 | Auto Loan | Accident Insurance | 0.064 | 0.533 | 3.810 |
| 19 | Cash Management | Trade Finance | 0.056 | 0.519 | 3.704 |
| 20 | Cash Management | SME Loan | 0.056 | 0.519 | 3.601 |
| 21 | Home Loan | Life Insurance | 0.052 | 0.500 | 3.125 |
 
> 💡 **โครงสร้างตาราง Output:** มี 6 คอลัมน์ — `Support`, `Confidence`, `Lift`, `Consequent` (item ฝั่งผล), `implies` (ลูกศร `<--`), `Items` (ชนิด Set = รายการ antecedent ฝั่งเหตุ) อ่านกฎจากขวาไปซ้าย: `Items` ⟶ `Consequent`
>
> 💡 ค่า Confidence/Lift อาจแสดงเป็นเปอร์เซ็นต์ในบางเวอร์ชัน — ความหมายเหมือนกัน
 
---
 
## 📊 การวิเคราะห์ผลลัพธ์ (Result Analysis)
 
### 1) ภาพรวมเชิงสถิติ
 
- ได้กฎทั้งหมด **21 กฎ** ทุกกฎมี **Lift > 3.0** — สูงกว่าเกณฑ์กรอง (1.1) มาก แปลว่า **ทุกความสัมพันธ์ที่ค้นพบเป็นของจริง ไม่ใช่ความบังเอิญ**
- **Lift สูงสุด = 19.23** (Life Insurance + Home Loan ⟶ Fire Insurance) สะท้อนความสัมพันธ์ที่แน่นมาก
- มี **8 กฎที่ Confidence = 100%** — เมื่อลูกค้ามีบริการในฝั่ง "เหตุ" ครบ จะมีบริการฝั่ง "ผล" เสมอ
- **Support สูงสุด = 0.10** อยู่ในกลุ่ม Payroll Service / Internet Banking / Current Account — เป็นชุดบริการที่พบบ่อยที่สุดในฐานลูกค้า
### 2) กลุ่มความสัมพันธ์ที่ค้นพบ (Service Clusters)
 
ผลลัพธ์ 21 กฎจัดกลุ่มได้เป็น **5 คลัสเตอร์บริการ** ที่ item ภายในกลุ่มทำนายกันเองไปมา:
 
| คลัสเตอร์ | บริการในกลุ่ม | กฎเด่น | การตีความเชิงธุรกิจ |
|-----------|----------------|--------|----------------------|
| **A. ประกัน + สินเชื่อบ้าน** | Home Loan, Life Insurance, Fire Insurance | กฎ #1, #3, #8, #21 | ลูกค้าที่กู้บ้านมักทำประกันชีวิตและประกันอัคคีภัยควบคู่ (ตามเงื่อนไขสินเชื่อบ้าน) |
| **B. ธนาคารดิจิทัล** | Mobile Banking, E-Statement, Savings Account | กฎ #2, #5, #7, #16, #17 | ลูกค้าสาย Digital ใช้บริการดิจิทัลเป็นชุดเดียวกัน |
| **C. ธุรกิจ SME** | SME Loan, Trade Finance, Cash Management | กฎ #4, #11, #12, #19, #20 | ลูกค้านิติบุคคล SME ใช้บริการธุรกิจครบวงจร |
| **D. การลงทุน** | Mutual Fund Bond, Mutual Fund Money Market, Fixed Deposit | กฎ #10, #14, #15 | ลูกค้าลงทุนกระจายพอร์ตในกองทุนหลายประเภท + เงินฝากประจำ |
| **E. บัญชีเงินเดือน/นิติบุคคล** | Payroll Service, Internet Banking, Current Account | กฎ #6, #9, #13 | ลูกค้าบัญชีเงินเดือนใช้ Internet Banking + Current Account ครบชุด (Support สูงสุด) |
 
### 3) ข้อสังเกตเชิงเทคนิคสำหรับผู้เรียน
 
- **กฎที่มี antecedent 2 item ให้ Lift สูงกว่ากฎ 1 item อย่างชัดเจน** — เปรียบเทียบกฎ #21 (`Home Loan` ⟶ `Life Insurance`, Lift 3.13) กับกฎ #8 (`Fire Insurance, Home Loan` ⟶ `Life Insurance`, Lift 6.25) — เงื่อนไขที่เฉพาะเจาะจงขึ้นทำให้การทำนายแม่นยำขึ้น
- **กฎมาเป็น "วง" ภายในคลัสเตอร์** — บริการในกลุ่มเดียวกันทำนายซึ่งกันและกัน เป็นสัญญาณว่ากลุ่มนั้นเป็น "ชุดบริการ" ที่ลูกค้ามักซื้อพร้อมกัน
### 4) การประยุกต์ใช้กับงาน Internal Audit และ Cross-sell
 
| สถานการณ์ที่ตรวจพบ | กฎอ้างอิง | ข้อเสนอแนะ |
|----------------------|-----------|-------------|
| ลูกค้ามี **Home Loan + Life Insurance** แต่**ไม่มี** Fire Insurance | กฎ #1 (Lift 19.23, Conf 92%) | **Flag ตรวจสอบเร่งด่วน** — โอกาสสูงมากที่ควรมีแต่ขาด อาจเป็นช่องโหว่ความเสี่ยงหรือเอกสารตกหล่น |
| ลูกค้ามี **Internet Banking + Current Account** แต่**ไม่มี** Payroll Service | กฎ #6 (Lift 7.81, Conf 100%) | **โอกาส Cross-sell ชัดเจน** — เสนอบริการบัญชีเงินเดือน |
| ลูกค้าใช้ **Mobile Banking** แต่**ไม่มี** E-Statement | กฎ #16 (Lift 4.83) | แนะนำเปิด E-Statement — เพิ่มความสะดวกและลดต้นทุนกระดาษ |
| ลูกค้ามี **SME Loan + Trade Finance** แต่**ไม่มี** Cash Management | กฎ #4 (Lift 8.55, Conf 92%) | เสนอ Cash Management เพื่อบริการธุรกิจครบวงจร |
 
> **สรุปแนวคิด:** กฎที่มี **Lift และ Confidence สูงพร้อมกัน** คือกฎที่นำไปใช้งานได้จริงที่สุด — ใช้ตั้งสมมติฐานสำหรับงาน Audit (หาลูกค้าที่ "ควรมีแต่ขาด") และวางแผน Cross-sell (เสนอบริการที่ลูกค้ามีแนวโน้มต้องการ) ได้อย่างมีหลักฐานเชิงข้อมูลรองรับ
 
---
 
## 📋 สรุปการตั้งค่าทั้ง Workflow (Quick Reference)
 
| Node | โหนด | ค่าตั้งที่สำคัญ |
|------|------|----------------|
| 1 | CSV Reader | File path → market_basket.csv |
| 2 | GroupBy | Group: `basket_id` / Aggregation: `product_service` → `Set` |
| 3 | Association Rule Learner | Item col: `Set(product_service)` / Min support `0.04` / Itemset type `FREE` / Max length `3` / ☑ Output association rules / Min confidence `0.50` |
| 4 | Row Filter | Add criterion → `Lift` ≥ `1.1` / Output matching rows |
| 5 | Sorter | `Lift` Descending → `Confidence` Descending |

---
## 🧪 Day2_Lab6_Clustering
 
**ชื่อเต็ม:** Customer Segmentation ด้วย k-Means และ DBSCAN
**วัตถุประสงค์:** จัดกลุ่มลูกค้าด้วย Unsupervised Learning เพื่อหา pattern และ outlier สำหรับงาน Internal Audit
**Dataset:** `customer_churn.csv` — **350 rows, 21 columns**
**เวอร์ชัน:** KNIME Analytics Platform 5.3.x
 
**Workflow สมบูรณ์:**
 
```
┌─ Data Reading ────────────────────┐
│ CSV Reader (customer_churn.csv)   │
└──────────────┬────────────────────┘
               │
┌─ Pre-processing ──────────────────┐
│ Column Filter → Normalizer        │
└──────────────┬────────────────────┘
               │
    ┌──────────┴───────────────┐
    ▼                          ▼
┌─ k-Means ─────────────┐   ┌─ DBSCAN ───────────────────────┐
│ k-Means (k=3)         │   │ Numeric Distances (Euclidean)  │
│  ├─[Port 1]→ Color Mgr│   │   → DBSCAN (ε=0.7, MinPts=5)    │
│  │            → Scatter│  │   → Row Filter (กรอง Noise)     │
│  └─[Port 1]→ GroupBy  │   └────────────────────────────────┘
└───────────────────────┘
```
 
> 📝 **ลำดับ Node ใน Workflow (Node 1–10):**
> CSV Reader → Column Filter → Normalizer → k-Means → Color Manager → Scatter Plot → GroupBy → Numeric Distances → DBSCAN → Row Filter
 
> 🔴 **ข้อควรระวังที่สำคัญที่สุดของแล็บนี้ — Output Port ของ k-Means**
> โหนด k-Means มี **3 output ports**:
> - **Port 1 "Labeled input"** = ข้อมูล 350 แถว + คอลัมน์ `Cluster` ← **ต้องใช้พอร์ตนี้**
> - **Port 2 "Clusters"** = ค่า centroid เพียง 3 แถว (เป็นค่า normalized)
> - **Port 3 "PMML Cluster Model"** = โมเดลสำหรับนำไปใช้ทำนายข้อมูลใหม่
>
> **Color Manager, Scatter Plot และ GroupBy ทั้งสามโหนดต้องลากเส้นจาก Port 1 (Labeled input)** หากต่อจาก Port 2 ผิด จะเกิดอาการ: Scatter Plot แสดงแค่ 3 จุด, Color Manager ไม่มี `Cluster` ให้เลือก, GroupBy ขึ้น warning เพราะไม่พบคอลัมน์ `Cluster`
 
---
 
### Node 1: CSV Reader (customer_churn.csv)
 
**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`
 
**ตั้งค่าเหมือน Lab3 Node 1** แต่เปลี่ยน File path เป็น:
 
| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\customer_churn.csv` |
 
คลิก **Autodetect format** → กด **OK** → **Execute**
 
**ตรวจสอบผล:** ต้องเห็น **350 rows, 21 columns**
 
---
 
### Node 2: Column Filter (เลือก Feature สำหรับ Clustering)
 
**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`
 
**จุดประสงค์:** เลือกเฉพาะ 8 คอลัมน์ตัวเลขที่สะท้อนพฤติกรรมลูกค้า สำหรับนำไปจัดกลุ่ม
 
**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Column Filter
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — เลือกโหมด **Manual** (จากปุ่ม Manual | Wildcard | Regex | Type)
 
**ย้าย 8 คอลัมน์ต่อไปนี้เข้าช่อง Includes (ฝั่งขวา):**
 
| # | Column | คำอธิบาย |
|---|--------|----------|
| 1 | `credit_score` | คะแนนเครดิต |
| 2 | `tenure_months` | ระยะเวลาเป็นลูกค้า (เดือน) |
| 3 | `account_balance` | ยอดเงินในบัญชี |
| 4 | `num_products` | จำนวนผลิตภัณฑ์ที่ใช้ |
| 5 | `login_count_30d` | ความถี่ล็อกอิน 30 วันล่าสุด |
| 6 | `transactions_3m` | จำนวนธุรกรรม 3 เดือน |
| 7 | `complaint_count` | จำนวนข้อร้องเรียน |
| 8 | `nps_score` | คะแนนความพึงพอใจ (NPS) |
 
> 💡 คอลัมน์ที่เหลือ (เช่น `customer_id`, `gender`, `geography`, `churn`) ปล่อยไว้ในช่อง Excludes — ไม่นำเข้า Clustering เพราะเป็นข้อความหรือไม่เกี่ยวกับพฤติกรรมการใช้บริการ
 
กด **Ok** (ปุ่มสีเหลืองมุมขวาล่าง)
 
---
 
### Node 3: Normalizer (ปรับ Scale ก่อนจัดกลุ่ม)
 
**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`
 
**จุดประสงค์:** ปรับทุกคอลัมน์ให้อยู่ในช่วง [0, 1] — Clustering ใช้ระยะทาง (Distance) ในการจัดกลุ่ม จึงไวต่อ Scale มาก
 
**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Normalizer
 
> ⚠️ **เหตุผลที่ต้อง Normalize (ตรวจสอบจากข้อมูลจริง):**
> `account_balance` มีค่าตั้งแต่ ~6,400 ถึง ~1,985,000 ขณะที่ `complaint_count` มีค่าเพียง 0–5
> ถ้าไม่ Normalize ระยะทางจะถูก **ครอบงำโดย `account_balance`** เพียงตัวเดียว — คอลัมน์อื่นแทบไม่มีผลต่อการจัดกลุ่ม
 
**การตั้งค่า** (Dialog หน้าเดียว ไม่มีแท็บแยก):
 
- **ช่อง Includes:** ใส่ทั้ง 8 คอลัมน์ (ช่อง Excludes ควรขึ้นว่า *No columns in this list*)
- **Normalization method:** เลือก **`Min-max`** (จากปุ่ม Min-max | Z-score | Decimal scaling)
- **Minimum:** `0`
- **Maximum:** `1`
กด **Ok**
 
---
 
### Node 4: k-Means
 
**ค้นหาใน Node Repository:** `Analytics → Mining → Clustering → k-Means`
 
**เชื่อมต่อ:** Output ของ Normalizer → Input ของ k-Means
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — แท็บ **k-Means Properties**
 
| ส่วน | ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|------|-----------|----------|
| **Clusters** | Number of clusters | `3` | แบ่งลูกค้าเป็น 3 กลุ่มเป็นจุดเริ่มต้น |
| **Clusters** | Centroid initialization | `Random initialization` | สุ่มจุดเริ่มต้น centroid |
| **Clusters** | Use static random seed | ☑ ติ๊ก + ค่า `42` | ให้ผลเหมือนกันทุกครั้งที่ Execute (reproducibility) |
| **Number of Iterations** | Max. number of iterations | `100` | จำนวนรอบสูงสุดก่อนหยุด |
| **Column Selection** | Include | ทั้ง 8 คอลัมน์ | ใช้ทุก feature ที่ Normalize แล้ว |
 
> ⚠️ Dialog ของ k-Means **ไม่มีช่อง "Distance measure"** — โหนดนี้ใช้ Euclidean distance เสมอ (เป็นนิยามของ k-Means) จึงไม่ต้องตั้งค่าใด ๆ เพิ่ม
 
> **ทำไมเริ่มที่ k = 3?**
> k = 3 เป็น**จุดเริ่มต้นที่เหมาะกับงาน Audit** เพื่อแบ่งลูกค้าเป็นกลุ่มหยาบ ๆ ก่อน แล้วจึงตีความลักษณะของแต่ละกลุ่มจากค่า centroid (ทำใน Node 7)
>
> ⚠️ **สิ่งสำคัญที่ต้องเข้าใจ:** เลข cluster (cluster_0, cluster_1, cluster_2) เป็นเพียง **ป้ายชื่อที่ระบบกำหนดให้** — **ไม่มีความหมายเชิงลำดับ** cluster_0 ไม่ได้แปลว่า "ความเสี่ยงต่ำ" เสมอไป ต้องดูค่า centroid จริงจาก GroupBy (Node 7) แล้วจึงตั้งชื่อกลุ่มเอง
 
กด **OK** → **Execute**
 
**ตรวจสอบผล:** โหนดสร้าง **3 output ports** — เปิดดูได้จากแท็บด้านล่าง:
- **`1: Labeled input`** — ข้อมูล **350 rows, 9 columns** (8 feature + คอลัมน์ `Cluster` ชนิด String ค่า `cluster_0/1/2`) ← พอร์ตที่ใช้ต่อ Node 5, 7
- **`2: Clusters`** — ค่า centroid 3 แถว (RowID = cluster_0/1/2) โดยค่าเป็น **normalized [0,1]** ตามข้อมูลที่ป้อนเข้า
- **`3: PMML Cluster Model`** — โมเดลสำหรับ deploy
---
 
### Node 5: Color Manager (ระบายสีตาม Cluster)
 
**ค้นหาใน Node Repository:** `Views → Property → Color Manager`
 
**เชื่อมต่อ:** **Port 1 "Labeled input"** ของ k-Means → Input ของ Color Manager
 
> 🔴 ต้องลากจาก **Port 1 (Labeled input)** เท่านั้น — หากต่อ Port 2 (Clusters) dropdown จะไม่มีคอลัมน์ `Cluster` ให้เลือก
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — แท็บ **Color Settings**
 
| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Color by ...** | `Cluster` | เลือกคอลัมน์ที่ k-Means สร้างขึ้น |
| โหมดสี | `Nominal` | จะถูกเลือกอัตโนมัติ เพราะ `Cluster` เป็นชนิด String — แต่ละ cluster ได้สีต่างกัน |
| **Palettes** | `Set 3 (colorblind safe)` | ชุดสีที่แยกแยะได้ดี เหมาะกับการนำเสนอ |
 
> 💡 หาก dropdown "Color by..." แสดงเฉพาะคอลัมน์ตัวเลข (เช่น `credit_score`) และเลือกได้แค่โหมด `Range` แสดงว่าต่อ Port ผิด — ให้กลับไปแก้เส้นเชื่อมให้ออกจาก Port 1 ของ k-Means
 
กด **OK** → **Execute**
 
---
 
### Node 6: Scatter Plot
 
**ค้นหาใน Node Repository:** `Views → Scatter Plot`
 
**เชื่อมต่อ:** Output ของ Color Manager → Input ของ Scatter Plot
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog (แผง Data / Plot / Interactivity)
 
| ส่วน | ช่อง | ค่าที่ตั้ง |
|------|------|-----------|
| **Data** | Horizontal dimension | `num_products` |
| **Data** | Vertical dimension | `complaint_count` |
| **Data** | Color dimension | `Cluster` |
| **Plot** | Point size | `8` (ปรับได้ตามความเหมาะสม) |
 
> 💡 ช่องจริงในเวอร์ชัน 5.3 ใช้คำว่า **Horizontal/Vertical dimension** และ **Point size** (ไม่ใช่ X column / Y column / Dot size)
>
> 💡 ตั้ง **Color dimension = `Cluster`** เพื่อให้กราฟแยกสีตามกลุ่ม
 
> ⚠️ **การเลือกแกนสำคัญมาก — บทเรียนจากการรันจริง**
> หากเลือกแกนเป็น `credit_score` กับ `account_balance` กราฟจะแสดงจุด 3 สี **ปนกันทั่วทั้งกราฟ ไม่เห็นการแยกกลุ่ม** เพราะ k-Means จัดกลุ่มโดยใช้ทั้ง **8 มิติ** แต่ Scatter Plot แสดงได้เพียง 2 มิติ
>
> เมื่อดูค่า centroid จะพบว่า `credit_score` (0.49 / 0.47 / 0.57) และ `account_balance` (0.50 / 0.55 / 0.48) ของทั้ง 3 กลุ่ม **เกือบเท่ากัน** — สองมิตินี้จึง*ไม่ใช่*มิติที่แยกกลุ่ม
>
> มิติที่แยกกลุ่มได้ดีคือ **`num_products`** (0.79 / 0.60 / 0.15) และ **`complaint_count`** (0.70 / 0.17 / 0.59) — เลือกสองตัวนี้เป็นแกน จึงจะเห็น cluster_2 (ผลิตภัณฑ์น้อย) แยกตัวออกชัดเจน
 
กด **Ok and Execute** → คลิกขวาที่โหนด → เปิด **View** เพื่อดูกราฟ
 
**ตรวจสอบผล:** กราฟต้องแสดง **ลูกค้าครบ 350 จุด** แยกเป็น 3 สีตามกลุ่ม — หากเห็นเพียง 3 จุด แสดงว่าสายเชื่อมต่อจาก Port 2 (centroid) ของ k-Means ต้องแก้
 
> 📊 **การอ่านผล Scatter Plot — สิ่งที่จะเห็นจริง**
> เมื่อใช้แกน `num_products` × `complaint_count` จุดจะเรียงตัวเป็น **ตาราง (grid)** ไม่กระจายอิสระ — เป็นเรื่องปกติ ไม่ใช่ความผิดพลาด เพราะทั้งสองตัวแปรเป็น**จำนวนเต็ม** (num_products = 1–5, complaint_count = 0–5) เมื่อ normalize แล้วจึงมีเพียงไม่กี่ค่า จุดลูกค้า 350 รายจึงไปซ้อนทับกันบนตำแหน่ง grid ประมาณ 30 จุด (เรียกว่า *overplotting*)
>
> แม้จะซ้อนกัน แต่จะเห็น **แนวโน้มการแบ่งกลุ่มชัดเจน**:
> - **cluster_1** (complaint ต่ำสุด) — เกาะอยู่แถวล่างสุด `complaint_count = 0`
> - **cluster_2** (ผลิตภัณฑ์น้อย) — เกาะอยู่คอลัมน์ซ้าย `num_products` ต่ำ
> - **cluster_0** (ผลิตภัณฑ์มาก + ร้องเรียนมาก) — เกาะอยู่มุมขวาบน
>
> 💡 เนื่องจากจุดซ้อนทับกัน Scatter Plot จึงบอก "จำนวนลูกค้าต่อตำแหน่ง" ไม่ได้ — **การตีความเชิงปริมาณที่แม่นยำที่สุดให้ดูจากตาราง GroupBy (Node 7)** ส่วน Scatter Plot ใช้เพื่อ "เห็นภาพรวมการแยกกลุ่ม" เท่านั้น
 
---
 
### Node 7: GroupBy (สรุปลักษณะแต่ละ Cluster)
 
**ค้นหาใน Node Repository:** `Manipulation → Aggregation → GroupBy`
 
**เชื่อมต่อ:** **Port 1 "Labeled input"** ของ k-Means → Input ของ GroupBy
 
> 🔴 ต้องต่อจาก **Port 1** ของ k-Means — หากต่อ Port 2 (Clusters) ช่อง Available columns จะไม่มี `Cluster` ให้จัดกลุ่ม (จะขึ้น warning)
 
**จุดประสงค์:** คำนวณค่าเฉลี่ยของแต่ละ feature แยกตาม Cluster เพื่อตีความว่าแต่ละกลุ่มมีลักษณะอย่างไร
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog
 
**แท็บ Settings → Groups:**
 
| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Group column(s)** | `Cluster` |
 
**แท็บ Settings → Manual Aggregation:** เพิ่มคอลัมน์ต่อไปนี้ พร้อมตั้ง Aggregation = `Mean`
 
| Column | Aggregation Method |
|--------|-------------------|
| `credit_score` | `Mean` |
| `account_balance` | `Mean` |
| `complaint_count` | `Mean` |
| `num_products` | `Mean` |
| `login_count_30d` | `Mean` |
| `nps_score` | `Mean` |
| `transactions_3m` | `Mean` |
 
กด **OK** → **Execute**
 
**ผลลัพธ์จริงจากการรัน k-Means บนเครื่อง** — ค่า centroid จาก Port 2 "Clusters" (เป็น normalized [0,1]):
 
| Centroid (normalized) | cluster_0 | cluster_1 | cluster_2 |
|-----------------------|----------:|----------:|----------:|
| credit_score | 0.491 | 0.472 | 0.572 |
| account_balance | 0.495 | 0.545 | 0.476 |
| num_products | 0.793 | 0.599 | 0.151 |
| login_count_30d | 0.614 | 0.411 | 0.474 |
| transactions_3m | 0.442 | 0.377 | 0.602 |
| complaint_count | 0.704 | 0.173 | 0.591 |
| nps_score | 0.548 | 0.437 | 0.517 |
 
**ผลจาก GroupBy — ค่าเฉลี่ยจริง (raw) แต่ละ Cluster** (แปลงกลับเป็นหน่วยเดิมเพื่อให้ตีความง่าย):
 
| Feature (Mean) | cluster_0 | cluster_1 | cluster_2 |
|----------------|----------:|----------:|----------:|
| จำนวนลูกค้า | 110 | 131 | 109 |
| credit_score | 570 | 560 | **614** |
| account_balance | 986,629 | **1,086,189** | 947,993 |
| num_products | **4.2** | 3.4 | **1.6** |
| login_count_30d | **36.8** | 24.7 | 28.4 |
| transactions_3m | 39.7 | 33.9 | **54.2** |
| complaint_count | **3.5** | **0.9** | 3.0 |
| nps_score | 5.5 | 4.4 | 5.2 |
| **churn rate** | 16.4% | **9.2%** | **21.1%** |
 
> 💡 **ข้อสังเกต:** centroid ใน Port 2 เป็นค่า **normalized** — ใช้เปรียบเทียบกลุ่มได้ แต่ตีความเป็นภาษาคนยาก จึงควรดูค่าเฉลี่ย **raw** จาก GroupBy (ตารางที่สอง) คู่กันเสมอ
 
**การตีความ (ตั้งชื่อกลุ่มจากค่า centroid จริง):**
 
| Cluster | ลักษณะเด่น | ชื่อกลุ่ม | Action สำหรับ Audit/Business |
|---------|------------|-----------|------------------------------|
| **cluster_0** (110) | ใช้ผลิตภัณฑ์มากสุด (4.2), ล็อกอินถี่สุด — แต่**ร้องเรียนสูงสุด (3.5)** | **Active แต่มีปัญหาบริการ** | ดูแล Service Quality เร่งด่วน |
| **cluster_1** (131) | **ร้องเรียนต่ำสุด (0.9), churn ต่ำสุด (9.2%)**, ยอดเงินสูงสุด | **ลูกค้าเสถียร–ไม่มีปัญหา** | Retain & รักษาความสัมพันธ์ |
| **cluster_2** (109) | **ผลิตภัณฑ์น้อยมาก (1.6), churn สูงสุด (21.1%)**, เครดิตสูงสุด | **ผูกพันต่ำ–เสี่ยง Churn สูงสุด** | 🔴 Cross-sell เร่งด่วน & ป้องกัน Churn |
 
> 💡 **Insight สำคัญ:** cluster_0 ร้องเรียนสูงสุดแต่ churn ไม่ใช่สูงสุด (16.4%) ขณะที่ cluster_2 ร้องเรียนปานกลางแต่ churn สูงสุด (21.1%) — แสดงว่า **"จำนวนผลิตภัณฑ์น้อย" เป็นสัญญาณเตือน Churn ที่แรงกว่า "จำนวนข้อร้องเรียน"** ลูกค้าที่ผูกพันกับธนาคารผ่านผลิตภัณฑ์เดียวพร้อมจะจากไปได้ง่ายกว่า
 
> ⚠️ **หมายเหตุ:** เลข cluster_0/1/2 เป็นป้ายชื่อที่ระบบกำหนด — **หากรันด้วย seed อื่นหรือเครื่องอื่น เลขอาจสลับกัน** สิ่งที่คงที่คือ "ลักษณะของกลุ่ม" ไม่ใช่ตัวเลข ผู้เรียนต้องดู centroid ของตนเองแล้วจับคู่กับการตีความข้างต้น
 
> ⚠️ **หมายเหตุ:** ป้ายชื่อ A/B/C ในตารางนี้แทน cluster_0/1/2 — **เลข cluster ในเครื่องของอาจารย์อาจสลับลำดับกัน** ขึ้นกับ implementation ผู้เรียนต้องดูค่า centroid จริงจาก GroupBy ของตนเองแล้วจับคู่กับการตีความข้างต้น
 
---
 
### Node 8: Numeric Distances (กำหนดวิธีวัดระยะทางให้ DBSCAN)
 
**ค้นหาใน Node Repository:** `Analytics → Mining → Distance Matrix → Numeric Distances`
 
> ℹ️ หากไม่พบโหนดนี้ ต้องติดตั้ง Extension **KNIME Distance Matrix** ก่อน (เมนู `File → Install KNIME Extensions`)
 
**จุดประสงค์:** โหนด DBSCAN ของ KNIME **ไม่ได้คำนวณระยะทางเอง** — ต้องรับ "Distance Model" จากภายนอก โหนด `Numeric Distances` ทำหน้าที่กำหนดว่าจะใช้วิธีวัดระยะทางแบบใด แล้วส่งต่อให้ DBSCAN
 
**เชื่อมต่อ:** Output ของ Normalizer → Input ของ Numeric Distances
 
**ดับเบิ้ลคลิก** เพื่อตั้งค่า:
 
| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Columns** | เลือกทั้ง 8 คอลัมน์ที่ Normalize แล้ว |
| **Distance selection** | `Euclidean` |
 
กด **OK** → **Execute**
 
---
 
### Node 9: DBSCAN
 
**ค้นหาใน Node Repository:** `Analytics → Mining → Clustering → DBSCAN`
 
> ℹ️ DBSCAN มี **2 input ports** — หากเชื่อมไม่ครบทั้งสองพอร์ต โหนดจะขึ้นสถานะแดงและสั่ง Execute ไม่ได้
 
**เชื่อมต่อ — ต้องต่อ 2 เส้น:**
 
| เส้นที่ | จาก | ไปยังพอร์ต DBSCAN |
|---------|-----|-------------------|
| 1 | **Normalizer** | พอร์ต **Input data** (ข้อมูล 350 แถว) |
| 2 | **Numeric Distances** | พอร์ต **Distance Model** |
 
```
Normalizer ─┬───────────────────────────→ [Input data]      DBSCAN
            └─→ Numeric Distances ──────→ [Distance Model]   DBSCAN
```
 
**จุดประสงค์:** ค้นหา Outlier และกลุ่มรูปทรงไม่สม่ำเสมอ — DBSCAN ไม่ต้องกำหนดจำนวน Cluster ล่วงหน้า และตรวจจับ **Noise/Anomaly** ได้โดยตรง
 
**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — แท็บ **Options**
 
| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Epsilon (ε)** | `0.7` | รัศมี neighborhood — ระยะสูงสุดที่ถือว่า "ใกล้กัน" ใน normalized space |
| **Minimum points** | `5` | จำนวนจุดขั้นต่ำใน neighborhood เพื่อเป็น Core Point |
 
> ⚠️ Dialog DBSCAN มีแค่ **Epsilon** และ **Minimum points** (แท็บชื่อ **"Options"** ไม่ใช่ "DBSCAN Settings") — **ไม่มีช่อง "Distance measure"** เพราะวิธีวัดระยะทางถูกกำหนดจากโหนด `Numeric Distances` (Node 8) แล้วป้อนเข้าทาง Distance Model port
 
> **DBSCAN ทำงานอย่างไร?**
> 1. **Core Point:** จุดที่มีเพื่อนบ้านอย่างน้อย MinPts ตัวภายในรัศมี ε
> 2. **Border Point:** จุดที่อยู่ในรัศมีของ Core Point แต่ตัวเองไม่ใช่ Core Point
> 3. **Noise/Outlier:** จุดที่ไม่ใช่ทั้ง Core และ Border → ลูกค้าพฤติกรรมผิดปกติ = เป้าหมาย Audit
 
> **ทำไม ε = 0.7 ไม่ใช่ 0.3 หรือ 1.0? — ผลการ tuning จริงบนข้อมูลนี้:**
>
> | Epsilon (MinPts=5) | ผลลัพธ์ | ประเมิน |
> |--------------------|---------|---------|
> | 0.3 | 0 cluster, **noise = 350** (ทุกจุดเป็น Noise) | ❌ เล็กเกินไป |
> | 0.5 | แตกเป็น ~9 กลุ่มเศษเล็ก | ❌ |
> | **0.7** | **1 กลุ่มหลัก + 3 outliers** | ✅ **เหมาะสม** |
> | 1.0 | 1 กลุ่ม, noise = 0 (ไม่พบ outlier) | ❌ ใหญ่เกินไป |
>
> ข้อมูลนี้มี 8 มิติ จึงเกิด *curse of dimensionality* — ระยะทางเฉลี่ยระหว่างจุดสูง ทำให้ ε ต้องค่อนข้างกว้าง ค่า ε ที่ดีคือค่าที่ให้ "1 กลุ่มหลัก + outlier จำนวนน้อย"
>
> 💡 ตัวเลขข้างต้นมาจาก reference implementation — ผลใน KNIME อาจต่างเล็กน้อย ให้อาจารย์ดู **จำนวน Noise จริง** จาก output แล้วปรับ ε ทีละ 0.05–0.1 จนได้ outlier ในระดับที่เหมาะ
 
กด **OK** → **Execute**
 
**ผลลัพธ์จริงจากการรัน** — DBSCAN ให้ **2 output ports:**
 
- **`1: Data With Cluster IDs`** — ข้อมูล 350 แถว + คอลัมน์ `Cluster`
- **`2: Summary Table`** — สรุปจำนวนสมาชิกแต่ละกลุ่ม:
| Cluster | Count |
|---------|------:|
| `Cluster_0` (กลุ่มหลัก) | 347 |
| `Noise` (ลูกค้าผิดปกติ) | 3 |
 
> 💡 **ข้อสังเกตเรื่องชื่อกลุ่ม:** DBSCAN ใช้คำว่า **`Cluster_0`** (C ใหญ่ มี underscore) และ **`Noise`** — ต่างจาก k-Means ที่ใช้ `cluster_0` (c เล็ก) ต้องระวังเวลานำไปใช้ใน Row Filter
>
> 💡 ผลลัพธ์ตรงตามคาด: ε=0.7, MinPts=5 ให้ **1 กลุ่มหลัก (347 ราย) + Noise 3 ราย** — เหมาะกับเป้าหมาย "หาลูกค้าผิดปกติจำนวนน้อยเพื่อ Audit"
 
---
 
### Node 10: Row Filter — กรองเฉพาะ Outlier
 
**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Filter`
 
**เชื่อมต่อ:** Output `1: Data With Cluster IDs` ของ DBSCAN → Input ของ Row Filter
 
**ตั้งค่า:** กด **Add criterion** → Column = `Cluster`, Operator = `equals`, Value = **`Noise`** → Filter behavior = `Output matching rows`
 
กด **Ok and Execute** → ได้รายชื่อลูกค้าพฤติกรรมผิดปกติ = **Audit Target**
 
**ผลลัพธ์จริง — Outlier 3 ราย (ε = 0.7, MinPts = 5):**
 
| RowID | customer_id | จุดผิดปกติ (พฤติกรรมขัดแย้งในตัวเอง) |
|-------|-------------|--------------------------------------|
| Row54 | CUS00055 | เครดิตต่ำมาก (392) แต่ยอดเงินสูงมาก (~1.68M) และล็อกอินน้อยมาก (4 ครั้ง/30วัน) แต่ทำธุรกรรมถี่ (85 ครั้ง/3เดือน) |
| Row152 | CUS00153 | เครดิตสูงเยี่ยม (829) ใช้งานถี่มาก แต่ร้องเรียนสูงสุด (5 ครั้ง) — โปรไฟล์ลูกค้าดีที่กลับร้องเรียนหนัก |
| Row222 | CUS00223 | ร้องเรียนสูงสุด (5) และ NPS = 0 (ต่ำสุด) แต่ยอดเงินสูง (~1.22M) — และเป็นรายเดียวใน 3 ที่ churn จริง |
 
> 💡 **หมายเหตุ:** Workflow นี้ตัดคอลัมน์ `customer_id` ออกตั้งแต่ Node 2 (Column Filter) ดังนั้นใน output อาจารย์จะเห็นเป็น **RowID** (Row54, Row152, Row222) เท่านั้น — ตาราง customer_id ข้างต้นได้จากการเทียบกลับกับไฟล์ต้นฉบับ หากต้องการให้ผลลัพธ์แสดง `customer_id` โดยตรง สามารถใช้โหนด **Joiner** ดึง `customer_id` จาก CSV Reader กลับเข้ามาภายหลังได้
 
> 💡 ลูกค้า 3 รายนี้มีโปรไฟล์ "ขัดแย้งในตัวเอง" ซึ่ง k-Means มองข้ามไป (ถูกบังคับรวมเข้ากลุ่มใดกลุ่มหนึ่ง) แต่ DBSCAN แยกออกมาเป็น Noise ได้ — เป็นจุดแข็งของ DBSCAN ในงานตรวจจับ Anomaly
 
---
 
## 📊 สรุปการวิเคราะห์ Lab 6
 
### k-Means vs DBSCAN สำหรับงาน Audit
 
| ด้าน | k-Means | DBSCAN |
|------|---------|--------|
| จำนวน Cluster | ต้องกำหนด k ล่วงหน้า | ค้นพบอัตโนมัติ |
| รูปทรง Cluster | กลม (Spherical) | ทุกรูปทรง |
| Outlier Detection | ไม่ตรงไปตรงมา (ทุกจุดถูกบังคับเข้ากลุ่ม) | ✅ ตรวจจับ Noise ได้โดยตรง |
| ความไวต่อพารามิเตอร์ | ปานกลาง (k, seed) | สูงมาก (ε, MinPts) |
| ประโยชน์ใน Audit | จัดกลุ่ม Risk Segment | หา Anomaly / Fraud Candidate |
 
### ข้อค้นพบเชิงธุรกิจ
 
- k-Means แบ่งลูกค้าได้ 3 กลุ่มที่ตีความได้ชัด — กลุ่ม `cluster_2` "ผูกพันต่ำ" (ใช้ผลิตภัณฑ์เฉลี่ยเพียง 1.6 อย่าง) มี churn rate สูงสุด **21.1%** ควรเป็นเป้าหมาย Cross-sell และป้องกันการเสียลูกค้า
- พบ Insight ว่า "จำนวนผลิตภัณฑ์น้อย" เป็นสัญญาณเตือน Churn ที่แรงกว่า "จำนวนข้อร้องเรียน" — กลุ่มที่ร้องเรียนสูงสุด (`cluster_0`) กลับ churn น้อยกว่ากลุ่มที่ใช้ผลิตภัณฑ์น้อย
- DBSCAN แยกลูกค้าผิดปกติ 3 ราย (CUS00055, CUS00153, CUS00223) ที่มีโปรไฟล์ขัดแย้ง (เครดิต/ยอดเงิน/ร้องเรียนไม่สอดคล้องกัน) — เป็นรายการที่ควร Flag ตรวจสอบเชิงลึก
- **บทเรียนเรื่องการ Visualize:** Scatter Plot บนแกน `credit_score` × `account_balance` แสดงจุดปนกัน เพราะสองมิตินี้ไม่ใช่มิติที่แยกกลุ่ม — การเห็น cluster แยกชัดต้องเลือกแกนที่ centroid ต่างกันมาก (`num_products`, `complaint_count`) บทเรียนคือ *"กราฟ 2 มิติเป็นเพียงภาพฉายของการจัดกลุ่มหลายมิติ"*
- **บทเรียนสำคัญ:** ใช้ k-Means เพื่อ "เข้าใจภาพรวม" และ DBSCAN เพื่อ "จับสิ่งผิดปกติ" — สองเทคนิคเสริมกัน ไม่ใช่แทนกัน
---

### แนวทางประยุกต์ใช้ Machine Learning ใน Internal Audit

| ประเภทงาน | เทคนิค ML | Lab ที่เกี่ยวข้อง |
|-----------|----------|-----------------|
| **ทำนายความเสี่ยง Loan Default** | Classification (DT / SVM) | Lab 3, Lab 4 |
| **ค้นหา Cross-sell Opportunities** | Association Rules (Apriori) | Lab 5 |
| **จัดกลุ่มความเสี่ยงลูกค้า** | Clustering (k-Means) | Lab 6 |
| **ตรวจจับ Fraud / Anomaly** | DBSCAN (Outlier Detection) | Lab 6 |
| **เปรียบเทียบโมเดล** | Scorer + Accuracy Statistics | Lab 3, Lab 4 |

> 💡 **Key Takeaway สำหรับ Internal Audit:**
> - ใช้ **Recall** เป็น KPI หลัก ไม่ใช่ Accuracy — เพราะ FN (พลาด Default จริง) มีต้นทุนสูง
> - ใช้ **DBSCAN** จับ Outlier เพื่อหา Fraud Candidates โดยอัตโนมัติ
> - ใช้ **Association Rules** ตรวจสอบ pattern การใช้บริการที่ผิดปกติ
> - **SMOTE** ช่วยแก้ Class Imbalance ได้โดยไม่ต้องหาข้อมูลจริงเพิ่ม

---

### ตัวอย่าง ทำนายผลลูกค้า (Churn API)
https://knimeml-production.up.railway.app/

---

*เอกสารนี้จัดทำโดย อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.*
*สำหรับการฝึกอบรม Machine Learning and Deep Learning with KNIME — กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา*
*วันที่ 19 พฤษภาคม 2569*
