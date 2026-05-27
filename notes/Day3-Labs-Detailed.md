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
| เวลา        | 09:00–16:00 น. (6 ชั่วโมง) |
| รูปแบบ      | Concept 35% · Hands-on Lab 65% |
| Dataset หลัก | loan_default.csv, customer_churn.csv, time_series_daily_transactions.csv |
| KNIME Version | KNIME Analytics Platform 5.3 (Modern UI) |
| Extensions ที่ต้องมี | KNIME Deep Learning - Keras, TensorFlow 2, Python Integration, Image Processing, Network Mining, Textprocessing, AI Extension |
| Python Environment | Conda environment `knime_python_env` — Python 3.11 + TensorFlow 2.21 CPU |

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

### Extensions ที่ต้องติดตั้งก่อน Lab

ตรวจสอบผ่าน **Menu → Install Extensions** ว่ามี Extensions ดังต่อไปนี้ติดตั้งแล้ว:

| Extension | ใช้ใน Lab |
| --------- | --------- |
| KNIME Deep Learning - Keras Integration | Lab 2, Lab 4 |
| KNIME Deep Learning - TensorFlow 2 Integration | Lab 2, Lab 4 |
| KNIME Python Integration | Lab 2, Lab 4 |
| KNIME Image Processing | Lab 3 (Demo) |
| KNIME Image Processing - Deep Learning Extension | Lab 3 (Demo) |


> **การตั้งค่า Python Environment สำหรับ Deep Learning:**
>
> 
> KNIME → Preferences → KNIME → Python Deep Learning
> → เลือก ● Use special Deep Learning configuration as defined below
> → Library used for the "DL Python" scripting nodes: ● TensorFlow 2
> → Deep Learning Python environment configuration: ● Conda
> → TensorFlow 2 Conda environment: knime_python_env
> → กด Apply and Close
> 
>
> เมื่อเลือก **TensorFlow 2** โหนดตระกูล `DL Python` จะใช้ TensorFlow 2 library — โค้ดทุก Lab ในเอกสารนี้เขียนแบบ `from tensorflow import keras` ซึ่งทำงานกับ TensorFlow 2 ได้โดยตรง หากหน้า Preferences แสดงข้อความสีส้มเตือนเรื่องเวอร์ชัน Keras นั่นเป็นคำเตือนของ Keras integration รุ่นเก่าที่ไม่ได้ใช้ในชุด Lab นี้ จึงไม่กระทบการทำงาน

---

### ⚙️ การเตรียมระบบก่อนเริ่ม Lab (สำคัญมาก — ทำก่อนเปิด KNIME)

เนื่องจาก KNIME 5.3 ใช้ **TensorFlow 2.13** ซึ่งเป็นเวอร์ชันใหม่กว่าที่ KNIME DL plugin รองรับ จึงต้องแทนที่ไฟล์ Python ภายใน KNIME 2 ไฟล์ก่อนทำ Lab ทุกครั้งบนเครื่องที่ยังไม่เคยแก้ไข

#### สาเหตุของปัญหา

TensorFlow 2.12 ขึ้นไปเปลี่ยน Keras Tensor ให้เป็น **unhashable** (ไม่สามารถใช้เป็น key ใน Python `set` ได้) แต่ไฟล์ภายใน KNIME ยังใช้ `set()` เพื่อเก็บ Tensor อยู่ ทำให้เกิด error:

```
TypeError: Tensors are unhashable. Instead, use tensor.ref() as the key.
```

#### ขั้นตอนการแก้ไข (ทำเพียงครั้งเดียวต่อเครื่อง)

**Step 1 — Backup ไฟล์ต้นฉบับ**

เปิด File Explorer ไปที่ทั้ง 2 path นี้แล้ว copy ไฟล์เก็บไว้ (rename เป็น `.bak`):

```
C:\KNIME_MASTER\plugins\org.knime.dl.keras_5.3.0.v202312081534\py\
    DLKerasNetworkSpecExtractor.py  →  DLKerasNetworkSpecExtractor.py.bak

C:\KNIME_MASTER\plugins\org.knime.dl.python_5.3.0.v202312081534\py\
    DLPythonNetwork.py  →  DLPythonNetwork.py.bak
```

**Step 2 — แทนที่ด้วยไฟล์ที่แก้ไขแล้ว**

นำไฟล์ที่วิทยากรแจกมา copy ทับไฟล์เดิม:

| ไฟล์ที่แจก | แทนที่ไฟล์เดิมที่ path |
|-----------|----------------------|
| `DLKerasNetworkSpecExtractor.py` | `C:\KNIME_MASTER\plugins\org.knime.dl.keras_5.3.0.v202312081534\py\` |
| `DLPythonNetwork.py` | `C:\KNIME_MASTER\plugins\org.knime.dl.python_5.3.0.v202312081534\py\` |

**Step 3 — Restart KNIME**

ปิด KNIME แล้วเปิดใหม่ 1 ครั้ง จากนั้นเริ่ม Lab ได้เลย

> **หมายเหตุ:** หากเครื่องนักเรียนแก้ไขแล้วก่อนหน้า ไม่จำเป็นต้องทำซ้ำ สังเกตว่าไฟล์มีบรรทัด comment `# แก้แล้ว` หรือ `# FIX:` อยู่ในโค้ด

#### สิ่งที่ไฟล์แก้ไขเปลี่ยนแปลง

| ไฟล์ | บรรทัด | การแก้ไข |
|------|--------|----------|
| `DLPythonNetwork.py` | 53 | เพิ่ม `try/except` รองรับ pandas 2.0+ ที่ลบ `pandas.util.testing` ออก |
| `DLKerasNetworkSpecExtractor.py` | 73, 166 | เปลี่ยน `_visited_outputs` จาก `set()` เป็น `list` และ `.add()` → `.append()` |
| `DLKerasNetworkSpecExtractor.py` | 149–150 | เปลี่ยน `_visited_inputs` จาก `set()` เป็น `list` และ `.add()` → `.append()` |
| `DLKerasNetworkSpecExtractor.py` | 155, 170, 172 | เปลี่ยน `.index()` และ `in` operator เป็น identity comparison (`is`) เพื่อรองรับ unhashable Tensor |

---

## 🧪 Day3_Lab1_Data_Preparation_for_Deep_Learning

**ชื่อเต็ม:** เตรียมข้อมูล loan_default.csv สำหรับ Deep Learning

**วัตถุประสงค์:** นำเข้า ทำความสะอาด เข้ารหัส Categorical features ด้วย One-Hot Encoding และแบ่งข้อมูลให้พร้อมสำหรับการสร้างโมเดล Neural Network ใน KNIME

**ตำแหน่งใน Schedule:** Section 2 — 10:00–11:00 น.

---

### ข้อมูล Dataset: loan_default.csv

ไฟล์นี้บันทึกประวัติสินเชื่อของลูกค้าธนาคาร **400 rows, 22 columns** — ใช้ทำนายว่าลูกค้าจะ Default (ผิดนัดชำระ) หรือไม่

| # | Column | Data Type | ค่าตัวอย่าง | คำอธิบาย |
|---|--------|-----------|-------------|----------|
| 1 | `loan_id` | String | LN000001 | รหัสสินเชื่อ (ID — ไม่ใช้ใน Model) |
| 2 | `customer_id` | String | CUS00138 | รหัสลูกค้า (ID — ไม่ใช้ใน Model) |
| 3 | `age` | Integer | 47 | อายุผู้กู้ (ปี) |
| 4 | `gender` | String | Male / Female | เพศ (2 ค่า) |
| 5 | `marital_status` | String | Single / Married / Divorced / Widowed | สถานภาพสมรส (4 ค่า) |
| 6 | `education` | String | Primary / Secondary / Vocational / Bachelor / Master / Doctoral | ระดับการศึกษา (6 ค่า) |
| 7 | `employment_type` | String | Permanent Employee / Contract Employee / Freelance / Self-Employed / Business Owner / Government Officer | ประเภทการจ้างงาน (6 ค่า) |
| 8 | `years_employed` | Integer | 26 | อายุงาน (ปี) |
| 9 | `annual_income` | Double | 2,575,488 | รายได้ต่อปี (บาท) |
| 10 | `monthly_income` | Double | 214,624 | รายได้ต่อเดือน (บาท) |
| 11 | `loan_amount` | Double | 1,549,902 | จำนวนเงินกู้ (บาท) |
| 12 | `loan_term_months` | Integer | 48 | ระยะเวลากู้ (เดือน) |
| 13 | `interest_rate` | Double | 13.97 | อัตราดอกเบี้ย (%) |
| 14 | `credit_score` | Integer | 554 | คะแนนเครดิต (300–850) |
| 15 | `existing_loans` | Integer | 1 | จำนวนสินเชื่อที่มีอยู่แล้ว |
| 16 | `collateral_type` | String | Real Estate / Vehicle / Fixed Deposit / Life Insurance / Guarantor / None | ประเภทหลักประกัน (6 ค่า) |
| 17 | `loan_purpose` | String | Personal Loan / Auto Loan / Home Purchase / Medical / Education / Debt Consolidation / Business Expansion | วัตถุประสงค์การกู้ (7 ค่า) |
| 18 | `debt_to_income` | Double | 1.8054 | อัตราส่วนหนี้ต่อรายได้ |
| 19 | `missed_payments_6m` | Integer | 5 | จำนวนครั้งที่ชำระผิดนัดใน 6 เดือน |
| 20 | `months_with_bank` | Integer | 151 | ระยะเวลาเป็นลูกค้าธนาคาร (เดือน) |
| 21 | `num_bank_products` | Integer | 2 | จำนวนผลิตภัณฑ์ธนาคารที่ใช้ |
| 22 | `default` | Integer | **0** (ไม่ผิดนัด) / **1** (ผิดนัด) | **Target column** — ทำนายค่านี้ |

> 💡 **สังเกต:** column `default` เป็น Integer 0/1 อยู่แล้ว — ต่างจาก Day 2 ที่ต้องสร้าง `default_label` (String) สำหรับ ML Classifier  
> สำหรับ Deep Learning ใช้ค่า `default` (0/1 Integer) เป็น Target ได้โดยตรง ไม่ต้องแปลงเพิ่มเติม

---

### Workflow ที่จะสร้าง

```
CSV Reader → Column Filter →  One to Many  → Partitioning → Normalizer
   (Node 1)     (Node 2)        (Node 3)       (Node 4)      (Node 5)
                             (One-Hot Enc.)   ↓ Port 1 (320 rows) ──→ Normalizer (Node 5)
                                              ↓ Port 2  (80 rows) ──→ Test Set → Lab 2
```

> 📝 **Workflow นี้มี 5 nodes:** CSV Reader → Column Filter → One to Many → Partitioning → Normalizer
> ⚠️ **Best Practice — ป้องกัน Data Leakage:** Partitioning (Node 4) อยู่ก่อน Normalizer (Node 5) เพื่อให้ Normalizer คำนวณค่า Min/Max จาก **Training data เท่านั้น** — ไม่ "รั่ว" ข้อมูล Test set เข้ามา

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
> 💡 **KNIME 5.3:** ด้านล่างจะแสดง Preview ข้อมูลอัตโนมัติ ตรวจสอบว่าเห็น column ครบ **22 columns** และ row แรกเป็นข้อมูลจริง (ไม่ใช่ชื่อ column) ก่อนกด OK

**แท็บ Transformation:** ตรวจสอบ Data Type ของแต่ละ column — KNIME จะ auto-detect ได้ถูกต้อง ไม่ต้องแก้ไข

กด **OK** → คลิกขวาที่ node → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table** → ต้องเห็น **400 rows, 22 columns**

> ✅ **ไม่มี Missing Values ในไฟล์นี้** — ทุก column มีข้อมูลครบ 400 แถว ไม่จำเป็นต้องใช้ Missing Value node

---

### Node 2: Column Filter

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**จุดประสงค์:** ตัด columns ที่เป็น ID ออก (`loan_id`, `customer_id`) เพราะไม่มีความหมายเชิง pattern สำหรับโมเดล Neural Network และตัด `monthly_income` ออกเพราะซ้ำซ้อนกับ `annual_income`

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog:

Toggle ด้านบน: **Manual** | Wildcard | Regex | Type → เลือก **Manual** (ปุ่มสีดำ = active)

ด้านล่าง Toggle มีช่อง **Search** พร้อมปุ่ม **Aa** (Case sensitive toggle) — ใช้ค้นหา column ได้รวดเร็ว

จะเห็น 2 panel พร้อมปุ่มลูกศรตรงกลาง:
- `>` ย้าย 1 รายการที่เลือกจาก Includes → Excludes
- `>>` ย้ายทั้งหมด Includes → Excludes
- `<` ย้าย 1 รายการที่เลือกจาก Excludes → Includes
- `<<` ย้ายทั้งหมด Excludes → Includes

**Panel Excludes (ซ้าย)** — ย้าย columns เหล่านี้มาไว้ที่นี่:
- `loan_id` — รหัสสินเชื่อ (ไม่มีความสัมพันธ์กับ default)
- `customer_id` — รหัสลูกค้า (ไม่มีความสัมพันธ์กับ default)
- `monthly_income` — ซ้ำซ้อนกับ `annual_income` (monthly = annual ÷ 12)

`monthly_income` — มี **Perfect Correlation (r = 1.0)** กับ `annual_income` ทุก row ในไฟล์นี้ คือ `monthly_income = annual_income ÷ 12` ตรงทุก row พอดี ตัวเลขต่างกัน (scale ต่างกัน) แต่ให้ข้อมูลสิ่งเดียวกัน Neural Network จะ "เห็น" pattern เดิมซ้ำสองครั้งโดยไม่ได้ข้อมูลใหม่ จึงตัดออก 1 column เพื่อประหยัด parameter และป้องกัน overfitting

**Panel Includes (ขวา)** — ต้องเหลือ **19 columns** นี้:

| # | Column | Data Type | คำอธิบาย |
|---|--------|-----------|----------|
| 1 | `age` | Integer | อายุผู้กู้ |
| 2 | `gender` | String | เพศ → จะทำ One-Hot |
| 3 | `marital_status` | String | สถานภาพสมรส → จะทำ One-Hot |
| 4 | `education` | String | ระดับการศึกษา → จะทำ One-Hot |
| 5 | `employment_type` | String | ประเภทการจ้างงาน → จะทำ One-Hot |
| 6 | `years_employed` | Integer | อายุงาน |
| 7 | `annual_income` | Double | รายได้ต่อปี |
| 8 | `loan_amount` | Double | จำนวนเงินกู้ |
| 9 | `loan_term_months` | Integer | ระยะเวลากู้ |
| 10 | `interest_rate` | Double | อัตราดอกเบี้ย |
| 11 | `credit_score` | Integer | คะแนนเครดิต |
| 12 | `existing_loans` | Integer | จำนวนสินเชื่อที่มีอยู่ |
| 13 | `collateral_type` | String | ประเภทหลักประกัน → จะทำ One-Hot |
| 14 | `loan_purpose` | String | วัตถุประสงค์กู้ → จะทำ One-Hot |
| 15 | `debt_to_income` | Double | อัตราส่วนหนี้ต่อรายได้ |
| 16 | `missed_payments_6m` | Integer | จำนวนครั้งผิดนัดชำระ 6 เดือน |
| 17 | `months_with_bank` | Integer | ระยะเวลาเป็นลูกค้า |
| 18 | `num_bank_products` | Integer | จำนวนผลิตภัณฑ์ธนาคาร |
| 19 | `default` | Integer | **Target** — 0/1 |

กด **Ok and Execute** (ปุ่มสีเหลืองมุมขวาล่าง) เพื่อบันทึกและ Execute พร้อมกัน

**💡 ทำไมตัด monthly_income ออก?**
ตรวจสอบแล้วพบว่า `monthly_income = annual_income ÷ 12` ตรงทุก 400 row โดยไม่มีข้อยกเว้น (Pearson r = 1.000000) — ตัวเลขต่างกันแต่ information เดียวกัน เปรียบเหมือนมีทั้ง column `น้ำหนัก_กิโลกรัม` และ `น้ำหนัก_กรัม` ในตารางเดียวกัน การเก็บทั้งคู่ไว้ทำให้ Neural Network มี parameter เพิ่มขึ้นโดยไม่ได้ประโยชน์

**ตรวจสอบผล:** ต้องเห็น **400 rows, 19 columns** — ไม่มี `loan_id`, `customer_id`, `monthly_income`

---

