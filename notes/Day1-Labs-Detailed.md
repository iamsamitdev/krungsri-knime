# Day 1 — Labs คำแนะนำการตั้งค่าทุก Node อย่างละเอียด
## Foundation: พื้นฐาน KNIME และการเตรียมข้อมูล
**วิทยากร:** อาจารย์สามิตร โกยม | **สถาบัน:** IT Genius | **วันที่:** 18 พฤษภาคม 2569
**สถานที่:** กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา

---

## ข้อมูล Dataset ที่ใช้ใน Day 1

| ไฟล์ | จำนวน | Columns หลัก |
|------|-------|-------------|
| `financial_transactions.csv` | 350 rows, 15 cols | transaction_id, customer_id, transaction_date, amount, merchant, merchant_category, status, is_fraud, balance_after, currency, channel, branch_code, transaction_type |
| `sales_data.csv` | 250 rows, 16 cols | customer_id, product, category, quantity, unit_price, total_amount, region, salesperson |

**ดาวน์โหลด Dataset:**
```
https://github.com/iamsamitdev/krungsri-knime/blob/main/datasets/
```

---

## 🧪 Lab 0: Hello KNIME — ทดสอบโหลดข้อมูลและ Python Script

**วัตถุประสงค์:** ทำความคุ้นเคยกับ KNIME interface และทดสอบโหลดไฟล์ขนาดใหญ่

**Workflow:**
```
CSV Reader (sales_data_workshop.csv) → ดูข้อมูล

Python Script (สร้าง 2 ล้าน rows) → CSV Reader (test_2million.csv)
```

---

### สร้าง Workflow ใหม่

```
File → New KNIME Workflow → ตั้งชื่อ "Hello_KNIME" → Finish
```

---

### Node 1: CSV Reader (sales_data_workshop.csv)

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**ลากวาง** node ลงบน Workflow Editor

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **File/URL** | คลิก **Browse** → เลือก `sales_data_workshop.csv` | ไฟล์ข้อมูลตัวอย่าง |
| **Column delimiter** | `,` (comma) | ตัวคั่นแต่ละ column |
| **Has column header** | ✅ ติ๊ก | บรรทัดแรกคือชื่อ column |
| **Has row ID** | ❌ ไม่ติ๊ก | ไม่มี Row ID ในไฟล์ |

กด **OK** → คลิกขวาที่ node → **Execute**

**ตรวจสอบ:** คลิกขวา → **File Table** → เห็นข้อมูล

---

### Node 2: Python Script — สร้างไฟล์ทดสอบ 2 ล้าน rows

**ค้นหาใน Node Repository:** `Scripting → Python → Python Script (1⇒1)`

**ลากวาง** node บน Canvas (ไม่ต้องเชื่อมกับ CSV Reader)

**ดับเบิ้ลคลิก** เพื่อเปิด Script Editor → วางโค้ดต่อไปนี้:

```python
import knime.scripting.io as knio
import pandas as pd
import numpy as np

n = 2_000_000
np.random.seed(42)

df = pd.DataFrame({
    'order_id':     range(1, n + 1),
    'customer_id':  np.random.randint(1, 50000, n),
    'product':      np.random.choice(['Laptop','Phone','Tablet','Monitor','Keyboard'], n),
    'category':     np.random.choice(['Hardware','Software','Training','Consulting'], n),
    'quantity':     np.random.randint(1, 20, n),
    'unit_price':   np.random.randint(500, 80000, n),
    'discount':     np.random.choice([0, 5, 10, 15, 20], n),
    'order_date':   pd.date_range('2020-01-01', periods=n, freq='1min').strftime('%Y-%m-%d'),
    'region':       np.random.choice(['North','South','East','West','Central'], n),
    'salesperson':  np.random.choice(['Alice','Bob','Charlie','Diana','Eve'], n),
})
df['total_amount'] = (df['quantity'] * df['unit_price'] * (1 - df['discount'] / 100)).astype(int)

output_path = r'C:\KNIME_MASTER\workspace\test_2million.csv'
df.to_csv(output_path, index=False)
knio.output_tables[0] = knio.Table.from_pandas(df.head(5))
print(f"✅ สร้างไฟล์สำเร็จ: {n:,} แถว")
print(f"📁 บันทึกที่: {output_path}")
```

> ⚠️ แก้ `output_path` ให้ตรงกับโฟลเดอร์ในเครื่องของตัวเอง

กด **OK** → **Execute**

**ผลลัพธ์ที่ต้องเห็นใน Console:**
```
✅ สร้างไฟล์สำเร็จ: 2,000,000 แถว
📁 บันทึกที่: C:\KNIME_MASTER\workspace\test_2million.csv
```

---

### Node 3: CSV Reader (test_2million.csv)

**เพิ่ม CSV Reader ใหม่** — ตั้งค่าเหมือน Node 1 แต่เปลี่ยนไฟล์:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **File/URL** | Browse → `C:\KNIME_MASTER\workspace\test_2million.csv` |
| **Has column header** | ✅ ติ๊ก |

กด **OK** → **Execute**

**สังเกต:** KNIME โหลดข้อมูล 2 ล้านแถวได้โดยไม่ค้าง — ลองเปรียบเทียบกับ Excel ที่จะหยุดนิ่ง

---

## 🧪 Lab 1: ทำความรู้จัก KNIME — สร้าง Workflow แรก

**วัตถุประสงค์:** สร้าง workflow ง่ายๆ เพื่อทำความคุ้นเคยกับ Node, Port และ Execute

**Workflow:**
```
Data Generator → ดูข้อมูลอัตโนมัติ
```

---

### สร้าง Workflow ใหม่

```
File → New KNIME Workflow → ตั้งชื่อ "Day1_Hello_KNIME" → Finish
```

---

### Node 1: Data Generator

**ค้นหาใน Node Repository:** ค้นหา `Data Generator` → ลากวางบน Canvas

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Number of rows** | `100` | สร้างข้อมูลตัวอย่าง 100 แถว |
| **Number of columns** | `5` | 5 columns |
| **Domain** | ค่า default | ช่วงข้อมูลที่สร้าง |

> ถ้า dialog ว่างเปล่า (Data Generator บางเวอร์ชันไม่มี options) ให้กด **OK** เลย

กด **OK** → คลิกขวาที่ node → **Execute**

**ตรวจสอบสี node:** ต้องเป็น **สีเขียว** = Execute สำเร็จ

**ดูผลลัพธ์:** คลิกขวา → **Data Table** → เห็นตารางข้อมูลตัวอย่าง

**บันทึก:** `File → Save` (หรือ **Ctrl+S**)

> **Checkpoint:** เข้าใจว่า node สีเขียว = สำเร็จ, สีแดง = มีปัญหา, สีเหลือง = ยังไม่ Execute

---

## 🧪 Lab 2: นำเข้าข้อมูล financial_transactions.csv

**วัตถุประสงค์:** นำเข้าข้อมูลธุรกรรม ตรวจสอบโครงสร้าง และวิเคราะห์ Missing Values

**Workflow:**
```
CSV Reader → Statistics
          → Missing Value (ตรวจสอบค่าว่าง)
```

---

### สร้าง Workflow ใหม่

```
File → New KNIME Workflow → ตั้งชื่อ "Day1_Lab2_Import" → Finish
```

---

### Node 1: CSV Reader

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **File/URL** | Browse → เลือก `financial_transactions.csv` | ไฟล์ธุรกรรมหลัก |
| **Column delimiter** | `,` | ตัวคั่น column |
| **Row delimiter** | `\n` | ตัวคั่นแถว |
| **Quote char** | `"` | อักขระ quote |
| **Has column header** | ✅ ติ๊ก | Row แรกเป็นชื่อ column |
| **Has row ID** | ❌ ไม่ติ๊ก | ไม่มี Row ID |
| **Support short data lines** | ✅ ติ๊ก | รองรับแถวที่มี column น้อยกว่า header |
| **Encoding** | `UTF-8` | รองรับภาษาไทยและอักขระพิเศษ |
| **Limit rows** | ❌ ไม่ติ๊ก | อ่านทุกแถว |

**แท็บ Transformation:** ตรวจสอบ column types ที่ KNIME detect ให้:

| Column | Type ที่ควรได้ | ถ้า detect ผิด |
|--------|--------------|--------------|
| `transaction_id` | String (S) | ปกติถูกต้อง |
| `transaction_date` | String (S) | ✅ ปกติ — จะแปลงทีหลัง |
| `amount` | Double (D) | ถ้าเป็น String → แก้ใน Data Type Caster |
| `is_fraud` | Integer (I) | ถ้าเป็น String → แก้ทีหลัง |
| `status` | String (S) | ปกติถูกต้อง |

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table**
- ✅ จำนวน rows = **350**
- ✅ จำนวน columns = **15**
- ✅ ชื่อ column ครบถ้วน

---

### Node 2: Statistics