### Node 3: One to Many

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → One to Many`

**จุดประสงค์:** แปลง Categorical (String) features เป็น **One-Hot Encoding** — สร้าง binary column แยกกันสำหรับแต่ละ category value Neural Network จะเข้าใจได้ถูกต้องโดยไม่มีการตีความลำดับที่ผิดพลาด

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ One to Many

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**Columns to transform | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ Columns to transform:**

**ส่วน Column Selection mode:**

| ตัวเลือก | การเลือก |
|---------|---------|
| ● **Manual Selection** | ✅ เลือก |
| ○ Wildcard/Regex Selection | ❌ ไม่เลือก |

**Panel Exclude (กรอบสีแดง — ซ้าย):**
- ว่างเปล่า — แสดงข้อความ *"No columns in this list"*
- ● **Enforce exclusion**

**Panel Include (กรอบสีเขียว — ขวา):** เพิ่ม String columns ทั้ง 6 ตัว:

| Column | Type | จำนวน category | Columns ที่จะสร้าง (One-Hot) |
|--------|------|----------------|------------------------------|
| `S gender` | String | 2 | `Female`, `Male` |
| `S marital_status` | String | 4 | `Divorced`, `Married`, `Single`, `Widowed` |
| `S education` | String | 6 | `Bachelor`, `Doctoral`, `Master`, `Primary`, `Secondary`, `Vocational` |
| `S employment_type` | String | 6 | `Business Owner`, `Contract Employee`, `Freelance`, `Government Officer`, `Permanent Employee`, `Self-Employed` |
| `S collateral_type` | String | 6 | `Fixed Deposit`, `Guarantor`, `Life Insurance`, `None`, `Real Estate`, `Vehicle` |
| `S loan_purpose` | String | 7 | `Auto Loan`, `Business Expansion`, `Debt Consolidation`, `Education`, `Home Purchase`, `Medical`, `Personal Loan` |

> 💡 ตัวอักษร `S` ข้างหน้าชื่อ column = String type

**ส่วน Settings (ด้านล่าง panel):**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **☑ Remove included columns from output** | ✅ ติ๊ก | ลบ String columns เดิมออกจาก output — เหลือเฉพาะ One-Hot columns |

> ⚠️ **สำคัญ:** ค่า default ของ checkbox นี้คือ **ไม่ติ๊ก** (☐) — ต้องติ๊กเองเพื่อลบ String columns เดิมออก มิฉะนั้น Neural Network จะได้รับ String columns ที่ใช้งานไม่ได้ปะปนอยู่ด้วย

กด **OK**

**ตรวจสอบผล:**
- String columns 6 ตัวหายไป แทนที่ด้วย One-Hot binary columns รวม **31 columns** (2+4+6+6+6+7)
- ผลรวมทั้งหมด: 13 columns (Numeric + Target `default`) + 31 One-Hot = **44 columns**
- ค่าในทุก One-Hot column เป็น **0 หรือ 1 เท่านั้น** — แต่ละแถวจะมีค่า 1 เพียงคอลัมน์เดียวในแต่ละกลุ่ม

**ตัวอย่างผลลัพธ์:**

| Female | Male | Widowed | Single | Married | Divorced | ... |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 0 | 1 | 1 | 0 | 0 | 0 | ... |
| 1 | 0 | 0 | 0 | 1 | 0 | ... |

> 💡 **ทำไมต้องใช้ One-Hot แทน Label Encoding?**
> Label Encoding (Category to Number) กำหนดค่า 0, 1, 2, 3... ซึ่ง Neural Network อาจตีความว่ามีลำดับ เช่น `Doctoral(1)` อยู่ "ระหว่าง" `Bachelor(0)` กับ `Master(2)` ทั้งที่ไม่มีความสัมพันธ์เชิงลำดับ
> One-Hot Encoding แก้ปัญหานี้ — แต่ละ category เป็นอิสระจากกัน Neural Network เรียนรู้ pattern ได้ถูกต้องกว่า

---

### Node 4: Partitioning

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Partitioning`

**จุดประสงค์:** แบ่งข้อมูลเป็น Training Set (80%) และ Test Set (20%) แบบสุ่ม (seed คงที่) — ได้ผลลัพธ์ reproducible เหมือนเดิมทุกครั้ง

> 💡 **ทำไม Partitioning ต้องอยู่ก่อน Normalizer? (Best Practice — ป้องกัน Data Leakage)**
> ถ้าทำ Normalizer ก่อน Partitioning, Normalizer จะ "เห็น" ทั้ง Training และ Test data รวมกัน → ค่า Min/Max ที่คำนวณได้จะ "รั่ว" ข้อมูลของ Test set เข้าไปใน Training — ทำให้ผลการประเมินโมเดลสูงเกินจริง
> **หลักการ:** Normalizer ควร fit บน Training data เท่านั้น

> ⚠️ **ข้อจำกัดของ KNIME Partitioning node:** Stratified sampling ใช้ได้เฉพาะ **Nominal (String)** column เท่านั้น — หลังจาก One to Many ทุก column กลายเป็น Numeric จึงไม่มี String column เหลือและ Stratified sampling จะ grayed out เลือกไม่ได้
> **แนวทางแก้ไข:** ใช้ **Draw randomly + seed 42** แทน — dataset 400 rows ขนาดเล็กผลแทบไม่ต่างกัน และ seed คงที่ทำให้ผลลัพธ์ reproducible

**เชื่อมต่อ:** Output ของ One to Many → Input ของ Partitioning

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog — มีแท็บ:
**First partition | Flow Variables | Job Manager Selection | Memory Policy**

**แท็บ First partition:**

**ส่วน Choose size of first partition:**

| ตัวเลือก | การเลือก | ค่า | คำอธิบาย |
|---------|---------|-----|----------|
| ○ **Absolute** | ❌ ไม่เลือก | 100 | จำนวนแถวตายตัว |
| ● **Relative[%]** | ✅ เลือก | **`80`** | Training = 80% = **320 rows** |

**ส่วน Sampling method:**

| ตัวเลือก | การเลือก | คำอธิบาย |
|---------|---------|----------|
| ○ Take from top | ❌ | เอา rows แรกๆ ตามลำดับ |
| ○ Linear sampling | ❌ | สุ่มแบบ linear |
| ● **Draw randomly** | ✅ เลือก | สุ่มแบบ random — ใช้ร่วมกับ seed เพื่อให้ผลซ้ำได้ |
| ○ Stratified sampling | ❌ (grayed out) | ต้องการ Nominal/String column — ไม่สามารถใช้ได้หลัง One to Many |

> 💡 **ทำไม Stratified sampling เลือกไม่ได้?**
> KNIME Partitioning node กำหนดให้ Stratified sampling ทำงานได้เฉพาะเมื่อมี **Nominal (String) column** ในตาราง
> หลังผ่าน One to Many (Node 3) แล้ว String columns ทั้ง 6 ตัว (gender, marital_status, education ฯลฯ) ถูกแปลงเป็น Integer (One-Hot) ทั้งหมด → ไม่มี String column เหลือ → Stratified grayed out
> **Draw randomly + seed 42** ให้ผลที่ acceptable สำหรับ dataset 400 rows

**Use random seed:** ☑ ติ๊ก → ใส่ค่า `42`

> 💡 seed `42` ทำให้การสุ่มให้ผลเดิมทุกครั้ง — ผู้เรียนทุกคนได้ Training/Test data ชุดเดียวกัน

กด **OK - Execute**

**Output Ports ที่ได้:**

| Port | แท็บ | จำนวน rows | คำอธิบาย |
|------|------|-----------|----------|
| **▶ Port บน** | *1: First partition (as defined in dialog)* | **320 rows** | Training Set (80%) — ต่อไปยัง Node 5 (Normalizer) |
| **▶ Port ล่าง** | *2: Second partition (remaining rows)* | **80 rows** | Test Set (20%) — ส่งไปยัง DL Python Network Executor ใน Lab 2 |

> 💡 **KNIME 5.3:** คลิกที่ node แล้วดู Output ด้านล่าง — จะเห็น 2 แท็บ *"1: First partition"* และ *"2: Second partition"* พร้อมแท็บ *"Flow Variables"* สามารถสลับดูข้อมูลแต่ละ partition ได้ทันที

---

### Node 5: Normalizer

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`

**จุดประสงค์:** ปรับสเกล Numeric features ให้อยู่ในช่วง [0, 1] — Neural Network ต้องการข้อมูลที่ scale เดียวกัน ป้องกัน column ที่มีตัวเลขใหญ่ (เช่น `annual_income` ≈ 2.5 ล้าน) กดทับ column เล็ก (เช่น `existing_loans` ≈ 0–5)

**เชื่อมต่อ:** **Port บน (Port 1 — Training 320 rows)** ของ Partitioning → Input ของ Normalizer

> ⚠️ **สำคัญ:** ต่อจาก **Port บน (Port 1)** เท่านั้น — Port ล่าง (Port 2 = Test 80 rows) จะถูก normalize ใน Lab 2 โดยใช้ **Normalization Model** จาก Port 2 ของ Node นี้ (ไม่คำนวณ Min/Max ใหม่จาก Test data)

**ดับเบิ้ลคลิก** เพื่อเปิด Configuration Dialog:

Toggle ด้านบน: **Manual** | Wildcard | Regex | Type → เลือก **Manual** (ปุ่มสีดำ = active)

ด้านล่าง Toggle มีช่อง **Search** พร้อมปุ่ม **Aa** (Case sensitive toggle)

**Panel Excludes (ซ้าย):**
- ว่างเปล่า — แสดงข้อความ *"No columns in this list"*

**Panel Includes (ขวา):** ใน Dialog จะเห็น columns ที่ระบุไว้ 6 ตัว + **Any unknown column** ที่แถวล่างสุด — ซึ่งหมายความว่า Normalizer จะ Normalize **ทุก column ทั้ง 44 columns** ที่รับเข้ามา

| กลุ่ม | Columns | ช่วงค่าจริง | หลัง Normalize |
|-------|---------|------------|----------------|
| **Numeric ที่แสดงใน Dialog** | `age` | 22 – 65 | 0.0 – 1.0 |
| | `years_employed` | 0 – 35 | 0.0 – 1.0 |
| | `annual_income` | 187,764 – 4,193,832 | 0.0 – 1.0 |
| | `loan_amount` | 67,909 – 4,989,701 | 0.0 – 1.0 |
| | `loan_term_months` | 12 – 300 | 0.0 – 1.0 |
| | `interest_rate` | 3.55 – 17.99 | 0.0 – 1.0 |
| **Numeric ที่ครอบคลุมโดย Any unknown column** | `credit_score` | 300 – 847 | 0.0 – 1.0 |
| | `existing_loans` | 0 – 5 | 0.0 – 1.0 |
| | `debt_to_income` | 0.02 – 98.68 | 0.0 – 1.0 |
| | `missed_payments_6m` | 0 – 6 | 0.0 – 1.0 |
| | `months_with_bank` | 1 – 240 | 0.0 – 1.0 |
| | `num_bank_products` | 1 – 6 | 0.0 – 1.0 |
| **Target** | `default` | 0 – 1 | 0.0 – 1.0 (ไม่เปลี่ยน) |
| **One-Hot columns (31 ตัว)** | `Male`, `Female`, `Widowed`, ... | 0 – 1 | 0.0 – 1.0 (ไม่เปลี่ยน) |

> 💡 **ทำไม One-Hot columns และ `default` "ไม่เปลี่ยน" หลัง Normalize?**
> เพราะค่าของมันเป็น 0 และ 1 อยู่แล้ว — Min-Max Normalization คำนวณ (x - min) / (max - min) = (0 - 0) / (1 - 0) = 0 และ (1 - 0) / (1 - 0) = 1 ผลลัพธ์จึงเหมือนเดิม

> 💡 **"Any unknown column"** ที่แถวล่างสุดของ Includes panel — คือกลไกของ KNIME ที่บอกว่า "columns ใดก็ตามที่ไม่ได้ระบุชื่อไว้ใน Excludes จงนำมา Normalize ด้วย" ทำให้ไม่ต้องเพิ่มทีละ column ด้วยตัวเอง

**ส่วน Normalization method:**

| ตัวเลือก | การเลือก | คำอธิบาย |
|---------|---------|----------|
| **Min-max** | ✅ เลือก (ปุ่มสีดำ) | แปลงค่าทุก column ให้อยู่ในช่วง [0, 1] |
| Z-score | ❌ ไม่เลือก | Standardize ด้วย mean=0, std=1 |
| Decimal scaling | ❌ ไม่เลือก | หารด้วย 10^k |

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Minimum** | `0` | ค่าต่ำสุดหลัง Normalize |
| **Maximum** | `1` | ค่าสูงสุดหลัง Normalize |

กด **Ok and Execute** (ปุ่มสีเหลืองมุมขวาล่าง)

> 💡 **Port 2 ของ Normalizer = Normalization Model** — KNIME บันทึกค่า Min/Max ของแต่ละ column ไว้ในนี้
> ใน Lab 2 ต้องต่อ Port 2 นี้เข้ากับ **Normalizer Apply** node เพื่อ normalize Test data (Port 2 ของ Partitioning) ด้วยค่า Min/Max เดิม ไม่คำนวณใหม่จาก Test data

**ตรวจสอบผล:** Training data 320 rows, Numeric columns ทั้งหมดมีค่าอยู่ในช่วง [0, 1] — พร้อมสำหรับ Lab 2

---

### สรุป Lab 1 — เปรียบเทียบกับ Day 2

| ขั้นตอน | Day 2 (ML) | Day 3 (Deep Learning) | เหตุผลที่ต่างกัน |
|---------|-----------|----------------------|-----------------|
| โหลดข้อมูล | CSV Reader | CSV Reader | เหมือนกัน |
| สร้าง Target String | Rule Engine (`default` → `default_label` "Yes/No") | **ไม่มี** | DL ใช้ Integer 0/1 โดยตรง |
| ลบ ID columns | ✅ Column Filter | ✅ Column Filter | เหมือนกัน |
| One-Hot Encoding | **ไม่มี** — ML บาง algorithm รับ String ได้ | ✅ **One to Many** | Neural Network ต้องการ Input เป็นตัวเลขเท่านั้น |
| แบ่ง Dataset | ✅ Partitioning — Stratified บน `default_label` (String) | ✅ Partitioning — **Draw randomly**, seed=42 (KNIME ต้องการ String สำหรับ Stratified) | ทำก่อน Normalize เพื่อป้องกัน Data Leakage |
| Normalize | ✅ Normalizer (Numeric เท่านั้น) | ✅ Normalizer — fit บน Training data เท่านั้น | ป้องกัน Data Leakage จาก Test set |

---

## Day3_Lab2_Deep_Learning_Classification

**ชื่อ Lab:** Deep Learning for Loan Default Classification
**Dataset:** ข้อมูลที่เตรียมจาก Lab 1 (Prepared Data)
**วัตถุประสงค์:** สร้างและ Train Neural Network สำหรับทำนาย Loan Default โดยใช้ Keras/TensorFlow ใน KNIME
**ตำแหน่งใน Schedule:** Section 3 — 11:15–12:00 น.

---

### Overview Workflow

```
[Lab1_Data_Preparation Metanode]
  ├─ Port 0: Training data (320 rows, normalized) ─────────────────────────────┐
  ├─ Port 1: Test data    (80 rows,  normalized) ──────────────────────┐        │
  └─ Port 2: Normalize Model (unused in Lab 2)                         │        │
                                                                        │        │
[Node 1]                    [Node 2]                                    │        │
DL Python Network Creator → DL Python Network Learner ←─────────────────┘        │
(สร้าง architecture)         (train 200 epochs)                                  │
        │                           │ trained model                              │
        │                           ▼                                            │
        │                   [Node 3]                                             │
        │             DL Python Network Executor ←──────────────────────────────┘
        │             (ทำนายบน Test set)
        │                    │
        │              ┌─────┴──────────────┐
        │              ▼                    ▼
        │        [Node 4]            [Node 6]
        │        Rule Engine     Number to String
        │    (prob → class 0/1)  (default: int→str)
        │              │                    │
        │          [Node 5]            [Node 7]
        │           Scorer             ROC Curve
        │       (Accuracy/F1)           (AUC)
```

> **หมายเหตุสำคัญ — Port Type Compatibility:**
> ใน KNIME 5.3 โหนดตระกูล **DL Python** (Creator / Learner / Executor) ใช้ port type เดียวกัน (`TF2NetworkPortObjectSpec`) จึงเชื่อมต่อกันได้โดยตรง
> **Keras Network Learner** และ **Keras Network Executor** ต้องการ port type `DLKerasNetworkPortObjectSpecBase` ซึ่งไม่ compatible กับ TF2Network — ใช้ไม่ได้กับ workflow นี้

> **หมายเหตุ — Lab1_Data_Preparation Metanode:**
> Metanode นี้ส่งออก 3 ports: Training data (normalized), Test data (normalized), Normalization Model
> ทั้ง Training และ Test data ผ่าน Normalizer แล้วภายใน metanode — Test data ใช้ **Normalize Model** จาก Training Normalizer (Apply mode) ป้องกัน Data Leakage
> ⚠️ ใน Normalizer ของ Lab 1 ต้อง **exclude `default` column ออกจากการ normalize** เพราะ `default` เป็น target variable ไม่ใช่ feature — ถ้า normalize แล้วจะกลายเป็น Double (0.0/1.0) ทำให้ Scorer เห็นเป็น 4 classes แทนที่จะเป็น 2 classes

---

### Node 1: DL Python Network Creator

**ชื่อ Node:** DL Python Network Creator
**Node Path:** `Analytics → Integrations → Deep Learning → DL Python Network Creator`
**วัตถุประสงค์:** สร้าง Neural Network Architecture ด้วย Python/Keras code

> **วิธีค้นหาโหนด:** พิมพ์ `DL Python` ในช่องค้นหา Node Repository — จะพบกลุ่มโหนด DL Python Network Creator / Editor / Learner / Executor

**การเชื่อมต่อ:**

> 💡 **DL Python Network Creator ไม่มี Input port** — เป็น node ที่สร้าง Network Architecture จาก Python code ล้วนๆ ไม่ต้องการ input data ใดๆ
> Output port เดียวของ node นี้ (สีส้ม) = **TF2 Network** → ต่อไปยัง DL Python Network Learner Port 1

| Port | ทิศทาง | ต่อกับ |
|------|--------|--------|
| Output 1 (TF2 Network) | → ออก | DL Python Network Learner — Port 1 |

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Script**

พิมพ์โค้ด Python ต่อไปนี้ในช่อง Script:

```python
from tensorflow import keras