**ค้นหาใน Node Repository:** `Analytics → Statistics → Statistics`

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Statistics

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Statistics:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Columns to include** | คลิก **Add All** | ดูสถิติทุก column |
| **Statistics** | ✅ Mean, ✅ Std Dev, ✅ Min, ✅ Max, ✅ Missing Count | เลือกสถิติที่ต้องการ |
| **Nominal values** | `20` | จำนวน unique values ที่แสดงสำหรับ String column |

กด **OK** → **Execute**

**ดูผล:** คลิกขวา → **Statistics View** → สังเกต:
- `amount`: Min / Max / Mean → ตรวจสอบว่าสมเหตุสมผล
- `is_fraud`: Mean ≈ 0.05–0.10 → มีธุรกรรม Fraud ประมาณ 5–10%
- Column ไหนที่มี **Missing Count > 0** → ต้องจัดการทีหลัง

---

### Node 3: Missing Value (ตรวจสอบ)

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Missing Value Filter`

> ⚠️ **Node นี้ใช้เพื่อ "ดู" ว่ามี Missing Values ที่ไหน — ยังไม่จัดการ**

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Missing Value Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Filter Rows that contain** | `At least one missing value` | กรองเฉพาะแถวที่มี Missing อย่างน้อย 1 cell |
| **Columns to check** | **All columns** | ตรวจทุก column |

กด **OK** → **Execute**

**ดูผล:** คลิกขวา → **File Table**
- Output Port 1 = แถวที่มี Missing Values → นับจำนวน
- Output Port 2 = แถวที่ไม่มี Missing → แถวสมบูรณ์

**บันทึกใน Annotation** (ดับเบิ้ลคลิกบน Canvas เพิ่มข้อความ): จำนวน Missing Rows ที่พบ

> **Audit Insight:** Column `merchant` มักมีค่าว่างเมื่อธุรกรรมเป็น ATM หรือ Online

---

## 🧪 Lab 3: Type Conversion และ String Manipulation

**วัตถุประสงค์:** แปลงประเภทข้อมูล ทำความสะอาด และสร้าง Column ใหม่สำหรับ Audit

**Workflow:**
```
CSV Reader → Data Type Caster → String to Date&Time → Rule Engine
```

---

### สร้าง Workflow ใหม่

```
File → New KNIME Workflow → ตั้งชื่อ "Day1_Lab3_TypeConversion" → Finish
```

---

### Node 1: CSV Reader

ตั้งค่าเหมือน Lab 2 Node 1 — อ่าน `financial_transactions.csv`

---

### Node 2: Data Type Caster

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → Data Type Caster`

**จุดประสงค์:** แปลง column ที่ KNIME detect ผิดประเภทให้ถูกต้อง

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Data Type Caster

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Type Settings:**

คลิก **Add** เพื่อเพิ่มการแปลงแต่ละ column:

| Column | จาก (From) | ไปเป็น (To) | คำอธิบาย |
|--------|-----------|------------|----------|
| `amount` | String | Double | ยอดเงินต้องเป็นทศนิยม |
| `is_fraud` | String | Integer | 0/1 ต้องเป็นจำนวนเต็ม |
| `balance_after` | String | Double | ยอดคงเหลือต้องเป็นทศนิยม |

**วิธีเพิ่มการแปลง:**
1. คลิก **Add** → เลือก column จาก dropdown
2. เลือก **Type** ที่ต้องการ
3. ทำซ้ำสำหรับทุก column ที่ต้องแปลง

กด **OK** → **Execute**

> **ถ้ามี Error:** "Cannot convert '1,299.29' to Double" → ข้อมูลมี comma → ใช้ **String Manipulation** ลบ comma ก่อน แล้วค่อย Cast

---

### Node 3: String to Date&Time

**ค้นหาใน Node Repository:** `Date & Time → Transform → String to Date&Time`

**จุดประสงค์:** แปลง `transaction_date` จาก String เป็น Date type จริงๆ เพื่อกรองตามช่วงวันที่ได้

**เชื่อมต่อ:** Output ของ Data Type Caster → Input ของ String to Date&Time

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Selected columns** | `transaction_date` | column ที่ต้องแปลง |
| **Date format** | `yyyy-MM-dd` | รูปแบบวันที่ในข้อมูล เช่น 2024-03-15 |
| **Type** | `Date` | แปลงเป็น Date (ไม่มีเวลา) |
| **Replace column** | ✅ ติ๊ก | แทนที่ column เดิม (ไม่สร้าง column ใหม่) |
| **Abort on error** | ❌ ไม่ติ๊ก | ถ้าแปลงไม่ได้ให้ใส่ค่าว่างแทน |

> **รูปแบบวันที่ที่ใช้บ่อย:**
> - `yyyy-MM-dd` → 2024-03-15
> - `dd/MM/yyyy` → 15/03/2024
> - `dd-MMM-yyyy` → 15-Mar-2024
> - `yyyy/MM/dd HH:mm:ss` → 2024/03/15 14:30:00

กด **OK** → **Execute**

**ตรวจสอบ:** column `transaction_date` ต้องมีสัญลักษณ์ 📅 (Date type)

---

### Node 4: Rule Engine — สร้าง Column risk_level

**ค้นหาใน Node Repository:** `Manipulation → Column → Calculate → Rule Engine`

**จุดประสงค์:** สร้าง column ใหม่ `risk_level` จากการรวมเงื่อนไข amount และ is_fraud

**เชื่อมต่อ:** Output ของ String to Date&Time → Input ของ Rule Engine

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ช่อง Rule Editor — พิมพ์กฎต่อไปนี้ทีละบรรทัด:**

```
$amount$ > 30000 AND $is_fraud$ = 1 => "HIGH RISK"
$amount$ > 10000 AND $is_fraud$ = 1 => "MEDIUM RISK"
$is_fraud$ = 1 => "LOW RISK"
TRUE => "NORMAL"
```

> **อธิบายกฎ:**
> - กฎที่ 1: ยอดสูงกว่า 30,000 และ fraud = 1 → "HIGH RISK"
> - กฎที่ 2: ยอดสูงกว่า 10,000 และ fraud = 1 → "MEDIUM RISK"
> - กฎที่ 3: fraud = 1 (ยอดไม่ถึง) → "LOW RISK"
> - กฎที่ 4: ทุกกรณีที่เหลือ → "NORMAL"
> - กฎประเมิน **บนลงล่าง** — แถวที่ match กฎแรกจะหยุดทันที

**ส่วนล่างของ Dialog:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Append Column** | ✅ เลือก | สร้าง column ใหม่ |
| **New column name** | `risk_level` | ชื่อ column ใหม่ |
| **Replace Column** | ❌ ไม่เลือก | ไม่ทับ column เดิม |

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table** → column `risk_level` ปรากฏท้ายตาราง มีค่า HIGH RISK / MEDIUM RISK / LOW RISK / NORMAL

---

## 🧪 Lab 4: Filtering ธุรกรรมที่มีความเสี่ยง

**วัตถุประสงค์:** คัดกรองธุรกรรมสำหรับงาน Audit และ Export รายงาน

**Workflow:**
```
[ต่อจาก Lab 3] → Row Filter (status) → Row Filter (amount) → Column Filter
                                                                    │
                                              Row Splitter (fraud) ─┤
                                                    │               │
                                               CSV Writer      [ดูข้อมูล]
```

---

### Node 5: Row Filter — กรองเฉพาะ Completed

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Filter`

**เชื่อมต่อ:** Output ของ Rule Engine (Lab 3) → Input ของ Row Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Filter Criteria:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Filter by attribute value** | ✅ เลือก | กรองตามค่าใน column |
| **Column** | `status` | column ที่ใช้กรอง |
| **Operator** | `equals` | ค่าต้องเท่ากับ |
| **Value** | `Completed` | กรองเฉพาะธุรกรรม Completed |
| **Case sensitive** | ✅ ติ๊ก | ตัวพิมพ์เล็ก-ใหญ่มีผล |
| **Include rows that match** | ✅ เลือก | เก็บแถวที่ผ่านเงื่อนไข |

กด **OK** → **Execute**

**ตรวจสอบ:** Output Port บน = แถว Completed เท่านั้น

---

### Node 6: Row Filter — กรองยอดสูง (High Value)

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Filter`

**เชื่อมต่อ:** Output Port บน ของ Node 5 → Input ของ Row Filter ใหม่

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Filter by attribute value** | ✅ เลือก | |
| **Column** | `amount` | |
| **Operator** | `>=` | มากกว่าหรือเท่ากับ |
| **Value** | `20000` | ยอด ≥ 20,000 บาท |
| **Include rows that match** | ✅ เลือก | เก็บธุรกรรมยอดสูง |

กด **OK** → **Execute**

> **ทำไมต้องกรอง 2 ครั้ง?** เพราะ KNIME Row Filter กรองได้ทีละเงื่อนไข — ถ้าต้องการ AND หลายเงื่อนไขให้ใช้หลาย Row Filter ต่อกัน หรือใช้ Row Splitter แทน