# จำนวน input features หลังเตรียมข้อมูลจาก Lab 1:
# 44 columns ทั้งหมด ลบ 1 target column (default) = 43 features
n_features = 43

# สร้าง Neural Network Architecture — Simple (เหมาะกับ dataset เล็ก 320 rows)
model = keras.Sequential([
    keras.layers.Dense(16, activation="relu", input_shape=(n_features,)),
    keras.layers.Dense(8, activation="relu"),
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

> 💡 **ทำไม n_features = 43?**
> Lab 1 ผลิต 44 columns (13 Numeric + 31 One-Hot) — ลบ `default` (target) ออก 1 column = **43 input features**

> **คำอธิบาย Architecture — ทำไมถึงเลือก Simple Architecture:**
> - **Dense(16, relu)**: Hidden Layer 1 — 16 neurons รับรู้ patterns จาก 43 features
> - **Dense(8, relu)**: Hidden Layer 2 — สรุป patterns ก่อน output
> - **Dense(1, sigmoid)**: Output Layer — ให้ค่า 0.0-1.0 (ความน่าจะเป็น Default)
>
> ⚠️ **เหตุผลที่ไม่ใช้ Dropout:** Dataset นี้มีเพียง 320 training rows — Dropout ทำให้โมเดล "ลืม" ข้อมูลมากเกินไป เหมาะสมกับ dataset ขนาดใหญ่ (10,000+ rows) เท่านั้น
> ⚠️ **เหตุผลที่ไม่ใช้ architecture ใหญ่ (64→32→16):** Overfitting risk สูงมากบน dataset เล็ก — โมเดลใหญ่ต้องการข้อมูลมากกว่านี้หลายเท่า

**Tab: Flow Variables** (ไม่ต้องตั้งค่า)

กด **OK** แล้ว **Execute**

> **หากเกิด Error:** ตรวจสอบว่าตั้งค่า Python Deep Learning environment แล้ว: `File → Preferences → KNIME → Python Deep Learning → เลือก TensorFlow 2 + Conda environment knime_python_env`

---

### Node 2: DL Python Network Learner

**ชื่อ Node:** DL Python Network Learner
**Node Path:** `Analytics → Integrations → Deep Learning → DL Python Network Learner`
**วัตถุประสงค์:** Train Neural Network ด้วย Python script — รองรับ TF2Network port type ที่สร้างโดย DL Python Network Creator

> **ทำไมไม่ใช้ Keras Network Learner?**
> **Keras Network Learner** คาดหวัง port type `DLKerasNetworkPortObjectSpecBase` แต่ **DL Python Network Creator** ส่งออก port type `TF2NetworkPortObjectSpec` — ทั้งสอง type ไม่ compatible กันใน KNIME 5.3 จึงต้องใช้ **DL Python Network Learner** ที่รับ TF2Network ได้โดยตรง

> **วิธีค้นหาโหนด:** พิมพ์ `DL Python` ในช่องค้นหา Node Repository

**การเชื่อมต่อ:**
- **Input port 1 (Network):** ต่อจาก DL Python Network Creator (Node 1)
- **Input port 2 (Table):** ต่อจาก **Partitioning Output 1** — Training Data 320 rows (จาก Lab 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Script**

ลบ code เดิมในช่อง Script ออกทั้งหมด แล้วพิมพ์โค้ดต่อไปนี้:

```python
import numpy as np

# ─── 1. กำหนด target column ──────────────────────────────────────────
target_col = 'default'

# ─── 2. แยก features กับ target จาก input_table ─────────────────────
feature_cols = [c for c in input_table.columns if c != target_col]
X_train = input_table[feature_cols].values.astype(np.float32)
Y_train = input_table[target_col].values.astype(np.float32)   # 1D array (ไม่ใช่ [[]])

# ─── 3. ดึง Keras model จาก input_network ────────────────────────────
# input_network คือ Keras Sequential model object โดยตรง
model = input_network

# ─── 4. Compile model ────────────────────────────────────────────────
model.compile(
    optimizer='adam',
    loss='binary_crossentropy',
    metrics=['accuracy']
)

# ─── 5. Train 200 epochs — ใช้ข้อมูลทุก row เต็มๆ ──────────────────
history = model.fit(
    X_train, Y_train,
    batch_size=16,    # batch เล็ก → gradient update บ่อยขึ้น (18 ครั้ง/epoch)
    epochs=200,       # epoch มาก → โมเดลเรียนรู้ได้นานขึ้น
    verbose=1
    # ไม่มี validation_split → ใช้ 320 rows ทั้งหมดสำหรับ train
    # ไม่มี class_weight  → สำหรับ dataset เล็ก class_weight อาจให้ผลตรงข้าม
)

# ─── 6. ส่ง trained model ออก ────────────────────────────────────────
output_network = input_network
```

> 💡 **คำอธิบายตัวแปร:**
> - `input_network` — Keras Sequential model ที่รับจาก DL Python Network Creator (Node 1)
> - `input_table` — pandas DataFrame ของ Training data 320 rows จาก Partitioning
> - `output_network` — trained model ที่จะส่งต่อไปยัง DL Python Network Executor
> - ต้องเขียน `output_network = input_network` เสมอ หรือ KNIME จะ error เพราะ output variable ไม่ถูก set

> ⚠️ **สำคัญ:** `model = input_network` (ไม่ใช่ `input_network.model`)
> ใน DL Python Network Learner `input_network` คือ Keras model โดยตรง ไม่ใช่ KNIME wrapper

> ⚠️ **Y_train ต้องเป็น 1D:** ใช้ `input_table[target_col]` (ไม่ใช่ `input_table[[target_col]]`)
> การใช้ double bracket `[[]]` ให้ 2D array shape (N,1) ซึ่งทำให้ `class_weight` ทำงานผิดพลาด

**Tab: Options** (ไม่ต้องแก้ไข — ใช้ค่า Default)

| ฟิลด์ | ค่า Default |
| ----- | ----------- |
| Row limit (dialog) | `1,000` |
| Missing values (Integer, Long) | ไม่ติ๊ก |
| Rows per chunk | `500,000` |

**Tab: Executable Selection**

| ฟิลด์ | ค่า |
| ----- | --- |
| ● Use KNIME Preferences | เลือกไว้ (ค่า Default) |

กด **OK** แล้ว **Execute**

> **ระหว่าง Execute:** KNIME Console จะแสดง training log ทุก epoch เช่น:
> ```
> Epoch 1/200 — loss: 0.6234 — accuracy: 0.6094
> Epoch 2/200 — loss: 0.5891 — accuracy: 0.6563
> ...
> Epoch 200/200 — loss: 0.4823 — accuracy: 0.7188
> ```
> Loss ควรค่อยๆ ลดลง accuracy ค่อยๆ เพิ่มขึ้น — ถ้า loss ไม่ลดเลยตลอด 200 epoch ให้ตรวจสอบว่า feature columns และ target column แยกถูกต้อง

**Output port:**
- Port 1: Trained TF2 Network → ต่อไปยัง DL Python Network Executor (Node 3)

---

### Node 3: DL Python Network Executor

**ชื่อ Node:** DL Python Network Executor
**Node Path:** `Analytics → Integrations → Deep Learning → DL Python Network Executor`
**วัตถุประสงค์:** ใช้ Model ที่ Train แล้วทำนายผลบน Test set ด้วย Python script

> **ทำไมไม่ใช้ Keras Network Executor?**
> เช่นเดียวกับ Learner — port type ไม่ compatible กัน ต้องใช้โหนดตระกูล **DL Python** ให้ตรงกันตลอด workflow

**การเชื่อมต่อ:**
- **Input port 1 (Network):** ต่อจาก DL Python Network Learner (Node 2)
- **Input port 2 (Table):** ต่อจาก **Partitioning Output 2** — Test Data 80 rows (จาก Lab 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Script**

ลบ code เดิมออก แล้วพิมพ์โค้ดต่อไปนี้:

```python
import numpy as np
import pandas as pd

# ─── 1. เตรียม Test features ─────────────────────────────────────────
# ตัด target column ออก — Test set ยังมี 'default' ติดมาด้วย
target_col = 'default'
feature_cols = [c for c in input_table.columns if c != target_col]
X_test = input_table[feature_cols].values.astype(np.float32)

# ─── 2. ทำนายผล ──────────────────────────────────────────────────────
model = input_network
predictions = model.predict(X_test, verbose=0)   # shape: (80, 1)

# ─── 3. สร้างตารางผลลัพธ์ ─────────────────────────────────────────────
output_table = input_table.copy()
output_table['DL_Prediction_0'] = predictions.flatten()
```

> 💡 `predictions.flatten()` แปลง array shape `(80, 1)` ให้เป็น `(80,)` เพื่อใส่ใน DataFrame column ได้ถูกต้อง

**Tab: Options** — ใช้ค่า Default ไม่ต้องแก้ไข

กด **OK** แล้ว **Execute**

**ผลลัพธ์:** ตาราง Test data **80 rows, 45 columns** — เพิ่ม column `DL_Prediction_0` (type: Number double) ต่อท้าย 44 columns เดิม
> 💡 ค่าใน `DL_Prediction_0` คือ sigmoid probability — อาจแสดงเป็น `0` หรือ `1` ในตาราง เพราะโมเดลมี confidence สูง (ค่าจริงคือ ~0.0001 หรือ ~0.9999) ไม่ใช่ class label — ขั้นตอน Node 4 Rule Engine จะแปลงเป็น class label อีกครั้ง

---

### Node 4: Rule Engine (แปลง probability → class label)

**ชื่อ Node:** Rule Engine
**Node Path:** `Manipulation → Column → Convert & Replace → Rule Engine`
**วัตถุประสงค์:** แปลง Output probability (0.0-1.0) จาก DL model เป็น Class label (0 หรือ 1) สำหรับ Scorer

> **หมายเหตุ:** ผลลัพธ์จาก DL Python Network Executor ออกมาเป็นตารางปกติอยู่แล้ว เพียงแปลงคอลัมน์ probability เป็น 0/1 ด้วย Rule Engine หรือ Math Formula

**การเชื่อมต่อ:**
- Input port: ต่อจาก DL Python Network Executor (Node 3)

**ตัวเลือก A: ใช้ Rule Engine** (แนะนำ)

Node Path: `Manipulation → Column → Convert & Replace → Rule Engine`

ในช่อง Expression พิมพ์ (ปรับชื่อ column ให้ตรงกับ output จริงของ Node 3):

```
$DL_Prediction_0$ >= 0.5 => 1
TRUE => 0
```

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Append Column | ✅ เลือก |
| New column name | `DL_Class_Prediction` |
| Output type | **Integer** |

**ตัวเลือก B: ใช้ Math Formula**

Node Path: `Manipulation → Column → Convert & Replace → Math Formula`

Formula: `floor($DL_Prediction_0$ + 0.5)`
Output column: `DL_Class_Prediction`

กด **OK** แล้ว **Execute**

---

### Node 5: Scorer

**ชื่อ Node:** Scorer
**Node Path:** `Analytics → Mining → Scoring → Scorer`
**วัตถุประสงค์:** ประเมิน Accuracy, Precision, Recall, F1 ของ Neural Network

**การเชื่อมต่อ:**
- Input port: ต่อจาก **Rule Engine (Node 4)** — ตารางที่มีทั้ง `default` (จริง) และ `DL_Class_Prediction` (ทำนาย)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Scorer**

| ฟิลด์ | ค่าที่ตั้ง | คำอธิบาย |
| ----- | --------- | --------- |
| **First column** | `default` | **ค่าจริง (Actual)** — Label ต้นฉบับจากข้อมูล (0 = ไม่ผิดนัด, 1 = ผิดนัด) |
| **Second column** | `DL_Class_Prediction` | **ค่าทำนาย (Predicted)** — ผลจาก Rule Engine ที่แปลง probability → 0/1 |
| Sorting strategy | **Insertion order** | เรียงตามลำดับที่พบในข้อมูล |

> ⚠️ **ข้อผิดพลาดที่พบบ่อย:** First Column และ Second Column **ต้องต่างกัน**
> - **First Column = `default`** (ค่าจริงจากข้อมูล)
> - **Second Column = `DL_Class_Prediction`** (ค่าที่โมเดลทำนาย)
> - ถ้าตั้งทั้งสอง column เป็น `DL_Class_Prediction` เหมือนกัน Scorer จะได้ Accuracy = 100% ซึ่งผิดพลาด

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

### Node 6: Number to String

**ชื่อ Node:** Number to String
**Node Path:** `Manipulation → Column → Convert & Replace → Number To String`
**วัตถุประสงค์:** แปลง `default` column จาก Integer → String เพื่อให้ ROC Curve node รับได้

> **ทำไมต้องมี Node นี้?**
> ROC Curve node ใน KNIME 5.3 Modern UI กำหนดให้ **Target column ต้องเป็น Nominal (String)** เท่านั้น
> `default` column เป็น Integer (0/1) → ไม่ขึ้นใน dropdown "Target column" → ต้องแปลงก่อน

**การเชื่อมต่อ:**
- Input port: ต่อจาก **DL Python Network Executor (Node 3)** โดยตรง
- Output port: → ต่อไปยัง **ROC Curve (Node 7)**

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

- ส่วน **Includes**: ตรวจสอบว่ามี **`default`** อยู่ใน Includes (ย้าย `default` เข้า Includes ถ้ายังไม่มี)
- ส่วน **Excludes**: columns อื่นทั้งหมดอยู่ที่นี่ (ไม่ต้องแปลง)

กด **Ok and Execute**

**ผลลัพธ์:** ตาราง 80 rows เหมือนเดิมทุกอย่าง แต่ `default` column เปลี่ยนจาก Integer (0, 1) → String ("0", "1")

---

### Node 7: ROC Curve

**ชื่อ Node:** ROC Curve
**Node Path:** `Analytics → Mining → Scoring → ROC Curve`
**วัตถุประสงค์:** วัด AUC (Area Under Curve) เพื่อประเมิน model discrimination

**การเชื่อมต่อ:**
- Input port: ต่อจาก **Number to String (Node 6)** — ตารางที่มี `default` เป็น String และ `DL_Prediction_0` เป็น Double

> ⚠️ **สำคัญ:** ต่อจาก **Node 6 (Number to String)** ไม่ใช่ Node 4 (Rule Engine)
> เพราะ ROC Curve ต้องการ probability (0.0–1.0) ใน column `DL_Prediction_0` ไม่ใช่ class label 0/1

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Step 1 — Target column:**
- คลิก dropdown → เลือก **`default`** (ตอนนี้เป็น String แล้ว จะขึ้นใน dropdown)

**Step 2 — Positive class value:**
- คลิก dropdown → เลือก **`1`** (String "1" = ผิดนัด)

**Step 3 — Prediction columns:**
1. ถ้ามี column อื่นใน **Includes** (เช่น `age`) → คลิก แล้วกดลูกศร **`<`** ย้ายออก
2. ใน **Excludes** หา `DL_Prediction_0` → คลิก แล้วกดลูกศร **`>`** ย้ายเข้า Includes
3. ตรวจสอบว่า **Includes มีเฉพาะ `DL_Prediction_0` เท่านั้น**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Target column | `default` (String) |
| Positive class value | `1` |
| Prediction columns (Includes) | `DL_Prediction_0` เท่านั้น |

กด **Ok and Execute**

**ดูผลลัพธ์:** คลิกขวา → **Open View** เพื่อดูกราฟ ROC พร้อมค่า AUC

**ผลลัพธ์ที่ได้จากการทดลอง:**
- AUC = **0.514** (แทบไม่ต่างจาก random classifier = 0.5)
- เส้น ROC แทบทับเส้นประ random classifier

> 💡 **ตีความ AUC = 0.514:**
> AUC 0.5 = random guessing — Neural Network บน dataset 320 rows ไม่สามารถเรียนรู้ pattern ได้ดีพอ
> นี่เป็นผลปกติสำหรับ Deep Learning บน dataset ขนาดเล็ก — ไม่ใช่ความผิดพลาด แต่เป็น **บทเรียนสำคัญ**

---

### ตารางเปรียบเทียบ ML vs Deep Learning

บันทึกผลจากทั้ง Day 2 และ Day 3 — **ผลจากการทดลองจริงในการอบรม:**

| โมเดล | Accuracy | Recall (Default) | F1 (Default) | AUC | ตีความได้ |
| ----- | -------- | ---------------- | ------------ | --- | --------- |
| Naive Bayes | ? | ? | ? | ? | ✅ สูง |
| Decision Tree | ? | ? | ? | ? | ✅✅ สูงมาก |
| k-NN (k=3) | ? | ? | ? | ? | ⚠️ ปานกลาง |
| SVM (RBF) | ? | ? | ? | ? | ❌ ต่ำ |
| **Neural Network** | **0.60** | **0.30** | **0.36** | **0.514** | ❌ ต่ำ |

> **Audit Insight — บทเรียนสำคัญจาก Lab 2:**
> Neural Network บน dataset 320 rows ให้ Accuracy **60%** และ AUC **0.514** (แทบ random) ซึ่งแย่กว่า Traditional ML ทุกตัว
> **เหตุผล:** Deep Learning ต้องการข้อมูลอย่างน้อย 10,000+ rows จึงจะแสดงประสิทธิภาพที่แท้จริง
> สำหรับงาน Internal Audit ที่ dataset มักมีหลัก 100–500 rows → **Decision Tree ยังเป็นตัวเลือกที่ดีกว่าในทุกมิติ** ทั้ง Accuracy, Explainability และ Speed

---

### สรุป Lab 2 — Deep Learning Classification

| Node | ชื่อ | หน้าที่ | หมายเหตุ |
| ---- | ---- | -------- | --------- |
| Lab1 | Lab1_Data_Preparation | Metanode: Training (320 rows) + Test (80 rows) normalized + Normalize Model | ต้อง exclude `default` จาก Normalizer |
| 1 | DL Python Network Creator | สร้าง Keras Sequential (16→8→1, ไม่มี Dropout) | ออก: TF2Network |
| 2 | DL Python Network Learner | Train model, Epoch=200, Batch=16, Adam, ไม่มี class_weight | รับ/ออก: TF2Network |
| 3 | DL Python Network Executor | ทำนายผลบน Test set → column `DL_Prediction_0` | รับ: TF2Network |
| 4 | Rule Engine | probability ≥ 0.5 → 1, else → 0 → column `DL_Class_Prediction` (Integer) | รับ: Table จาก Node 3 |
| 5 | Scorer | First=`default`, Second=`DL_Class_Prediction` → Accuracy=0.60, kappa=0.086 | รับ: Table จาก Node 4 |
| 6 | Number to String | แปลง `default`: Integer → String เพื่อให้ ROC Curve รับได้ | รับ: Table จาก Node 3 |
| 7 | ROC Curve | Target=`default`(str), Pos=`1`, Pred=`DL_Prediction_0` → **AUC=0.514** | รับ: Table จาก Node 6 |

> 💡 **หลักการเลือก node:** ใช้ **DL Python** ทั้ง 3 node (Creator / Learner / Executor) ให้ครบชุดเสมอ อย่าผสมกับ Keras Network Learner / Executor เพราะ port type ต่างกัน

> 💡 **ทำไม Number to String ถึงจำเป็น:** ROC Curve node ใน KNIME 5.3 Modern UI ต้องการ Target column เป็น Nominal (String) — ถ้า `default` ยังเป็น Integer จะไม่ขึ้น dropdown และ graph จะแสดงแค่เส้น random classifier

---

## Day3_Lab3_CNN_Image_Classification (Concept Lab — Demo)

**ชื่อ Lab:** CNN Image Classification — Concept Lab
**วัตถุประสงค์:** เข้าใจกระบวนการ CNN และการนำไปประยุกต์ใช้กับงาน Internal Audit ผ่านการอธิบายแนวคิดและ demo
**ตำแหน่งใน Schedule:** Section 4 — 13:00–14:00 น.
**รูปแบบ:** Concept Lab — วิทยากรอธิบายแนวคิดและสาธิต ผู้เรียนไม่ต้องลงมือทำตามทุกขั้นตอน

> **หมายเหตุสำคัญเรื่องรูปแบบ Lab นี้:**
> Lab 3 เป็น **Concept Lab** ที่เน้นความเข้าใจแนวคิด Convolutional Neural Network (CNN) — ไม่ใช่ hands-on workshop ที่ผู้เรียนต้องสร้าง workflow เอง เหตุผล:
> - การเทรน CNN จริงต้องมีชุดรูปภาพที่ติด label จำนวนมาก และต้องใช้ GPU จึงจะได้ผลในเวลาที่เหมาะกับการอบรม
> - การทำ CNN ใน KNIME อย่างเป็นทางการใช้แนวทาง `DL Python Network Editor` + `DL Python Network Learner` ทำงานกับ image patches ซึ่งซับซ้อนเกินกว่าขอบเขตของหลักสูตรเบื้องต้น
>
> วิทยากรจะอธิบายแนวคิด CNN พร้อมแผนภาพ และสาธิตด้วย example workflow จาก KNIME Hub (เช่น workflow "CNN for Image Classification of the MNIST Fashion Dataset") เพื่อให้ผู้เรียนเห็นภาพรวมว่า CNN ทำงานอย่างไรและนำไปใช้กับงาน Audit ได้อย่างไร

---

### แนวคิด CNN — Convolutional Neural Network คืออะไร

CNN เป็น Neural Network ประเภทหนึ่งที่ออกแบบมาเพื่องานด้าน **รูปภาพ** โดยเฉพาะ จุดเด่นคือมันเรียนรู้ "ลักษณะเด่นของภาพ" (features) ได้เองโดยอัตโนมัติ ไม่ต้องให้คนกำหนด — ต่างจาก Machine Learning แบบดั้งเดิมที่ต้องทำ Feature Engineering เอง

CNN ทำงานเป็นชั้น ๆ จากลักษณะง่ายไปซับซ้อน:

| ชั้น | หน้าที่ | เปรียบเทียบ |
| ---- | ------- | ----------- |
| **Convolution Layer** | กวาดหา pattern เล็ก ๆ ในภาพ เช่น เส้นขอบ มุม | เหมือนแว่นขยายที่เลื่อนดูทีละส่วน |
| **Pooling Layer** | ย่อขนาดภาพ เก็บเฉพาะข้อมูลสำคัญ | เหมือนการสรุปย่อ |
| **Convolution + Pooling ชั้นลึก** | ประกอบ pattern เล็กเป็น pattern ใหญ่ เช่น รูปทรง ตัวอักษร | เหมือนต่อจิ๊กซอว์ |
| **Flatten + Dense Layer** | นำ feature ทั้งหมดมาตัดสินใจว่าภาพคืออะไร | เหมือนสรุปคำตอบสุดท้าย |

---

### โครงสร้าง CNN ตัวอย่าง (Document Classification)

แนวคิดของ CNN สำหรับจำแนกประเภทเอกสาร 3 ชนิด — โค้ด Keras ด้านล่างใช้สำหรับ "อธิบายแนวคิด" ให้ผู้เรียนเห็นว่า architecture ของ CNN ประกอบด้วยอะไรบ้าง:

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
```

> **อธิบาย Architecture:**
> - **Conv2D(32)**: ดึง 32 pattern เบื้องต้น (edges, corners)
> - **MaxPooling2D(2,2)**: ลดขนาด feature map ครึ่งหนึ่ง
> - **Conv2D(64), Conv2D(128)**: ดึง patterns ที่ซับซ้อนขึ้นในแต่ละชั้น
> - **Flatten**: แปลง 2D → 1D เพื่อเข้า Dense layer
> - **Dense(3, softmax)**: Output 3 class ด้วย Softmax — ให้ความน่าจะเป็นของเอกสารแต่ละประเภท

---

### ขั้นตอนการทำ CNN ใน KNIME (สำหรับผู้สนใจศึกษาต่อ)

หากต้องการสร้าง CNN workflow จริงใน KNIME 5.3 แนวทางที่ถูกต้องเป็นดังนี้ — วิทยากรจะ demo ให้ดูจาก example workflow:

| ขั้นตอน | โหนดที่ใช้ | หน้าที่ |
| ------- | ---------- | ------- |
| 1. โหลดรูปภาพ | **Image Reader** (`KNIME Image Processing`) | อ่านไฟล์รูปภาพเข้าเป็น Table |
| 2. ปรับขนาด | **Image Resizer** | ปรับทุกรูปให้ขนาดเท่ากัน (เช่น 224×224) |
| 3. เตรียมข้อมูลภาพ | **Image Converter** / โหนดในกลุ่ม Image Processing | แปลงรูปให้อยู่ในรูปแบบที่ network รับได้ |
| 4. สร้าง + เทรน CNN | **DL Python Network Creator** + **DL Python Network Learner** | สร้างและเทรนโมเดล |
| 5. ทำนายผล | **DL Python Network Executor** | จำแนกประเภทรูปภาพ |
| 6. แปลงผลเป็นชื่อคลาส | **Rule Engine** | แปลงเลข class เป็นชื่อประเภทเอกสาร |

> **ข้อควรทราบ:** KNIME รุ่นใหม่ไม่มีโหนดชื่อ "Image to DL Tensor" — แนวทางที่ KNIME แนะนำสำหรับ CNN คือใช้กลุ่มโหนด DL Python ทำงานกับข้อมูลภาพโดยตรง ผู้สนใจสามารถศึกษา example workflow ได้จาก KNIME Hub โดยค้นคำว่า "CNN Image Classification" หรือ "Keras Image"

ตัวอย่างการแปลงเลข class กลับเป็นชื่อประเภทเอกสารด้วย Rule Engine:

```
$CNN_Prediction$ = 0 => "ใบเสร็จ (Receipt)"
$CNN_Prediction$ = 1 => "สัญญา (Contract)"
$CNN_Prediction$ = 2 => "หนังสือมอบอำนาจ (Authorization)"
TRUE => "Unknown"
```

---

### 🧪 CNN Feature Extraction Demo — Python Script ใน KNIME

> **วัตถุประสงค์:** ให้ผู้เรียนได้ทดลองรัน script ที่แสดงให้เห็นการทำงานของ CNN (Convolution + MaxPooling) ด้วย Python ใน KNIME โดยไม่ต้องใช้ Keras หรือชุดรูปภาพจริง

#### Workflow Overview

```
┌──────────────────────────────────────────────────────────┐
│  Day 3: Lab 3: Convolutional Neural Network (CNN)        │
│  Convolutional Neural Network (CNN)                      │
└──────────────────────────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────┐
│  Python Script for Test CNN    │  ← metanode label
│  สร้างภาพด้วยเทคนิค CNN        │
│                                │
│   ┌─────────────────────┐      │
│   │   Python Script     │ ──►  │  Output Table (7 rows)
│   └─────────────────────┘      │
└────────────────────────────────┘
```

| Node | ชื่อ | หน้าที่ |
|------|------|---------|
| Node 1 | **Python Script** | รัน CNN demo script — สร้าง synthetic image, apply filters, MaxPool, บันทึก PNG และ output summary table |

#### ขั้นตอนการตั้งค่า Python Script Node

1. ลากโหนด **Python Script** จาก Node Repository ลงใน workflow
2. ดับเบิลคลิกเพื่อเปิด dialog
3. คัดลอก script ด้านล่างวางในหน้าต่าง editor
4. กด **Run all** หรือ `Ctrl+Shift+F5`
5. ตรวจสอบ Output Table ที่มี 7 rows และ 5 columns
6. เปิดดูไฟล์ภาพผลลัพธ์จาก path ที่กำหนดใน script

#### Python Script — CNN Feature Extraction Demo

```python
import knime.scripting.io as knio  # Required by KNIME 5.3 Python Script node
import numpy as np
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
from scipy.ndimage import convolve
import pandas as pd
import io
import base64

# ─────────────────────────────────────────────
# 1. สร้าง Synthetic Document Image (28x28)
# ─────────────────────────────────────────────
np.random.seed(42)
image = np.zeros((28, 28), dtype=np.float32)

# วาดเส้นแนวนอน (จำลอง text lines)
for row in [5, 10, 15, 20]:
    image[row, 4:24] = 1.0

# วาดเส้นแนวตั้ง (จำลอง column divider)
image[3:23, 13] = 1.0

# เพิ่ม noise เล็กน้อย
noise = np.random.normal(0, 0.05, image.shape)
image = np.clip(image + noise, 0, 1)

# ─────────────────────────────────────────────
# 2. กำหนด CNN Filters (Kernels)
# ─────────────────────────────────────────────
edge_filter = np.array([
    [-1, -1, -1],
    [-1,  8, -1],
    [-1, -1, -1]
], dtype=np.float32)

horizontal_filter = np.array([
    [-1, -1, -1],
    [ 2,  2,  2],
    [-1, -1, -1]
], dtype=np.float32)

vertical_filter = np.array([
    [-1,  2, -1],
    [-1,  2, -1],
    [-1,  2, -1]
], dtype=np.float32)

blur_filter = np.ones((3, 3), dtype=np.float32) / 9.0

# ─────────────────────────────────────────────
# 3. Apply Convolution + MaxPooling
# ─────────────────────────────────────────────
def apply_conv(img, kernel):
    return np.clip(convolve(img, kernel), 0, 1)

def max_pool(feature_map, pool_size=2):
    h, w = feature_map.shape
    h_out, w_out = h // pool_size, w // pool_size
    pooled = np.zeros((h_out, w_out), dtype=np.float32)
    for i in range(h_out):
        for j in range(w_out):
            pooled[i, j] = feature_map[
                i*pool_size:(i+1)*pool_size,
                j*pool_size:(j+1)*pool_size
            ].max()
    return pooled

edge_map     = apply_conv(image, edge_filter)
horiz_map    = apply_conv(image, horizontal_filter)
vert_map     = apply_conv(image, vertical_filter)
blur_map     = apply_conv(image, blur_filter)
edge_pooled  = max_pool(edge_map)
horiz_pooled = max_pool(horiz_map)

# ─────────────────────────────────────────────
# 4. Visualize — สร้างภาพ 2×4 panel
# ─────────────────────────────────────────────
fig, axes = plt.subplots(2, 4, figsize=(14, 7))
fig.suptitle('CNN Feature Extraction Demo\n(Synthetic Document Image)',
             fontsize=14, fontweight='bold')

for ax, (title, data) in zip(axes[0], [
    ('Original Image', image),
    ('Edge Detection', edge_map),
    ('Horizontal Filter', horiz_map),
    ('Vertical Filter', vert_map),
]):
    ax.imshow(data, cmap='gray', vmin=0, vmax=1)
    ax.set_title(title, fontsize=9)
    ax.axis('off')

for ax, (title, data) in zip(axes[1][:3], [
    ('Blur Filter', blur_map),
    ('Edge After MaxPool', edge_pooled),
    ('Horiz After MaxPool', horiz_pooled),
]):
    ax.imshow(data, cmap='viridis', vmin=0, vmax=1)
    ax.set_title(title, fontsize=9)
    ax.axis('off')

axes[1][3].axis('off')
axes[1][3].text(0.05, 0.95,
    f"CNN Pipeline Summary\n─────────────────────\n"
    f"Input  : 28×28 = 784 px\n"
    f"Conv   : 3×3 kernel → 28×28\n"
    f"Pool   : 2×2 MaxPool → 14×14\n"
    f"Output : 14×14 = 196 features\n\n"
    f"Edge max  : {edge_map.max():.3f}\n"
    f"Edge mean : {edge_map.mean():.3f}\n"
    f"Pool max  : {edge_pooled.max():.3f}",
    transform=axes[1][3].transAxes, fontsize=8,
    verticalalignment='top', fontfamily='monospace',
    bbox=dict(boxstyle='round', facecolor='lightyellow', alpha=0.8))

plt.tight_layout()

# ─────────────────────────────────────────────
# 5. บันทึกภาพ PNG ลง disk
# ─────────────────────────────────────────────
output_path = r'C:\TrainingDocument\Machine Learning and Deep Learning with KNIME - Krungsri\cnn_demo_output.png'
plt.savefig(output_path, format='png', dpi=120, bbox_inches='tight')
plt.close()

# ─────────────────────────────────────────────
# 6. Output Table — KNIME ต้องการ output_tables[0]
# ─────────────────────────────────────────────
result_df = pd.DataFrame({
    'Step':       ['Input','Edge','Horizontal','Vertical','Blur','Pool-Edge','Pool-Horiz'],
    'Shape':      ['28×28','28×28','28×28','28×28','28×28','14×14','14×14'],
    'Max_Value':  [round(float(x.max()),4) for x in
                   [image,edge_map,horiz_map,vert_map,blur_map,edge_pooled,horiz_pooled]],
    'Mean_Value': [round(float(x.mean()),4) for x in
                   [image,edge_map,horiz_map,vert_map,blur_map,edge_pooled,horiz_pooled]],
})

knio.output_tables[0] = knio.Table.from_pandas(result_df)
```

#### Expected Output

**Output Table** — 7 rows × 4 columns:

| Step | Shape | Max_Value | Mean_Value |
|------|-------|-----------|------------|
| Input | 28×28 | 1.0 | ~0.137 |
| Edge | 28×28 | 1.0 | ~0.178 |
| Horizontal | 28×28 | 1.0 | ~0.150 |
| Vertical | 28×28 | 1.0 | ~0.097 |
| Blur | 28×28 | ~0.574 | ~0.137 |
| Pool-Edge | **14×14** | 1.0 | ~0.424 |
| Pool-Horiz | **14×14** | 1.0 | ~0.337 |

**ไฟล์ภาพ PNG** บันทึกที่:
```
C:\TrainingDocument\Machine Learning and Deep Learning with KNIME - Krungsri\cnn_demo_output.png
```

เปิดดูได้ทันทีจาก Windows Explorer — ภาพแสดง 2×4 panels: Original → Edge → Horizontal → Vertical (แถวบน) และ Blur → MaxPool Edge → MaxPool Horiz → CNN Pipeline Summary (แถวล่าง)

#### สิ่งที่ผู้เรียนควรสังเกตจากภาพ

| สิ่งที่เห็น | สิ่งที่เรียนรู้ |
|-------------|----------------|
| Horizontal Filter เห็นเฉพาะเส้นนอน, Vertical เห็นเฉพาะเส้นตั้ง | Filter แต่ละตัว "sensitive" ต่อ pattern คนละแบบ — คือ Feature Map |
| Blur Filter ทำให้ Max_Value ลดจาก 1.0 → 0.574 | Averaging kernel ลดความเข้มข้นของ pixel สูงสุด |
| MaxPool เปลี่ยน Shape จาก 28×28 → 14×14 | Pooling ลดขนาด 50% แต่ยังคง pattern หลักไว้ |
| Pool-Edge/Pool-Horiz มีสีเหลืองชัดเจน (viridis colormap) | MaxPool เก็บค่าสูงสุดในแต่ละ region — ตำแหน่ง feature ยังคงอยู่ |

#### ⚠️ ข้อควรทราบสำหรับ KNIME 5.3

- Python Script node (ใหม่) **ต้องมี** `import knime.scripting.io as knio` ทุก script
- Output ต้องกำหนดเป็น `knio.output_tables[0]` (พหูพจน์ + index `[0]`) ไม่ใช่ `knio.output_table_1`
- `matplotlib.use('Agg')` ต้องกำหนดก่อน `import matplotlib.pyplot` เสมอ เพราะ KNIME ไม่มี GUI display backend
- ไฟล์ PNG จะ overwrite ทุกครั้งที่ Run all — หากต้องการเก็บหลายเวอร์ชัน ให้เปลี่ยน filename

---

### Use Cases CNN สำหรับงาน Internal Audit

| Use Case | การนำไปใช้ |
| -------- | ---------- |
| **OCR เอกสาร** | อ่านใบเสร็จ, slip โอนเงิน, เอกสารสัญญาอัตโนมัติ |
| **Signature Verification** | ตรวจสอบลายเซ็นในเอกสารทางการ |
| **Stamp Detection** | ตรวจสอบตราประทับบนเอกสารว่าถูกต้องหรือไม่ |
| **Document Classification** | แยกประเภทเอกสาร audit โดยอัตโนมัติ |

> **Audit Insight:** สำหรับงานตรวจสอบที่ต้องประมวลผลเอกสารจำนวนมาก CNN ช่วยคัดแยกและจัดหมวดเอกสารเบื้องต้นได้รวดเร็ว ลดภาระงาน manual — แต่ผลลัพธ์ยังต้องผ่านการตรวจสอบโดยผู้เชี่ยวชาญ เพราะ CNN เป็น Black Box ที่อธิบายเหตุผลการตัดสินใจได้ยาก

---

## Day3_Lab4_LSTM_Time_Series_Prediction

**ชื่อ Lab:** LSTM สำหรับพยากรณ์ยอดธุรกรรมรายวัน
**Dataset:** `time_series_daily_transactions.csv` (730 วัน)
**วัตถุประสงค์:** สร้างโมเดล LSTM พยากรณ์ยอดธุรกรรมธนาคาร และตรวจจับวันที่มียอดผิดปกติ
**ตำแหน่งใน Schedule:** Section 5 — 14:15–15:00 น.

---

### Overview Workflow

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Day 3: Lab 4: LSTM Time Series Prediction                              │
│  LSTM สำหรับพยากรณ์ยอดธุรกรรมรายวัน                                    │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────────────┐   ┌───────────────────────────┐
│   Data Reading       │   │   String to Date&Time      │
│   อ่านข้อมูลจากไฟล์  │   │   แปลงคอลัมน์ Date จาก    │
│   time_series_daily_ │   │   String เป็นแบบ Date(Local)│
│   transactions.csv   │   └───────────────────────────┘
└──────────────────────┘
         │                          │
         ▼                          ▼
   [Node 1]               [Node 2]             [Node 3]
   CSV Reader    ──────►  String to Date&Time ──► Sorter
                                                   (by date ASC)
         │
         ▼
   [Node 4]               [Node 5]             [Node 6]
   Column Filter ──────►  Normalizer     ──────► Lag Column
   (keep all cols)        (Min-max [0,1])         (next_day_total_amount)
         │
         ▼
   [Node 7]               [Node 8]                    [Node 9]
   Missing Value ────────► Column Filter  ──────────►  Row Filter
   (Remove Row)            (เลือก Feature + lag cols)  (Train / Test split by date)

   [Node 10]                     [Node 11]                        [Node 12]
   DL Python Network Creator ──► DL Python Network Learner   ──► DL Python Network Executor
   (LSTM Architecture)            (Python script, Epochs=100)      (Python script)

   [Node 13]         [Node 14]
   Denormalizer  ──► Line Chart
                     (Actual vs Predicted)
```

| Node | ชื่อ Node | หน้าที่ |
|------|-----------|---------|
| Node 1 | **CSV Reader** | โหลด time_series_daily_transactions.csv |
| Node 2 | **String to Date&Time** | แปลง `date` String → Date type (yyyy-MM-dd) |
| Node 3 | **Sorter** | เรียงข้อมูลตาม `date` Ascending |
| Node 4 | **Column Filter** | คัดเลือก columns ที่ใช้ (date, day_of_week, is_weekend, month, quarter, is_month_end, …) |
| Node 5 | **Normalizer** | Normalize ทุก feature columns ด้วย Min-max [0, 1] |
| Node 6 | **Lag Column** | สร้าง lag feature จาก `next_day_total_amount` |
| Node 7 | **Missing Value** | ลบแถวที่มี Missing Value (Remove Row) → 731 → 729 rows |
| Node 8 | **Column Filter** | เลือกเฉพาะ Feature columns + lag column + date |
| Node 9 | **Row Filter** | แบ่ง Training / Test set ตามช่วงเวลา |
| Node 10 | **DL Python Network Creator** | สร้าง LSTM Architecture |
| Node 11 | **DL Python Network Learner** | เทรน LSTM model |
| Node 12 | **DL Python Network Executor** | ทำนายผลด้วย test set |
| Node 13 | **Denormalizer** | คืนค่ากลับเป็น THB จริง |
| Node 14 | **Line Chart** | เปรียบเทียบ Actual vs Predicted |

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

### Node 2: String to Date&Time

**ชื่อ Node:** String to Date&Time
**Node Path:** `Manipulation → Column → Convert & Replace → String to Date&Time`
**วัตถุประสงค์:** แปลง column `date` จาก String → Date type เพื่อให้ Sorter เรียงลำดับเวลาได้ถูกต้อง

**การเชื่อมต่อ:**
- Input port: ต่อจาก CSV Reader (Node 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Options**

**Column Selection:**

| ฝั่ง | Columns |
|------|---------|
| **Exclude** | `day_of_week` |
| **Include** ✅ | `date` |

> เลือกเฉพาะ `date` เข้า Include — `day_of_week` อยู่ Exclude เพราะเป็น String ที่ไม่ต้องแปลงเป็น Date

**Replace/Append Selection:**
- ● **Replace selected columns** (แทนที่ column `date` เดิมด้วย Date type ใหม่)

**Type and Format Selection:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| New type | `Date` |
| Date format | `yyyy-MM-dd` |
| Locale | `en-US` |
| Content of the first cell | `2023-01-01` (แสดง preview อัตโนมัติ) |

> กดปุ่ม **Guess data type and format** ได้เพื่อให้ KNIME ตรวจสอบ format จาก data จริงโดยอัตโนมัติ

**Abort Execution:**
- ✅ **Fail on error** (แนะนำให้เปิดไว้ — ถ้า format ไม่ตรงจะแจ้ง error ทันที)

กด **OK** แล้ว **Execute**

---

### Node 3: Sorter

**ชื่อ Node:** Sorter
**Node Path:** `Manipulation → Row → Transform → Sorter`
**วัตถุประสงค์:** เรียงข้อมูลตามวันที่จากเก่าไปใหม่ — สำคัญมากสำหรับ Time Series!

**การเชื่อมต่อ:**
- Input port: ต่อจาก String to Date&Time (Node 2)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog (KNIME 5.3 Modern UI):**

**Sorting panel:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Criterion 1 — Column | `date` |
| Order | **Ascending** (กด toggle ให้เลือก Ascending) |

> KNIME 5.3 Modern UI แสดง Sort criteria เป็น card ไม่ใช่ตารางแบบ classic — กดปุ่ม **⊕ Add sorting criterion** ถ้าต้องการเพิ่ม column เรียงลำดับที่ 2

กด **Ok and Execute**

> **ทำไมต้อง Sort?** Time Series ต้องการข้อมูลเรียงตามลำดับเวลา — การสร้าง Lag features ต้องอาศัยลำดับที่ถูกต้อง ถ้าข้อมูลไม่เรียง Lag Column จะสร้าง features ผิด window

---

### Node 4: Column Filter

**ชื่อ Node:** Column Filter
**Node Path:** `Manipulation → Column → Filter → Column Filter`
**วัตถุประสงค์:** คัดเลือก columns ที่จะใช้ใน pipeline — รวม time features ที่สร้างมาจาก date ด้วย

**การเชื่อมต่อ:**
- Input port: ต่อจาก Sorter (Node 3)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog (KNIME 5.3 Modern UI):**

**Column filter — Manual tab:**

| ฝั่ง | Columns |
|------|---------|
| **Excludes** | *(ไม่มีใน list — No columns in this list)* |
| **Includes** ✅ | `date`, `day_of_week`, `is_weekend`, `month`, `quarter`, `is_month_end` (และ columns อื่นที่ scroll ลงมา เช่น `total_txn_count`, `total_amount_thb`, `next_day_total_amount`) |

> Dataset `time_series_daily_transactions.csv` มี time feature columns ที่สร้างไว้ล่วงหน้าแล้ว ได้แก่ `day_of_week`, `is_weekend`, `month`, `quarter`, `is_month_end` — เก็บทั้งหมดไว้ใน Includes เพื่อใช้เป็น feature เพิ่มเติมใน LSTM

กด **Ok**

---

### Node 5: Normalizer

**ชื่อ Node:** Normalizer
**Node Path:** `Manipulation → Column → Transform → Normalizer`
**วัตถุประสงค์:** Normalize ทุก feature columns ให้อยู่ในช่วง [0, 1] ก่อนสร้าง Lag features — LSTM ต้องการข้อมูลใน scale เดียวกัน

**การเชื่อมต่อ:**
- Input port: ต่อจาก Column Filter (Node 4)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog (KNIME 5.3 Modern UI):**

**Column Selection — Manual tab:**

| ฝั่ง | Columns |
|------|---------|
| **Excludes** | *(ไม่มีใน list — No columns in this list)* |
| **Includes** ✅ | `is_weekend`, `month`, `quarter`, `is_month_end`, `total_txn_count`, `total_amount_thb` (และ columns numeric อื่น ๆ ที่ scroll ลงมา) |

> `date` และ `day_of_week` (String) จะไม่ปรากฏใน Includes โดยอัตโนมัติเพราะ Normalizer ทำงานกับ Numeric columns เท่านั้น

**Normalization method:**
- ● **Min-max** (เลือก toggle)
- Minimum: `0`
- Maximum: `1`

> **สำคัญมาก:** Port 2 ของ Normalizer คือ **Normalization Model** (สี่เหลี่ยมสีฟ้า ■) — ต้องต่อเข้า **Denormalizer** (Node 11) เพื่อคืนค่า THB จริงหลัง predict

กด **Ok**

---

### Node 6: Lag Column

**ชื่อ Node:** Lag Column
**Node Path:** `Manipulation → Row → Transform → Lag Column`
**วัตถุประสงค์:** สร้าง Lag feature จาก `next_day_total_amount` — เลื่อนข้อมูล 1 ขั้นเพื่อให้ model เรียนรู้ pattern ต่อเนื่องของ time series

**การเชื่อมต่อ:**
- Input port: ต่อจาก Normalizer (Node 5)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog (KNIME 5.3 Modern UI):**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| **Column to lag** | `next_day_total_amount` |
| **Number of copies** | `1` |
| **Lag per copy** | `1` |
| Drop incomplete rows at the top of the table | ☐ (ไม่เลือก) |
| Drop incomplete rows at the bottom of the table | ☐ (ไม่เลือก) |

> **ทำไมใช้ `next_day_total_amount` ไม่ใช่ `total_amount_thb`?**
> - `next_day_total_amount` คือ target ที่ต้องการทำนาย (ยอดธุรกรรมวันถัดไป)
> - Lag Column เลื่อน column นี้ลง 1 แถว → ทำให้แต่ละแถวรู้ว่า "วันนี้ต้องทำนายอะไร"
> - `Number of copies = 1` สร้าง lag column 1 ตัว: `next_day_total_amount(-1)`
> - แถวแรกจะมี Missing Value เพราะยังไม่มีข้อมูลก่อนหน้า — ต้องลบด้วย Row Filter ในขั้นต่อไป

กด **Ok**

---

### Node 7: Missing Value

**ชื่อ Node:** Missing Value
**Node Path:** `Manipulation → Column → Transform → Missing Value`
**วัตถุประสงค์:** ลบแถวที่มี Missing Value ออกก่อนเข้า LSTM — Lag Column สร้าง missing value ที่แถวแรกและแถวสุดท้าย

**การเชื่อมต่อ:**
- Input port: ต่อจาก Lag Column (Node 6)
- Output port 1 (Cleaned Table): ต่อไปยัง Column Filter (Node 8)
- Output port 2 (PMML Transformations): ไม่ต้องต่อ

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Default**

| Type | ค่าที่ตั้ง |
|------|----------|
| Local Date | **Remove Row** |
| String | **Remove Row** |
| Number (double) | **Remove Row** |

> เปลี่ยนทุก Type จาก "Do nothing" → **"Remove Row"** — จะลบแถวใดก็ตามที่มี missing value ใน column ใด column หนึ่ง

กด **OK**

**ผลลัพธ์หลัง Execute:** 731 rows → **729 rows** (ลบออก 2 แถว — แถวแรกและแถวสุดท้ายที่มี missing value จาก Lag Column)

---

### Node 8: Column Filter (เลือก Feature columns)

**ชื่อ Node:** Column Filter
**Node Path:** `Manipulation → Column → Filter → Column Filter`
**วัตถุประสงค์:** เลือกเฉพาะ columns ที่จำเป็นสำหรับ LSTM — feature columns + target + lag column

**การเชื่อมต่อ:**
- Input port: ต่อจาก Missing Value (Node 7)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog (KNIME 5.3 Modern UI):**

**Column filter — Manual tab:**

| ฝั่ง | Columns |
|------|---------|
| **Excludes** | `day_of_week`, `next_day_total_amount` |
| **Includes** ✅ | `date`, `is_weekend`, `month`, `quarter`, `is_month_end`, `total_txn_count`, `total_amount_thb`, `avg_txn_amount`, `atm_txn_count`, `online_txn_count`, `branch_txn_count`, `new_customers`, `fraud_count`, `interbank_rate`, `usd_thb_rate`, `next_day_total_amount(-1)` |

> **ทำไม Exclude เหล่านี้:**
> - `day_of_week` — เป็น **String** ("Monday"…) LSTM ต้องการ float ทั้งหมด — ถ้าไม่ Exclude จะเกิด error: `could not convert string to float: 'Monday'`
> - `next_day_total_amount` — เป็น target ต้นฉบับ **ก่อน** lag — ไม่ควรให้ model เห็น "คำตอบวันพรุ่งนี้" ตรง ๆ ขณะ training

> **ผลลัพธ์:** Includes มี **16 columns** (1 Local Date + 15 Numeric) — KNIME DL Python Network Learner จะใช้เฉพาะ **14 Numeric columns** เป็น features (ไม่รวม `date` และ `next_day_total_amount(-1)` ที่เป็น target) → ตรงกับ `input_shape=(14, 1)` ใน Node 10

กด **Ok**

---

### Node 9: Row Filter (แบ่ง Train/Test ตามเวลา)

**ชื่อ Node:** Row Filter
**Node Path:** `Manipulation → Row → Filter → Row Filter`
**วัตถุประสงค์:** แบ่ง Training / Test Set ตามช่วงเวลา

**การเชื่อมต่อ:**
- Input port: ต่อจาก Column Filter (Node 7)
- Output port 1 (Training Set): ต่อไปยัง DL Python Network Learner (Node 10)
- Output port 1 (Test Set): ต่อไปยัง DL Python Network Executor (Node 11)

> Lab 4 จำเป็นต้องสร้าง **2 Row Filter nodes แยกกัน** — Node 8a สำหรับ Training Set และ Node 8b สำหรับ Test Set ทั้งคู่รับ input จาก Node 7 เหมือนกัน

**การตั้งค่า — Dialog KNIME 5.3 Modern UI:**

> เมื่อเปิด Dialog ครั้งแรก หน้า Filter จะว่างเปล่า — ต้องกดปุ่มเพิ่ม criterion เอง

**Output section (ค่า default ที่ถูกต้องแล้ว):**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Column domains | **Retain** (เก็บ domain เดิม) |
| Filter behavior | **Output matching rows** (แสดงแถวที่ตรงเงื่อนไข) |

#### Node 9a: Row Filter — Training Set

1. กดปุ่ม **⊕ Add criterion**
2. ตั้งค่าเงื่อนไข:

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Filter column | `date` |
| Operator | **`Less than`** |
| Value | `2024-09-01` |

3. Output → Filter behavior: **Output matching rows** ✅ (default)

กด **Ok and Execute**

> **Operator ที่มีใน KNIME 5.3 สำหรับ date column:** Equals, Does not equal, Less than, Less than or equal, Greater than, Greater than or equal, Matches regex — ใช้ `Less than` แทน "before date"

#### Node 9b: Row Filter — Test Set

สร้าง Row Filter node ที่ 2 แยกต่างหาก — ตั้งค่าเหมือนข้างบน **แต่เปลี่ยน:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Filter column | `date` |
| Operator | **`Greater than or equal`** |
| Value | `2024-09-01` |

กด **Ok**

> **ทำไมไม่ใช้ Random Split?** Time Series ต้องแบ่งตาม **เวลา** เสมอ:
> - Training Set = ข้อมูลอดีต (ก่อน Sep 2024)
> - Test Set = ข้อมูลอนาคต (Sep 2024 เป็นต้นไป)
> - ถ้าใช้ Random split — model จะ "เห็นอนาคต" ระหว่าง training → ผล accuracy สูงเกินจริง (**Data Leakage!**)

---

### Node 10: DL Python Network Creator (LSTM)

**ชื่อ Node:** DL Python Network Creator
**Node Path:** `Analytics → Integrations → Deep Learning → DL Python Network Creator`
**วัตถุประสงค์:** สร้าง LSTM Architecture สำหรับ Time Series Prediction

**การตั้งค่า — Python Code:**

```python
from tensorflow import keras

# LSTM สำหรับ Time Series
# Input shape: (n_features=14, 1)
# n_features = จำนวน numeric columns จาก Column Filter (Node 8)
# ตรวจสอบได้จาก output ของ Column Filter → นับ numeric columns ที่ Include
model = keras.Sequential([
    keras.layers.LSTM(
        50,
        return_sequences=True,
        input_shape=(14, 1)   # ← 14 = จำนวน feature columns จริงในข้อมูล
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

> ⚠️ **ข้อควรระวัง:** ค่า `input_shape=(14, 1)` ต้องตรงกับจำนวน numeric columns ที่ Column Filter (Node 8) ส่งออกมาจริง ถ้าเพิ่มหรือลด column ใน Column Filter ต้องแก้ตัวเลขนี้ให้ตรงด้วยทุกครั้ง มิฉะนั้นจะเกิด error: `expected shape=(None, 14, 1), found shape=(batch, N, 1)`

กด **OK** แล้ว **Execute**

---

### Node 11: DL Python Network Learner (LSTM)

**ชื่อ Node:** DL Python Network Learner
**Node Path:** `Analytics → Integrations → Deep Learning → DL Python Network Learner`
**วัตถุประสงค์:** Train LSTM ด้วยข้อมูล Training set ผ่าน Python script

**การเชื่อมต่อ:**
- **Input port 1 (Network):** ต่อจาก DL Python Network Creator (Node 8)
- **Input port 2 (Table):** ต่อจาก Row Filter Training Set (Node 7a)

**Tab: Script** — ลบ code เดิมออก แล้วพิมพ์โค้ดต่อไปนี้:

```python
import numpy as np

# ─── 1. กำหนด target และ feature columns ─────────────────────────────
target_col = 'next_day_total_amount(-1)'   # lag column = สิ่งที่ต้องการทำนาย
exclude_cols = [target_col, 'date']        # ไม่รวม target และ date column

# feature_cols = ทุก column ยกเว้น target และ date → 14 numeric columns
feature_cols = [c for c in input_table.columns if c not in exclude_cols]

# ─── 2. เตรียม X_train, Y_train ──────────────────────────────────────
X_train = input_table[feature_cols].values.astype(np.float32)
Y_train = input_table[target_col].values.astype(np.float32)

# ─── 3. Reshape สำหรับ LSTM: (samples, timesteps, features) ──────────
# LSTM ต้องการ 3D input: (จำนวน rows, จำนวน timesteps, 1)
X_train = X_train.reshape(X_train.shape[0], X_train.shape[1], 1)

# ─── 4. ดึง model และ Train ──────────────────────────────────────────
model = input_network
model.compile(optimizer='adam', loss='mse')
model.fit(X_train, Y_train, epochs=50, batch_size=16, verbose=1)

output_network = input_network
```

> **คำอธิบาย feature / target selection:**
> - `target_col = 'next_day_total_amount(-1)'` — lag column ที่ Node 6 สร้างขึ้น คือยอดธุรกรรมวันถัดไปในรูป normalized [0,1]
> - `feature_cols` = 14 columns ได้แก่ `is_weekend, month, quarter, is_month_end, total_txn_count, total_amount_thb, avg_txn_amount, atm_txn_count, online_txn_count, branch_txn_count, new_customers, fraud_count, interbank_rate, usd_thb_rate` — ตรงกับ `input_shape=(14, 1)` ใน Node 10

> **ระหว่าง Train:** ใช้เวลาประมาณ 2-5 นาทีบน CPU สำหรับ 50 epochs — ดู loss log ใน KNIME Console ค่า loss ควรค่อยๆ ลดลงทุก epoch

กด **OK** แล้ว **Execute**

---

### Node 12: DL Python Network Executor (LSTM)

**ชื่อ Node:** DL Python Network Executor
**Node Path:** `Analytics → Integrations → Deep Learning → DL Python Network Executor`
**วัตถุประสงค์:** ใช้ LSTM ที่ Train แล้วพยากรณ์ยอดธุรกรรม Test set

**การเชื่อมต่อ:**
- **Input port 1 (Network):** ต่อจาก DL Python Network Learner (Node 9)
- **Input port 2 (Table):** ต่อจาก Row Filter Test Set (Node 7b)

**Tab: Script** — ลบ code เดิมออก แล้วพิมพ์โค้ดต่อไปนี้:

```python
import numpy as np

# ─── 1. กำหนด target และ feature columns (ต้องตรงกับ Node 11 Learner) ──
target_col = 'next_day_total_amount(-1)'
exclude_cols = [target_col, 'date']
feature_cols = [c for c in input_table.columns if c not in exclude_cols]

# ─── 2. เตรียม X_test จาก Test set ──────────────────────────────────
X_test = input_table[feature_cols].values.astype(np.float32)
X_test = X_test.reshape(X_test.shape[0], X_test.shape[1], 1)

# ─── 3. ทำนายผลด้วย trained LSTM model ──────────────────────────────
model = input_network
predictions = model.predict(X_test, verbose=0)

# ─── 4. สร้างตารางผลลัพธ์ ─────────────────────────────────────────────
# ตั้งชื่อ column ว่า 'next_day_total_amount' เพื่อให้ Denormalizer (Node 11)
# หาค่า Min/Max จาก Normalization Model ได้ถูกต้อง
# (ชื่อ 'next_day_total_amount' มีอยู่ใน Normalization Model จาก Node 5)
output_table = input_table.copy()
output_table['next_day_total_amount'] = predictions.flatten()
```

> ⚠️ **ทำไมต้องตั้งชื่อ column ว่า `next_day_total_amount`?**
> Denormalizer ค้นหา column ใน Normalization Model โดยใช้ **ชื่อ column ที่ตรงกัน** — ถ้าใช้ชื่อ `LSTM_Predicted_normalized` หรือชื่ออื่นที่ไม่มีในโมเดล Denormalizer จะไม่สามารถ scale กลับได้ และค่าจะยังคงเป็น normalized [0, 1] (เช่น ~0.001)
> `next_day_total_amount` เป็นชื่อ column ที่มีอยู่ใน Normalization Model ตั้งแต่ Node 5 Normalizer จึงรู้ค่า Min/Max ที่แท้จริง

กด **OK** แล้ว **Execute**

---

### Node 13: Denormalizer

**ชื่อ Node:** Denormalizer
**Node Path:** `Manipulation → Column → Transform → Denormalizer`
**วัตถุประสงค์:** คืนค่า Predicted ยอดธุรกรรมจาก [0,1] กลับเป็นค่า THB จริง

**การเชื่อมต่อ:**
- Input port 1 (Table): ต่อจาก DL Python Network Executor (Node 12)
- Input port 2 (Normalization Model): ต่อจาก **Normalizer (Node 5) → Port 2** (สีฟ้า ■)

> ⚠️ **Port 2 ของ Normalizer** คือ Normalization Model (สีฟ้า ■) ไม่ใช่ Port 1 (Data Table) — ถ้าต่อผิด port Denormalizer จะ error หรือไม่ทำงาน

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Options** — ตรวจสอบว่า `next_day_total_amount` อยู่ใน **Includes**

> **ทำไม column ชื่อ `next_day_total_amount`?**
> Node 12 Executor script ตั้งชื่อ prediction column ว่า `next_day_total_amount` โดยเฉพาะ เพื่อให้ตรงกับชื่อ column ที่ Normalizer (Node 5) รู้จักและมีค่า Min/Max บันทึกไว้ Denormalizer จึงสามารถ scale กลับจาก [0,1] → THB จริงได้ถูกต้อง

**Warning ที่อาจเห็น:** `"Normalization was not applied to N column(s)"` — เป็นเรื่องปกติ

กด **OK** แล้ว **Execute**

**ตรวจสอบผลลัพธ์:** column `next_day_total_amount` ควรแสดงค่าระดับ THB จริง (หลักล้าน–หลักร้อยล้าน) ไม่ใช่ค่า ~0.001

---

### Node 14: Line Chart

**ชื่อ Node:** Line Chart
**Node Path:** `Views → Line Chart`
**วัตถุประสงค์:** แสดงกราฟเปรียบเทียบ Actual vs Predicted ยอดธุรกรรมรายวัน

**การเชื่อมต่อ:**
- Input port: ต่อจาก Denormalizer (Node 13)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

| ฟิลด์ | ค่าที่ตั้ง |
| ----- | --------- |
| Horizontal axis column | `date` |
| Y columns | เพิ่ม `total_amount_thb` และ `next_day_total_amount` |
| Title | `LSTM Prediction: Actual vs Predicted Daily Transactions` |

กด **Ok and Execute** → คลิกขวา → **Open View**

---

### สรุป Lab 4 — LSTM Time Series Prediction

| Node | ชื่อ | หน้าที่ |
| ---- | ---- | -------- |
| 1 | CSV Reader | โหลด time_series_daily_transactions.csv |
| 2 | String to Date&Time | แปลง date String → Local Date |
| 3 | Sorter | เรียงตาม date Ascending |
| 4 | Column Filter | เลือก columns ที่ใช้ |
| 5 | Normalizer | Normalize [0,1] + บันทึก Normalization Model (Port 2) |
| 6 | Lag Column | สร้าง `next_day_total_amount(-1)` = target |
| 7 | Missing Value | Remove Row แถวที่มี missing value → 731→729 rows |
| 8 | Column Filter | Exclude: `day_of_week`, `next_day_total_amount` → 16 columns |
| 9a | Row Filter | Training Set: date < 2024-09-01 |
| 9b | Row Filter | Test Set: date >= 2024-09-01 → ~121 rows |
| 10 | DL Python Network Creator | LSTM(50)→Dropout→LSTM(25)→Dropout→Dense(1, sigmoid) |
| 11 | DL Python Network Learner | target=`next_day_total_amount(-1)`, Epoch=50, clipvalue=1.0 |
| 12 | DL Python Network Executor | ทำนายผล → column: `next_day_total_amount` |
| 13 | Denormalizer | คืนค่า normalized → THB จริง |
| 14 | Line Chart | Actual vs Predicted |

---

## Day3_Lab5_Unsupervised_Anomaly_Detection_Autoencoder

**ชื่อ Lab:** Unsupervised Anomaly Detection for Fraud Detection using Autoencoder
**Dataset:** `workshop_fraud_data.csv` — IEEE-CIS Fraud Detection (สุ่มตัวอย่าง 30,000 rows)
**วัตถุประสงค์:** สร้าง Autoencoder Neural Network ที่เรียนรู้ "pattern ธุรกรรมปกติ" โดยไม่ใช้ Label เพื่อตรวจจับธุรกรรมผิดปกติ (Fraud) จาก Reconstruction Error
**ตำแหน่งใน Schedule:** Section 5 — Workshop เพิ่มเติม (Bonus Lab)

---

### แนวคิด Autoencoder สำหรับ Anomaly Detection

```
ข้อมูลปกติ → [Encoder → Bottleneck → Decoder] → Reconstruction ≈ ข้อมูลเดิม  (Error ต่ำ)
ข้อมูล Fraud → [Encoder → Bottleneck → Decoder] → Reconstruction ≠ ข้อมูลเดิม  (Error สูง!)
```

**หลักการ:** Autoencoder เรียนรู้บีบอัด (encode) และขยาย (decode) ข้อมูล
- **Train บน isFraud=0 เท่านั้น** → โมเดลจดจำ pattern ธุรกรรมปกติ
- ธุรกรรม **Fraud** มี pattern แตกต่าง → โมเดล reconstruct ได้ไม่ดี → **Reconstruction Error สูง**
- ใช้ `isFraud` จริงตอนท้ายเพื่อ **ตรวจสอบ** ว่าโมเดลจับ Fraud ได้แม่นแค่ไหน

> 💡 **นี่คือ Unsupervised Learning จริงๆ** — ขั้นตอน Train ไม่ได้ใช้ข้อมูล `isFraud` เลย
> ใช้ Label เฉพาะตอน **ประเมินผล** เท่านั้น — เหมือนงาน Internal Audit ที่ตรวจสอบโดยไม่รู้คำตอบล่วงหน้า

---

### ข้อมูล Dataset: workshop_fraud_data.csv

| รายการ | ค่า |
|--------|-----|
| แหล่งที่มา | IEEE-CIS Fraud Detection (Kaggle) — ข้อมูล E-commerce จริงจาก Vesta Corporation |
| จำนวน rows | 30,000 |
| จำนวน columns | 25 |
| isFraud = 0 (ปกติ) | **28,950 rows (96.5%)** |
| isFraud = 1 (Fraud) | **1,050 rows (3.5%)** |
| ขนาดไฟล์ | 3.04 MB |

**Columns ทั้ง 25 ตัว:**

| # | Column | Type | Missing | คำอธิบาย |
|---|--------|------|---------|----------|
| 1 | `TransactionID` | Integer | 0% | รหัสธุรกรรม — **ตัดออก** |
| 2 | `isFraud` | Integer 0/1 | 0% | Label (ซ่อนระหว่าง Train) |
| 3 | `TransactionDT` | Integer | 0% | เวลา (วินาทีจาก reference) |
| 4 | `TransactionAmt` | Double | 0% | จำนวนเงิน \$0.88 – \$6,085 |
| 5 | `ProductCD` | String | 0% | ประเภทสินค้า: W, C, H, R, S |
| 6 | `card1` | Integer | 0% | รหัสบัตร group 1 |
| 7 | `card2` | Double | 1.5% | รหัสบัตร group 2 |
| 8 | `card3` | Double | 0.2% | รหัสบัตร group 3 |
| 9 | `card4` | String | 0.2% | ประเภทบัตร: visa, mastercard, ... |
| 10 | `card5` | Double | 0.7% | รหัสบัตร group 5 |
| 11 | `card6` | String | 0.2% | ประเภทการชำระ: debit, credit, ... |
| 12 | `addr1` | Double | 11.4% | รหัสที่อยู่ผู้ซื้อ |
| 13 | `addr2` | Double | 11.4% | รหัสที่อยู่ (ระดับประเทศ) |
| 14 | `P_emaildomain` | String | 16.0% | email domain ผู้ซื้อ — **ตัดออก** (59 unique, missing สูง) |
| 15 | `R_emaildomain` | String | **76.4%** | email domain ผู้รับ — **ตัดออก** (missing สูงเกินไป) |
| 16 | `C1`–`C5` | Integer | 0% | Count features (ธุรกรรมสะสม) |
| 21 | `D1` | Double | 0.2% | Timedelta feature (วันนับจากธุรกรรมก่อนหน้า) |
| 22 | `D2` | Double | 47.5% | Timedelta feature |
| 23 | `D3` | Double | 44.5% | Timedelta feature |
| 24 | `D4` | Double | 28.5% | Timedelta feature |
| 25 | `D15` | Double | 15.0% | Timedelta feature |

**หลังเตรียมข้อมูล (ใช้จริงใน Autoencoder):**
- ตัดออก: `TransactionID`, `R_emaildomain`, `P_emaildomain` → เหลือ 22 columns
- One-Hot Encoding: `ProductCD` (5) + `card4` (4) + `card6` (4) = +10 columns แทน 3 String columns
- Feature สุทธิ: **29 columns** (ไม่รวม `isFraud`)

---

### Overview Workflow

```
[CSV Reader]──→[Column Filter_1]──→[Missing Value]──→[One to Many]──→[Normalizer]
   (Node 1)        (Node 2)            (Node 3)          (Node 4)       (Node 5)
                 ตัด 3 columns      Impute missing     One-Hot Encode  Min-Max [0,1]
                                                                           │
                    ┌──────────────────────────────────────────────────────┤
                    │                                                      │
             [Row Filter]         (ทุก 30,000 rows → ใช้ตอน Evaluate)     │
              (Node 6)                                                     │
         กรอง isFraud=0                                                    │
         28,950 rows                                                       │
                    │                                                      │
         [Column Filter_2]                                                 │
              (Node 7)                                                     │
           ตัด isFraud ออก                                                 │
                    │                                                      │
     [DL Python Network Creator]                                           │
              (Node 8)                                                     │
         สร้าง Autoencoder                                                 │
                    │                                                      │
   [DL Python Network Learner]──────────────→[DL Python Network Executor]
              (Node 9)                                (Node 10)
           Train โมเดล                     รัน ทุก 30,000 rows
                                                       │
                                           [Python Script]
                                              (Node 11)
                                        คำนวณ Reconstruction Error (MSE)
                                                       │
                                           [Rule Engine]
                                              (Node 12)
                                        Flag: Anomaly / Normal
                                                       │
                                             [Joiner]
                                             (Node 13)
                                        รวมกับ isFraud จริง
                                                       │
                                             [Scorer]
                                             (Node 14)
                                        วัด Precision / Recall
```

---

### Node 1: CSV Reader

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**File:** `C:\KNIME_MASTER\datasets\Mockup Resources\workshop_fraud_data.csv`

**แท็บ Settings:** Autodetect format → ตรวจสอบ **30,000 rows, 25 columns**

กด **OK → Execute**

---

### Node 2: Column Filter (ตัด columns ไม่จำเป็น)

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Column Filter

**Panel Excludes (ซ้าย)** — ย้าย 3 columns นี้มา:
- `TransactionID` — รหัสธุรกรรม ไม่มี pattern ที่มีความหมาย
- `R_emaildomain` — Missing 76.4% ข้อมูลขาดหายมากเกินไป
- `P_emaildomain` — 59 unique values + 16% missing → ซับซ้อนเกินสำหรับ Workshop นี้

> ⚠️ **อย่าตัด `isFraud` ออกตรงนี้** — เก็บไว้ก่อนเพื่อใช้ filter training data ใน Node 6

**Panel Includes (ขวา):** เหลือ **22 columns**

กด **Ok and Execute**

**ตรวจสอบ:** 30,000 rows, 22 columns

---

### Node 3: Missing Value (อุดช่องว่าง)

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Missing Value`

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Missing Value

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog — มีแท็บ **Settings | Domain | Flow Variables**

**แท็บ Settings — Default method per type:**

| Type | วิธี | เหตุผล |
|------|------|--------|
| **Double** | `Mean` | ค่าเฉลี่ย — เหมาะสำหรับ numeric ที่มี missing น้อย |
| **Integer** | `Mean` | ใช้ค่าเฉลี่ย (ปัดเศษ) |
| **String** | `Most Frequent Value` | ใช้ category ที่พบบ่อยที่สุด |

**Column-specific settings** (ด้านล่าง) — คลิก **Add** แล้วตั้งค่าพิเศษ:

| Column | Method | หมายเหตุ |
|--------|--------|---------|
| `D2` | `Mean` | Missing 47.5% — ใช้ค่ากลาง |
| `D3` | `Mean` | Missing 44.5% |
| `D4` | `Mean` | Missing 28.5% |

> 💡 **Missing Value Imputation สำหรับ Autoencoder:** Neural Network ไม่สามารถรับค่า NaN ได้ ต้องอุดช่องว่างทุกตัว — แม้ว่า D2/D3 จะ missing เกือบ 50% ค่า Mean ก็ยังดีกว่าปล่อยว่างไว้

กด **Ok and Execute**

---

### Node 4: One to Many (One-Hot Encoding)

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → One to Many`

**เชื่อมต่อ:** Output ของ Missing Value → Input ของ One to Many

**แท็บ Columns to transform — Panel Include:**

| Column | Type | Categories | Columns ที่สร้าง |
|--------|------|-----------|-----------------|
| `ProductCD` | String | 5 | `C`, `H`, `R`, `S`, `W` |
| `card4` | String | 4 | `american express`, `discover`, `mastercard`, `visa` |
| `card6` | String | 4 | `charge card`, `credit`, `debit`, `debit or credit` |

☑ **Remove included columns from output** — ติ๊ก

กด **OK**

**ตรวจสอบ:** 30,000 rows, **29 columns** (22 - 3 String + 13 One-Hot) + `isFraud` = **30 columns รวม**

---

### Node 5: Normalizer (Min-Max Scaling)

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`

**เชื่อมต่อ:** Output ของ One to Many → Input ของ Normalizer

**Panel Excludes (ซ้าย):** ย้าย `isFraud` มาไว้ที่นี่ (ไม่ normalize Label)

**Panel Includes (ขวา):** columns ที่เหลือทั้งหมด 29 columns + **Any unknown column**

**Normalization method:** ● **Min-max** | Minimum: `0` | Maximum: `1`

กด **Ok and Execute**

> 💡 Normalizer ทำงานบน Training data (ปกติควรเป็นเฉพาะ isFraud=0) แต่ในที่นี้ normalize ทั้ง dataset ก่อนเพื่อความสะดวกในการสอน — ใน Production ควร fit Normalizer บน Training set เท่านั้น

**ตรวจสอบ:** 30,000 rows, 30 columns (29 features + `isFraud`)

---

### Node 6: Row Filter (คัดเลือก Training Data)

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Filter`

**เชื่อมต่อ:** Output ของ Normalizer → Input ของ Row Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Filter criteria:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column to test** | `isFraud` |
| **Operator** | `=` |
| **Value** | `0` |
| ● **Include rows by attribute value** | เลือก |

กด **Ok and Execute**

**ตรวจสอบ:** **28,950 rows** (เฉพาะ isFraud=0) — นี่คือ "ธุรกรรมปกติ" ที่ใช้ Train Autoencoder

---

### Node 7: Column Filter (ตัด isFraud ออกจาก Training Data)

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**เชื่อมต่อ:** Output ของ Row Filter → Input ของ Column Filter (Node ที่ 2)

**Panel Excludes (ซ้าย):** ย้าย `isFraud` มาที่นี่

**Panel Includes (ขวา):** **29 columns** (เฉพาะ features ไม่มี Label)

กด **Ok and Execute**

> 💡 **ทำไมต้องตัด isFraud ออก?**
> Autoencoder เรียนรู้ reconstruct INPUT กลับมา ถ้าปล่อย `isFraud=0` ไว้ทุก row โมเดลจะ "จำ" ว่า output ต้องเป็น 0 เสมอ ซึ่งไม่ใช่ pattern ที่ต้องการ

**ตรวจสอบ:** 28,950 rows, **29 columns** — พร้อมสำหรับ Train Autoencoder

---

### Node 8: DL Python Network Creator (สร้าง Autoencoder Architecture)

**ค้นหาใน Node Repository:** พิมพ์ `DL Python` ใน Node Repository

**เชื่อมต่อ:** ไม่มี Input (วางบน Canvas เชื่อมต่อ Output port → DL Python Network Learner)

**ดับเบิ้ลคลิก** เพื่อเปิด Python Script Editor:

```python
# Autoencoder สำหรับ Anomaly Detection — Fraud Detection
# Input: 29 features (ธุรกรรม E-commerce หลัง preprocessing)
# Architecture: 29 → 16 → 8 → [4 Bottleneck] → 8 → 16 → 29

from tensorflow.keras.layers import Input, Dense
from tensorflow.keras.models import Model

input_dim = 29  # จำนวน features หลังตัด isFraud ออก

input_layer = Input(shape=(input_dim,), name='input')

# ── Encoder: บีบอัด pattern ──
enc1 = Dense(16, activation='relu', name='encoder_1')(input_layer)
enc2 = Dense(8,  activation='relu', name='encoder_2')(enc1)
bottleneck = Dense(4, activation='relu', name='bottleneck')(enc2)

# ── Decoder: ขยายกลับ ──
dec1 = Dense(8,  activation='relu', name='decoder_1')(bottleneck)
dec2 = Dense(16, activation='relu', name='decoder_2')(dec1)
output_layer = Dense(input_dim, activation='sigmoid', name='output')(dec2)

# สร้าง Model
autoencoder = Model(input_layer, output_layer)

# ส่ง output ให้ KNIME
output_network = autoencoder
```

> 💡 **อธิบาย Architecture:**
> `29 → 16 → 8 → 4` = Encoder บีบอัดข้อมูล 29 มิติ เหลือเพียง 4 มิติ (Bottleneck)
> `4 → 8 → 16 → 29` = Decoder ขยายกลับมา 29 มิติ
> โมเดลต้องเรียนรู้จับ "สาระสำคัญ" ของธุรกรรมปกติให้ได้ใน 4 มิติ

กด **Execute**

---

### Node 9: DL Python Network Learner (Train Autoencoder)

**ค้นหาใน Node Repository:** พิมพ์ `DL Python` ใน Node Repository → เลือก **DL Python Network Learner**

**เชื่อมต่อ:**
- **Port 1 (Network):** Output ของ DL Python Network Creator (Node 8)
- **Port 2 (Table):** Output ของ Column Filter Node 7 (28,950 rows training data — ไม่มี isFraud)

**Tab: Script** — ลบ code เดิมออก แล้วพิมพ์โค้ดต่อไปนี้:

```python
import numpy as np

# ─── KEY: Autoencoder ใช้ Input = Target ──────────────────────────────
# โมเดลเรียนรู้ที่จะ reconstruct input กลับมา
# ข้อมูล training = 28,950 rows, 29 features (ทุก column)
X_train = input_table.values.astype(np.float32)
Y_train = X_train.copy()   # Target = Input เหมือนกันทุกประการ

# ─── Train ───────────────────────────────────────────────────────────
model = input_network
model.compile(optimizer='adam', loss='mse')

history = model.fit(
    X_train, Y_train,
    batch_size=256,
    epochs=50,
    verbose=1,
    validation_split=0.1
)

output_network = input_network
```

> ⚠️ **KEY: `Y_train = X_train.copy()`** — Autoencoder ใช้ input เป็น target เพื่อให้โมเดลเรียนรู้ pattern ของธุรกรรม "ปกติ" ธุรกรรม Fraud จะ reconstruct ได้ไม่ดีเพราะมี pattern ต่างออกไป

กด **OK** แล้ว **Execute** — รอ ~2–4 นาที

**ตรวจสอบผล:** Loss ควรลดลงต่อเนื่อง — ถ้า validation loss สูงขึ้นแต่ training loss ลด = Overfitting

---

### Node 10: DL Python Network Executor (Reconstruct ทุก 30,000 rows)

**ค้นหาใน Node Repository:** พิมพ์ `DL Python` ใน Node Repository → เลือก **DL Python Network Executor**

**เชื่อมต่อ:**
- **Port 1 (Network):** Output ของ DL Python Network Learner (Node 9)
- **Port 2 (Table):** Output ของ Normalizer (Node 5) **ผ่าน Column Filter (Node 10a)** ที่ตัด `isFraud` ออก

> ⚠️ **ต้องเพิ่ม Column Filter (Node 10a)** ระหว่าง Normalizer และ DL Python Network Executor:
> - ต่อ Output ของ Normalizer (Node 5) เข้า Column Filter ใหม่ (Node 10a)
> - ใน Column Filter: ย้าย `isFraud` ไปที่ Excludes
> - Output (29 features, 30,000 rows) → DL Python Network Executor

**Tab: Script** — ลบ code เดิมออก แล้วพิมพ์โค้ดต่อไปนี้:

```python
import numpy as np
import pandas as pd

# ─── Reconstruct ทุก 30,000 rows ────────────────────────────────────
X_all = input_table.values.astype(np.float32)

model = input_network
X_reconstructed = model.predict(X_all, verbose=0)

# ─── สร้างตารางผลลัพธ์ ────────────────────────────────────────────────
# เพิ่ม reconstructed columns ต่อท้าย original columns
recon_cols = [c + '_recon' for c in input_table.columns]
df_recon = pd.DataFrame(X_reconstructed, columns=recon_cols,
                        index=input_table.index)

output_table = pd.concat([input_table, df_recon], axis=1)
```

กด **OK** แล้ว **Execute**

**ผลลัพธ์:** ตาราง 30,000 rows ที่มีทั้ง:
- 29 columns ต้นฉบับ (ค่า normalized เดิม เช่น `V1`, `V2`, ...)
- 29 columns ที่ reconstruct (ชื่อ `V1_recon`, `V2_recon`, ...)

---

### Node 11: Python Script — คำนวณ Reconstruction Error

**ค้นหาใน Node Repository:** พิมพ์ `Python Script` ใน Node Repository → เลือก **Python Script (1⇒1)**

**เชื่อมต่อ:** Output ของ DL Python Network Executor → Input ของ Python Script

**ดับเบิ้ลคลิก** เพื่อเปิด Python Script Editor:

```python
import numpy as np
import pandas as pd

# รับข้อมูลจาก KNIME
df = input_table.copy()

# แยก columns input (ค่าเดิม) กับ output (ค่าที่ reconstruct)
# DL Python Network Executor เพิ่ม columns ชื่อ V1_recon, V2_recon, ...
all_cols = list(df.columns)

# Input columns = 29 features เดิม (ไม่มี _recon suffix)
# Output columns = columns ที่ลงท้ายด้วย _recon
input_cols  = [c for c in all_cols if not c.endswith('_recon')]
output_cols = [c for c in all_cols if c.endswith('_recon')]

# คำนวณ Reconstruction Error (MSE) ต่อ row
input_vals  = df[input_cols].values.astype(float)
output_vals = df[output_cols].values.astype(float)

mse_per_row = np.mean((input_vals - output_vals) ** 2, axis=1)

# เพิ่มคอลัมน์ reconstruction_error
df['reconstruction_error'] = mse_per_row

output_table = df
```

กด **Execute**

> 💡 **Reconstruction Error = MSE ต่อ row** = ค่าเฉลี่ยของ (input - output)² ทุก feature
> ธุรกรรม Fraud คาดว่าจะมีค่านี้สูงกว่าธุรกรรมปกติ

---

### Node 12: Rule Engine — กำหนด Threshold และ Flag Anomaly

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → Rule Engine`

**เชื่อมต่อ:** Output ของ Python Script → Input ของ Rule Engine

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**กำหนด Threshold:** ก่อนตั้งค่า Rule Engine ให้ดูค่า Reconstruction Error ก่อน:
- คลิกขวาที่ Python Script node → **Interactive View** หรือ **Statistics**
- ดูค่า mean และ std ของ `reconstruction_error`
- **Threshold แนะนำ = mean + 2 × std** (ครอบคลุม 97.7% ของข้อมูลปกติ)
- หรือเริ่มต้นจาก threshold = `0.01` แล้วปรับตามผล

**กฎที่ต้องใส่ใน Rule Engine:**

```
$reconstruction_error$ > 0.01 => "Fraud"
TRUE => "Normal"
```

> 📝 **ปรับค่า 0.01 ตามที่สังเกตจากข้อมูลจริง** — ค่านี้เป็นเพียงจุดเริ่มต้น

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Append Column** | ✅ เลือก |
| **Column name** | `predicted_fraud` |

กด **Ok and Execute**

---

### Node 13: Joiner — รวมกับ isFraud จริง

**ค้นหาใน Node Repository:** `Manipulation → Column → Join → Joiner`

**เชื่อมต่อ:**
- **Port บน (Left):** Output ของ Rule Engine (30,000 rows + reconstruction_error + predicted_fraud)
- **Port ล่าง (Right):** Output ของ Normalizer Node 5 (ที่มีทั้ง features + `isFraud`)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog — แท็บ **Joiner settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Join column(s)** | ไม่ต้องตั้ง Row Key join (ใช้ Row index อัตโนมัติ) |
| **Join mode** | `Inner Join` |

**แท็บ Column Selection:**
- Left table: เลือก `reconstruction_error`, `predicted_fraud`
- Right table: เลือก `isFraud`

> 💡 หรืออาจใช้วิธีง่ายกว่า: ใน Python Script (Node 11) ให้เก็บ `isFraud` ไว้ตั้งแต่ต้นโดยผ่านข้อมูล Normalizer ที่ยังมี `isFraud` เข้า Python Script โดยตรง แล้วไม่ต้องใช้ Joiner

กด **Ok and Execute**

---

### Node 14: Scorer — ประเมินผล Anomaly Detection

**ค้นหาใน Node Repository:** `Analytics → Mining → Scoring → Scorer`

**เชื่อมต่อ:** Output ของ Joiner → Input ของ Scorer

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **First column** | `predicted_fraud` ("Fraud" / "Normal") |
| **Second column** | `isFraud` (0 / 1) |

> ⚠️ **หมายเหตุ:** Scorer ต้องการ type เดียวกัน — ถ้า `isFraud` เป็น Integer (0/1) แต่ `predicted_fraud` เป็น String ให้เพิ่ม Rule Engine แปลง `isFraud` เป็น "1" / "0" ก่อน หรือใช้ **Node: Number To String** แปลงก่อน Scorer

กด **Ok and Execute**

---

### ผลลัพธ์ที่คาดหวัง

**Confusion Matrix:**

```
                 ทำนาย: Normal   ทำนาย: Fraud
จริง: Normal (0)   ~27,000+        ~1,000-
จริง: Fraud  (1)     ~600-           ~450+
```

**Metrics ที่ควรได้:**

| Metric | ค่าเป้าหมาย | ความหมาย |
|--------|------------|---------|
| **Recall (Sensitivity)** | > 40% | โมเดลจับ Fraud จริงได้กี่ % |
| **Precision** | > 30% | ในสิ่งที่โมเดลบอกว่า Fraud มีจริงกี่ % |
| **False Positive Rate** | < 5% | ธุรกรรมปกติที่ถูก flag ผิดพลาด |

> 💡 **Recall สำคัญกว่า Precision** สำหรับงาน Fraud Detection — การ "ตกหล่น" Fraud จริง (False Negative) มีผลเสียรุนแรงกว่าการ "แจ้งเตือนผิด" (False Positive)

---

### การปรับปรุงโมเดลเพื่อผลดีขึ้น

**ทดลองปรับค่าต่อไปนี้แล้วเปรียบเทียบผล:**

| พารามิเตอร์ | ค่าเริ่มต้น | ลองปรับเป็น | ผลที่คาดหวัง |
|------------|-----------|-----------|------------|
| Epochs | 50 | 100 | Loss ลดลงต่อเนื่อง |
| Bottleneck size | 4 | 2 หรือ 6 | ค่าต่ำ = detect ได้ดีกว่า (บีบแน่นกว่า) |
| Threshold | 0.01 | ปรับตาม mean+2std | Recall สูงขึ้น แต่ Precision ลด |
| Training data | isFraud=0 เท่านั้น | ทั้งหมด (30,000 rows) | เปรียบเทียบว่าต่างกันแค่ไหน |

---

### สรุป Lab 5 — Autoencoder Anomaly Detection

| Node | ชื่อ | หน้าที่ |
|------|------|--------|
| 1 | CSV Reader | โหลด workshop_fraud_data.csv (30,000 rows, 25 cols) |
| 2 | Column Filter | ตัด TransactionID, R_emaildomain, P_emaildomain |
| 3 | Missing Value | Impute Mean สำหรับ numeric, Mode สำหรับ String |
| 4 | One to Many | One-Hot: ProductCD + card4 + card6 (→ 13 One-Hot cols) |
| 5 | Normalizer | Min-Max [0,1] ทุก feature ยกเว้น isFraud |
| 6 | Row Filter | กรองเฉพาะ isFraud=0 → Training (28,950 rows) |
| 7 | Column Filter | ตัด isFraud ออกจาก Training data |
| 8 | DL Python Network Creator | สร้าง Autoencoder: 29→16→8→4→8→16→29 |
| 9 | DL Python Network Learner | Train: Loss=MSE, Adam, Epoch=50, Batch=256 (Python script) |
| 10a | Column Filter | ตัด isFraud จาก full dataset สำหรับ Executor |
| 10 | DL Python Network Executor | Reconstruct ทั้ง 30,000 rows (Python script) |
| 11 | Python Script | คำนวณ Reconstruction Error (MSE per row) |
| 12 | Rule Engine | Flag: reconstruction_error > threshold → "Fraud" |
| 13 | Joiner | รวมผลทำนายกับ isFraud จริง |
| 14 | Scorer | วัด Precision / Recall / Confusion Matrix |

---

### สิ่งที่ค้นพบจาก Lab นี้

> **ข้อสรุปสำคัญสำหรับกลุ่มงานตรวจสอบภายใน:**
>
> 1. **Deep Learning สามารถตรวจจับความผิดปกติได้โดยไม่ต้องมีเฉลย** — Autoencoder เรียนรู้จาก "ความปกติ" แล้วตรวจจับสิ่งที่แตกต่าง
> 2. **Reconstruction Error คือ "คะแนนความผิดปกติ"** — ยิ่งสูง ยิ่งน่าสงสัย — ผู้ตรวจสอบสามารถ sort รายการตาม error สูงสุดและตรวจสอบเพิ่มเติม
> 3. **Threshold ปรับได้ตามทรัพยากร** — ถ้าทีมตรวจสอบมีเวลาน้อย ตั้ง threshold สูงขึ้น เพื่อเน้นเฉพาะรายการที่น่าสงสัยที่สุด
> 4. **ประยุกต์กับงาน Audit:** ธุรกรรมที่ Reconstruction Error สูงผิดปกติ = รายการที่ควรนำมาตรวจสอบเชิงลึกก่อน

---

## Day3_Lab6_KNIME_with_GenAI (Bonus)

**ชื่อ Lab:** KNIME กับการเชื่อมต่อ Generative AI
**วัตถุประสงค์:** แสดงให้เห็นว่า KNIME สามารถเชื่อมต่อกับ OpenAI API เพื่อส่ง Prompt และรับ Response โดยตรงภายใน KNIME Workflow โดยไม่ต้องเขียนโค้ดเพิ่มเติม
**ตำแหน่งใน Schedule:** Bonus Lab — สาธิตหลังจาก Lab 5

> 💡 **ประโยชน์สำหรับงาน Audit:** สามารถใช้ LLM ช่วย summarize ผลการตรวจสอบ, แปล error message, หรือสร้าง narrative report จากตารางข้อมูลได้โดยตรงใน KNIME Workflow

---

### โครงสร้าง Workflow

```
┌────────────────────────────────────────────────────────────────┐
│  Authenticate             Select              Prompt           │
│                                                                │
│  Credentials Config  →  OpenAI Auth     Table Creator         │
│  (OpenAI API Key)                    ┐   (Prompt column)  ──┐ │
│                         Chat Model ──┤                       │ │
│                         Connector    │   LLM Prompter ←──────┘ │
│                                      │   (Response column)     │
│                         LLM       ───┘                         │
│                         Connector                              │
└────────────────────────────────────────────────────────────────┘
```

| Node | ชื่อ | หน้าที่ |
|------|------|---------|
| 1 | Credentials Configuration | เก็บ OpenAI API Key อย่างปลอดภัย |
| 2 | OpenAI Authenticator | ยืนยัน API Key กับ OpenAI |
| 3 | OpenAI Chat Model Connector | เลือก Chat model (gpt-4o-mini) |
| 4 | OpenAI LLM Connector | เลือก LLM model (gpt-3.5-turbo-instruct) |
| 5 | LLM Prompter | ส่ง Prompt → รับ Response จาก LLM |
| 6 | Table Creator | สร้างตาราง Prompt ที่ต้องการส่ง |

---

### Extensions ที่ต้องติดตั้ง

ตรวจสอบผ่าน **Menu → Install Extensions** ค้นหา:

| Extension | รายละเอียด |
|-----------|-----------|
| **KNIME AI Extension** | ครอบคลุม nodes: OpenAI Authenticator, Chat Model Connector, LLM Connector, LLM Prompter |

---

### Node 1: Credentials Configuration (OpenAI API Key)

**ชื่อ Node:** Credentials Configuration
**Node Path:** `Workflow Control → Configuration → Credentials Configuration`
**วัตถุประสงค์:** เก็บ OpenAI API Key อย่างปลอดภัยเป็น Credential variable ภายใน Workflow

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

**Tab: Control**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Label | `OpenAI API Key` |
| Parameter/Variable Name | `credentials` |
| Username | (ว่างไว้ ไม่ต้องใส่) |
| **Password** | **ใส่ OpenAI API Key ของคุณที่นี่** (รูปแบบ `sk-proj-...`) |
| Save password in configuration | ☑ เลือก (weakly encrypted) |

> ⚠️ **สำคัญ:** OpenAI API Key ต้องใส่ในช่อง **Password** — ไม่ใช่ Username
> สร้าง API Key ได้ที่ https://platform.openai.com/api-keys

กด **OK**

---

### Node 2: OpenAI Authenticator (Auth OpenAI)

**ชื่อ Node:** OpenAI Authenticator
**Node Path:** `AI → LLM → OpenAI → OpenAI Authenticator`
**วัตถุประสงค์:** รับ Credential จาก Node 1 แล้วยืนยันตัวตนกับ OpenAI API

**การเชื่อมต่อ:**
- Input port (Credentials): ต่อจาก Credentials Configuration (Node 1)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| **OpenAI API key** | `credentials` (เลือกจาก dropdown) |

กด **Ok**

---

### Node 3: OpenAI Chat Model Connector

**ชื่อ Node:** OpenAI Chat Model Connector
**Node Path:** `AI → LLM → OpenAI → OpenAI Chat Model Connector`
**วัตถุประสงค์:** เลือก Chat model สำหรับ Conversation format (system/user/assistant)

**การเชื่อมต่อ:**
- Input port (Authentication): ต่อจาก OpenAI Authenticator (Node 2)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Model selection | **Default models** |
| **Model ID** | **`gpt-4o-mini`** |
| Maximum response length (token) | `800` |
| Temperature | `0.2` |

> **Temperature 0.2** = คำตอบ deterministic สูง เหมาะกับงาน Audit ที่ต้องการความแม่นยำ
> **Temperature 1.0** = คำตอบ creative เหมาะกับงาน content writing

กด **Ok**

---

### Node 4: OpenAI LLM Connector

**ชื่อ Node:** OpenAI LLM Connector
**Node Path:** `AI → LLM → OpenAI → OpenAI LLM Connector`
**วัตถุประสงค์:** เลือก LLM model สำหรับ Text Completion format (single-turn)

**การเชื่อมต่อ:**
- Input port (Authentication): ต่อจาก OpenAI Authenticator (Node 2)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| Model selection | **Default models** |
| **Model ID** | **`gpt-3.5-turbo-instruct`** |
| Maximum response length (token) | `200` |
| Temperature | `0.2` |

> **ความแตกต่าง Chat Model vs LLM Connector:**
> - **Chat Model Connector** → gpt-4o-mini — รองรับ system prompt + multi-turn conversation
> - **LLM Connector** → gpt-3.5-turbo-instruct — single-turn text completion เท่านั้น
>
> ใน workflow นี้เชื่อมต่อทั้งสองไว้เพื่อสาธิตความแตกต่าง — **LLM Prompter ใช้ output จาก Chat Model Connector**

กด **Ok and Execute**

---

### Node 5: LLM Prompter

**ชื่อ Node:** LLM Prompter
**Node Path:** `AI → LLM → LLM Prompter`
**วัตถุประสงค์:** รับ column ที่มี Prompt text ส่งไปยัง LLM ทีละแถว รับ Response กลับมาเป็น column ใหม่

**การเชื่อมต่อ:**
- **Input port 1 (LLM):** ต่อจาก OpenAI Chat Model Connector (Node 3)
- **Input port 2 (Table):** ต่อจาก Table Creator (Node 6)

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

| ฟิลด์ | ค่าที่ตั้ง |
|-------|----------|
| **Prompt column** | `Prompt` (ชื่อ column จาก Table Creator) |
| **Response column name** | `Response` |

กด **Ok**

> **หมายเหตุ:** LLM Prompter จะส่ง API request 1 ครั้งต่อ 1 แถว — ถ้า Table Creator มี 5 rows จะมี 5 API calls → คำนึงถึง cost และ rate limit ของ API

---

### Node 6: Table Creator (Prompt Input)

**ชื่อ Node:** Table Creator
**Node Path:** `IO → Other → Table Creator`
**วัตถุประสงค์:** สร้างตาราง Prompt ที่ต้องการส่งให้ LLM — แต่ละแถวคือ 1 Prompt

**การตั้งค่า — ดับเบิลคลิก Node เพื่อเปิด Dialog:**

1. สร้าง column ใหม่ → ตั้งชื่อ **`Prompt`** (ประเภท String)
2. ใส่ Prompt ใน Row0:

```
How do I install KNIME Analytics Platform?
```

> **ตัวอย่าง Prompt สำหรับงาน Internal Audit:**
> ```
> Summarize the following transaction anomaly in 2 sentences for an audit report: 
> Date 2024-11-15, Amount 8,500,000 THB, Z-score 4.2, flagged as unusual.
> ```

กด **OK**

---

### ผลลัพธ์ที่ได้

หลัง Execute ทั้ง Workflow — Output table จาก LLM Prompter มี 2 columns:

| Column | ตัวอย่างข้อมูล |
|--------|--------------|
| `Prompt` | `How do I install KNIME Analytics Platform?` |
| `Response` | `To install KNIME Analytics Platform, visit knime.com/downloads, select your OS, download the installer, and follow the installation wizard...` |

---

### การประยุกต์ใช้ในงาน Internal Audit

| Use Case | ตัวอย่าง Prompt |
|----------|---------------|
| **Summarize anomaly** | `"วันที่ 2024-11-15 มียอดธุรกรรม 8.5 ล้านบาท สูงกว่าค่าเฉลี่ย 4.2 เท่า สรุปความเสี่ยงสำหรับ Audit Report ใน 2 ประโยค"` |
| **Explain ML result** | `"LSTM ทำนาย next day = 45 ล้านบาท แต่ actual = 92 ล้านบาท อธิบายสาเหตุที่เป็นไปได้"` |
| **Generate report** | `"จาก fraud_summary นี้ เขียน Executive Summary สำหรับผู้บริหาร ความยาว 1 ย่อหน้า"` |
| **Translate finding** | `"แปลผลการตรวจสอบต่อไปนี้เป็นภาษาอังกฤษสำหรับ International Audit Report"` |

> 💡 **เทคนิค Dynamic Prompt:** ใช้ **String Manipulation** node ประกอบ Prompt แบบ dynamic จากข้อมูลในตาราง เช่น รวม `date`, `amount`, `flag_reason` เข้ากับ template ก่อนส่งเข้า LLM Prompter ทุก row จะได้ Prompt เฉพาะของตัวเอง

---

### สรุป Lab 6 — KNIME with Gen AI

| Node | ชื่อ | หน้าที่ |
|------|------|---------|
| 1 | Credentials Configuration | เก็บ API Key ใน Password field, variable name = `credentials` |
| 2 | OpenAI Authenticator | รับ `credentials` → ยืนยันตัวตนกับ OpenAI |
| 3 | OpenAI Chat Model Connector | `gpt-4o-mini`, Temperature=0.2, Max tokens=800 |
| 4 | OpenAI LLM Connector | `gpt-3.5-turbo-instruct`, Temperature=0.2, Max tokens=200 |
| 5 | LLM Prompter | Prompt column=`Prompt`, Response column=`Response` |
| 6 | Table Creator | column `Prompt` (String), Row0 = คำถาม/คำสั่งสำหรับ LLM |

> **หลักการ:** Authenticate → Select Model → Create Prompt Table → LLM Prompter → Response
> ทุกอย่างอยู่ใน KNIME Workflow — ไม่ต้องเขียน Python หรือ API code เพิ่มเติม

---

*จบเอกสาร Day 3 Labs — Machine Learning and Deep Learning with KNIME*
*อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.*