---

### Node 7: Column Filter — เลือก Columns สำหรับ Audit

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**เชื่อมต่อ:** Output ของ Node 6 → Input ของ Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ย้ายไปฝั่ง Exclude (คลิก column → กด `>`):**
- `currency`
- `channel`
- `balance_after`

**ฝั่ง Include ต้องเหลือ:**
- `transaction_id`, `customer_id`, `transaction_date`
- `amount`, `merchant`, `merchant_category`
- `status`, `is_fraud`, `branch_code`
- `transaction_type`, `risk_level`

กด **OK** → **Execute**

---

### Node 8: Row Splitter — แยก Fraud จาก Normal

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Splitter`

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Row Splitter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Filter Criteria:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Filter by attribute value** | ✅ เลือก | |
| **Column** | `is_fraud` | |
| **Operator** | `=` | |
| **Value** | `1` | ธุรกรรม Fraud |

กด **OK** → **Execute**

**Output Ports:**
- **Port บน** = ธุรกรรม Fraud (`is_fraud = 1`) → ส่งต่อ CSV Writer
- **Port ล่าง** = ธุรกรรมปกติ (`is_fraud = 0`) → ดูข้อมูลหรือวิเคราะห์ต่อ

---

### Node 9: CSV Writer — บันทึกรายการ Fraud

**ค้นหาใน Node Repository:** `IO → Write → CSV Writer`

**เชื่อมต่อ:** Output Port **บน** ของ Row Splitter → Input ของ CSV Writer

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Output location** | Browse → เลือก folder → ตั้งชื่อ `audit_high_risk_transactions.csv` | ที่เก็บไฟล์ผลลัพธ์ |
| **Column delimiter** | `,` | ตัวคั่น column |
| **Write column headers** | ✅ ติ๊ก | ใส่ header row |
| **Write row IDs** | ❌ ไม่ติ๊ก | ไม่ต้องการ Row ID |
| **If file exists** | `Overwrite` | เขียนทับถ้าไฟล์มีอยู่แล้ว |
| **Encoding** | `UTF-8` | รองรับอักขระพิเศษ |

กด **OK** → **Execute**

**ตรวจสอบ:** เปิด folder ที่เลือก → ต้องเห็นไฟล์ `audit_high_risk_transactions.csv`

---

## 🧪 Lab 5: วิเคราะห์ธุรกรรมรายสาขาและประเภท

**วัตถุประสงค์:** สรุปยอดธุรกรรมตามมิติต่างๆ และ Join กับข้อมูลการขาย

**Workflow:**
```
[ข้อมูล clean จาก Lab 3]
        │
        ├── GroupBy (branch_code) ──────── ดูยอดรายสาขา
        │
        ├── GroupBy (category + fraud) ─── วิเคราะห์ Fraud ต่อหมวด
        │
        ├── Pivot (branch × type) ───────── เปรียบเทียบมิติ
        │
        └── Joiner ← CSV Reader (sales_data.csv)
```

---

### Node 1: GroupBy — ยอดธุรกรรมรายสาขา

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → GroupBy`

**เชื่อมต่อ:** Output ของ Rule Engine (จาก Lab 3) → Input ของ GroupBy

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Groups:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Group column(s)** | `branch_code` | จัดกลุ่มตามสาขา |

**แท็บ Manual Aggregation — คลิก Add สำหรับแต่ละ column:**

| Column | Aggregation | New Column Name | คำอธิบาย |
|--------|-------------|-----------------|----------|
| `amount` | `Sum` | `total_amount` | ยอดรวมทั้งสาขา |
| `amount` | `Mean` | `avg_amount` | ยอดเฉลี่ยต่อธุรกรรม |
| `amount` | `Max` | `max_amount` | ธุรกรรมยอดสูงสุด |
| `transaction_id` | `Count` | `txn_count` | จำนวนธุรกรรม |
| `is_fraud` | `Sum` | `fraud_count` | จำนวนธุรกรรม Fraud |

**แท็บ Advanced Settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Sort groups** | ✅ ติ๊ก |
| **Missing values** | `Ignore` |

กด **OK** → **Execute**

**ตรวจสอบ:** ผลลัพธ์เป็นตารางสรุปยอดแต่ละสาขา

---

### Node 2: GroupBy — วิเคราะห์ Fraud ต่อ Category

**เพิ่ม GroupBy ใหม่** — เชื่อมต่อจากข้อมูลเดียวกัน (แตกสาขาขนาน)

**แท็บ Groups:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Group column(s)** | `merchant_category`, `is_fraud` |

**แท็บ Manual Aggregation:**

| Column | Aggregation | New Column Name |
|--------|-------------|-----------------|
| `transaction_id` | `Count` | `txn_count` |
| `amount` | `Sum` | `total_amount` |
| `amount` | `Mean` | `avg_amount` |

กด **OK** → **Execute**

> **Audit Insight:** ดูว่า merchant_category ไหนมี is_fraud=1 บ่อยที่สุด → Audit Target

---

### Node 3: Pivot — เปรียบเทียบยอดตาม Transaction Type

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Pivot`

**เชื่อมต่อ:** Output ของ Rule Engine → Input ของ Pivot

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ส่วน | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Group columns** | `branch_code` | แต่ละแถว = 1 สาขา |
| **Pivot columns** | `transaction_type` | แต่ละ column = 1 ประเภทธุรกรรม (ATM, Online, Branch) |
| **Aggregation** | `Sum` บน `amount` | รวมยอดของแต่ละ cell |
| **Ignore missing values** | ✅ ติ๊ก | |
| **Make aggregation name** | ✅ ติ๊ก | ตั้งชื่อ column อัตโนมัติ |

กด **OK** → **Execute**

**ผลลัพธ์ตัวอย่าง:**

| branch_code | ATM+Sum(amount) | Online+Sum(amount) | Branch+Sum(amount) |
|-------------|-----------------|---------------------|---------------------|
| BKK001 | 450,000 | 1,200,000 | 780,000 |
| BKK002 | 320,000 | 980,000 | 620,000 |

---

### Node 4: CSV Reader (sales_data.csv)

**เพิ่ม CSV Reader ใหม่** บน Canvas

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **File/URL** | Browse → เลือก `sales_data.csv` |
| **Has column header** | ✅ ติ๊ก |
| **Column delimiter** | `,` |

กด **OK** → **Execute**

**ตรวจสอบ:** 250 rows, 16 columns

---

### Node 5: Joiner — รวม Transactions กับ Sales

**ค้นหาใน Node Repository:** `Manipulation → Column → Join → Joiner`

**เชื่อมต่อ:**
- **Input Port บน** ← Output ของ CSV Reader (financial_transactions)
- **Input Port ล่าง** ← Output ของ CSV Reader (sales_data)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Joiner Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Join mode** | `Inner Join` | เฉพาะแถวที่มีคู่กันในทั้ง 2 ตาราง |
| **Top input (left table)** | `customer_id` | Key จากตาราง Transactions |
| **Bottom input (right table)** | `customer_id` | Key จากตาราง Sales |

**แท็บ Column Selection:**

| ส่วน | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Top (left)** | เลือก columns ที่ต้องการจาก Transactions | ✅ transaction_id, amount, is_fraud, risk_level |
| **Bottom (right)** | เลือก columns ที่ต้องการจาก Sales | ✅ product, category, total_amount |
| **Duplicate column handling** | `Append suffix` | ถ้าชื่อซ้ำให้เพิ่ม suffix |
| **Suffix** | `_right` | suffix ของตาราง right |

กด **OK** → **Execute**

**ตรวจสอบ:** Output table มี columns จากทั้ง 2 ตาราง รวมกัน

---

## 🧪 Lab 6: Workshop สรุป Day 1 — Audit Dashboard Workflow

**วัตถุประสงค์:** สร้าง End-to-End Workflow ครบกระบวนการสำหรับรายงาน Audit

**Workflow สมบูรณ์:**
```
CSV Reader (financial_transactions.csv)
        │
        ▼
Data Type Caster
(amount, balance_after → Double; is_fraud → Integer)
        │
        ▼
String to Date&Time
(transaction_date → Date, format: yyyy-MM-dd)
        │
        ▼
Missing Value
(merchant → "Unknown"; amount → Mean)
        │
        ▼
Rule Engine
(สร้าง risk_level: HIGH/MEDIUM/LOW RISK/NORMAL)
        │
        ▼
Row Filter
(status = "Completed" AND amount >= 5000)
        │
   ┌────┴────────────────────────┐
   ▼                             ▼
GroupBy                     Row Splitter
(branch_code)               (is_fraud = 1)
Sum/Count/Mean amount            │           │
   │                       Fraud rows   Normal rows
   ▼                             │
Normalizer                  Column Filter
(Min-Max on amount)              │
   │                        CSV Writer
   ▼                  (audit_fraud.csv)
Bar Chart
(ยอดรวมรายสาขา)
```

---

### Node 1: CSV Reader

ตั้งค่าเหมือน Lab 2 Node 1 — อ่าน `financial_transactions.csv`

---

### Node 2: Data Type Caster

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → Data Type Caster`

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Data Type Caster

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog — เพิ่มการแปลง:

| Column | จาก | ไปเป็น |
|--------|-----|--------|
| `amount` | String | Double |
| `balance_after` | String | Double |
| `is_fraud` | String | Integer |

กด **OK** → **Execute**

---

### Node 3: String to Date&Time

**ค้นหาใน Node Repository:** `Date & Time → Transform → String to Date&Time`

**เชื่อมต่อ:** Output ของ Data Type Caster → Input ของ String to Date&Time

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Selected columns** | `transaction_date` |
| **Date format** | `yyyy-MM-dd` |
| **Type** | `Date` |
| **Replace column** | ✅ ติ๊ก |

กด **OK** → **Execute**

---

### Node 4: Missing Value

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Missing Value`

**เชื่อมต่อ:** Output ของ String to Date&Time → Input ของ Missing Value

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Settings — ตั้งค่าแยกตาม column type:**

**สำหรับ String columns:**

| Column | Handling | ค่า |
|--------|----------|-----|
| `merchant` | **Fixed Value** | `Unknown` |
| `merchant_category` | **Fixed Value** | `Uncategorized` |

**สำหรับ Double/Integer columns:**

| Column | Handling | คำอธิบาย |
|--------|----------|----------|
| `amount` | **Mean** | แทนด้วยค่าเฉลี่ย |
| `balance_after` | **Mean** | แทนด้วยค่าเฉลี่ย |

**วิธีตั้งค่า:**
1. คลิกแท็บ **Double** → เลือก column `amount` → เลือก **Mean**
2. คลิกแท็บ **String** → เลือก column `merchant` → เลือก **Fix Value** → พิมพ์ `Unknown`
3. ทำซ้ำสำหรับ column อื่น

กด **OK** → **Execute**

---

### Node 5: Rule Engine

ตั้งค่าเหมือน Lab 3 Node 4

**กฎ:**
```
$amount$ > 30000 AND $is_fraud$ = 1 => "HIGH RISK"
$amount$ > 10000 AND $is_fraud$ = 1 => "MEDIUM RISK"
$is_fraud$ = 1 => "LOW RISK"
TRUE => "NORMAL"
```

**Output column:** `risk_level`

กด **OK** → **Execute**

---

### Node 6: Row Filter — กรองเฉพาะ Completed ≥ 5,000

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Filter`

**เชื่อมต่อ:** Output ของ Rule Engine → Input ของ Row Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

> ⚠️ **Row Filter กรองได้ครั้งละ 1 เงื่อนไข** — ต้องใช้ 2 node ต่อกัน

**Row Filter ที่ 1 — กรอง status:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column** | `status` |
| **Operator** | `equals` |
| **Value** | `Completed` |

กด **OK** → **Execute**

**เพิ่ม Row Filter ที่ 2 — กรอง amount:**

เชื่อมต่อจาก Row Filter แรก → Row Filter ใหม่:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column** | `amount` |
| **Operator** | `>=` |
| **Value** | `5000` |

กด **OK** → **Execute**

---

### Node 7: GroupBy — สรุปรายสาขา

ตั้งค่าเหมือน Lab 5 Node 1

**Group:** `branch_code`

**Aggregation:**

| Column | Function | New Name |
|--------|----------|----------|
| `amount` | Sum | `total_amount` |
| `amount` | Mean | `avg_amount` |
| `transaction_id` | Count | `txn_count` |
| `is_fraud` | Sum | `fraud_count` |

กด **OK** → **Execute**

---

### Node 8: Normalizer

**ค้นหาใน Node Repository:** `Manipulation → Column → Transform → Normalizer`

**เชื่อมต่อ:** Output ของ GroupBy → Input ของ Normalizer

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Method** | `Min-Max Normalization` | แปลงให้อยู่ในช่วง [0, 1] |
| **New minimum** | `0.0` | ค่าต่ำสุดหลัง normalize |
| **New maximum** | `1.0` | ค่าสูงสุดหลัง normalize |

**Columns to normalize — เลือกเฉพาะ Numeric:**
- ✅ `total_amount`
- ✅ `avg_amount`
- ✅ `txn_count`
- ❌ `branch_code` (String — ไม่ normalize)

กด **OK** → **Execute**

> **สำหรับ Day 1:** Normalizer แสดงให้เห็นว่า KNIME ทำได้ — ใน Day 2 จะใช้จริงก่อน ML

---

### Node 9: Bar Chart — ยอดรวมรายสาขา

**ค้นหาใน Node Repository:** `Views → JavaScript → Bar Chart`

**เชื่อมต่อ:** Output ของ GroupBy (ก่อน Normalizer) → Input ของ Bar Chart

> ใช้ข้อมูลก่อน Normalize เพราะ Bar Chart ต้องการค่าจริง ไม่ใช่ค่า Normalized

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Category column** | `branch_code` | แกน X |
| **Frequency column** | `total_amount` | แกน Y (ยอดรวม) |
| **Chart title** | `ยอดธุรกรรมรวมรายสาขา — Audit Report 2569` | หัวข้อกราฟ |
| **X-axis label** | `สาขา` | ชื่อแกน X |
| **Y-axis label** | `ยอดธุรกรรม (บาท)` | ชื่อแกน Y |
| **Orientation** | `Vertical` | แท่งตั้ง |
| **Include missing values** | ❌ ไม่ติ๊ก | ไม่แสดงแถวที่ขาดข้อมูล |

กด **OK** → **Execute** → คลิกขวา → **View: Bar Chart**

---

### Node 10: Row Splitter — แยก Fraud

**ค้นหาใน Node Repository:** `Manipulation → Row → Filter → Row Splitter`

**เชื่อมต่อ:** Output ของ Row Filter (amount ≥ 5,000) → Input ของ Row Splitter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column** | `is_fraud` |
| **Operator** | `=` |
| **Value** | `1` |

กด **OK** → **Execute**

---

### Node 11: Column Filter — ก่อน Export

**เชื่อมต่อ:** Output Port **บน** (Fraud) ของ Row Splitter → Column Filter

**เก็บ columns เหล่านี้เท่านั้น:**
- `transaction_id`, `customer_id`, `transaction_date`
- `amount`, `merchant`, `merchant_category`
- `branch_code`, `transaction_type`
- `risk_level`, `is_fraud`

กด **OK** → **Execute**

---

### Node 12: CSV Writer — Export รายงาน Fraud

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ CSV Writer

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Output location** | Browse → ตั้งชื่อ `audit_fraud_2569Q2.csv` |
| **Write column headers** | ✅ ติ๊ก |
| **Column delimiter** | `,` |
| **If file exists** | `Overwrite` |
| **Encoding** | `UTF-8` |

กด **OK** → **Execute**

---

## สรุป Nodes ที่ใช้ใน Day 1

| Lab | Node | จำนวน | Output สำคัญ |
|-----|------|--------|--------------|
| Lab 0 | CSV Reader, Python Script | 3 | โหลดข้อมูลขนาดใหญ่ |
| Lab 1 | Data Generator | 1 | เข้าใจ Node/Port/Execute |
| Lab 2 | CSV Reader, Statistics, Missing Value Filter | 3 | Schema + Missing analysis |
| Lab 3 | CSV Reader, Data Type Caster, String to Date&Time, Rule Engine | 4 | ข้อมูลสะอาด + risk_level |
| Lab 4 | Row Filter ×2, Column Filter, Row Splitter, CSV Writer | 5 | audit_high_risk.csv |
| Lab 5 | GroupBy ×2, Pivot, CSV Reader, Joiner | 5 | ตารางสรุป + Join |
| Lab 6 | ทุก Node รวม 12 node | 12 | Audit Dashboard ครบกระบวนการ |

---

## เคล็ดลับการใช้ KNIME สำหรับงาน Audit

| เคล็ดลับ | วิธีทำ |
|----------|--------|
| **บันทึกบ่อยๆ** | Ctrl+S ทุกครั้งที่ Execute สำเร็จ |
| **ตั้งชื่อ Node** | คลิกขวา → Edit Node Description |
| **เพิ่ม Annotation** | ดับเบิ้ลคลิกบน Canvas เปล่าๆ |
| **Reset Node** | คลิกขวา → Reset → ล้างผลลัพธ์เพื่อ Execute ใหม่ |
| **ดู Log** | View → Console → ดู Error message |
| **Copy Workflow** | Ctrl+A → Ctrl+C → Ctrl+V → ย้าย Node ทั้งหมด |
| **ตั้งชื่อ Workflow** | ชัดเจน เช่น `Audit_Fraud_2569Q2_v2` |

---

*จัดทำโดย: อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.*
*สำหรับ: กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา | Day 1: 18 พฤษภาคม 2569*
