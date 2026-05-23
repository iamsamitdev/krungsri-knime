# Day 1 — Labs คำแนะนำการตั้งค่าทุก Node อย่างละเอียด
## Foundation: พื้นฐาน KNIME และการเตรียมข้อมูล
**วิทยากร:** อาจารย์สามิตร โกยม | **สถาบัน:** IT Genius | **วันที่:** 18 พฤษภาคม 2569
**สถานที่:** กลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา
**เวอร์ชัน:** KNIME Analytics Platform **5.3** (Modern UI)

---

> **หมายเหตุเกี่ยวกับ KNIME 5.3 Modern UI**
> KNIME 5.3 ยังคงใช้ **Dialog แบบ Tab-based** เหมือนเวอร์ชันก่อนหน้า — สิ่งที่ปรับปรุงใน 5.3 ได้แก่:
> - **Dialog ยังเป็น Tab-based** เช่น CSV Reader มี tabs: Settings / Transformation / Advanced Settings / Limit Rows / Encoding / Flow Variables / Job Manager Selection / Memory Policy
> - **CSV Reader — Input location** มี `Read from` dropdown สำหรับเลือก `Local File System` / `KNIME Hub` / `Mountpoint` และ Mode radio button: **File** / **Files in folder**
> - **Row Filter** ใช้ Dialog แบบ Tab-based เหมือนกัน — กำหนดเงื่อนไขโดยเลือก Column / Operator / Value ในแท็บ Settings
> - **Color Manager** กำหนดสีต่อค่าในคอลัมน์ได้แบบ custom palette
> - Node icons มีสี scheme: สีส้ม = IO nodes, สีเหลือง = Manipulation, สีน้ำเงิน = Views/Analytics

---

## ข้อมูล Dataset ที่ใช้ใน Day 1

| ไฟล์ | จำนวน | Columns หลัก |
|------|-------|-------------|
| `sales_data_workshop.csv` | 250+ rows, 9 cols | Transaction_ID, Date, Product_Category, Product_Name, Quantity, Price_Per_Unit, Total_Sales, Region, Customer_Type |
| `financial_transactions.csv` | 350 rows, 15 cols | transaction_id, customer_id, transaction_date, amount, merchant, merchant_category, status, is_fraud, balance_after, currency, channel, branch_code, transaction_type |
| `sales_data.csv` | 250 rows, 16 cols | customer_id, product, category, quantity, unit_price, total_amount, region, salesperson |

**ดาวน์โหลด Dataset:**
```
https://github.com/iamsamitdev/krungsri-knime/blob/main/datasets/
```

---

## 🧪 Lab 1: Day1_Lab1_KNIME_Interface

**วัตถุประสงค์:** สร้าง workflow แรก เรียนรู้ Node/Port/Execute และ interface ของ KNIME 5.3

**Workflow:**
```
Data Generator → ดูผลลัพธ์
```

### สร้าง Workflow ใหม่

```
File → New Workflow... → ตั้งชื่อ "Day1_Lab1_KNIME_Interface" → Create
```

### Node 1: Data Generator

**ค้นหา:** พิมพ์ `Data Generator` ใน Search → ลากวางบน Canvas

**ดับเบิ้ลคลิก** → กด **OK** (ใช้ค่า default)

→ คลิกขวา → **Execute** → สังเกตสีเปลี่ยนเป็น **สีเขียว**

→ คลิกขวา → **Data Table** → ดูข้อมูลตัวอย่างที่ถูกสร้างขึ้น

> **KNIME 5.3 สีสถานะ:**
> - 🔴 แดง = ยังไม่ได้ตั้งค่า / มี Error
> - 🟡 เหลือง = ตั้งค่าแล้ว รอ Execute
> - 🟢 เขียว = Execute สำเร็จ
> - 🔵 น้ำเงิน = กำลัง Execute อยู่

**บันทึก:** `Ctrl+S`

---

## 🧪 Lab 2: Day1_Lab2_Hello_KNIME

**วัตถุประสงค์:** เรียนรู้ node พื้นฐานของ KNIME 5.3 ครบวงจร ตั้งแต่การสร้างข้อมูล อ่านไฟล์ กรองข้อมูล จัดเรียง คำนวณ จนถึงการสร้างกราฟและ Export Excel

**Workflow แบ่งเป็น 2 ส่วน:**

```
┌─────────────────────────────────────────────────────────────┐
│  Component: Create and Read Large CSV                       │
│                                                             │
│  Python Script ──▶ CSV Reader (2M rows)                     │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  Overview Workflow node in KNIME                            │
│                                                             │
│  CSV Reader ──▶ Row Filter ──▶ Column Filter               │
│       ──▶ Column Resorter ──▶ String Manipulation          │
│                    │                    │                   │
│                    ▼                    ▼                   │
│             Color Manager           GroupBy                 │
│                    │                    │                   │
│                    ▼                    ▼                   │
│             Scatter Plot          Math Formula              │
│                                    │         │              │
│                                    ▼         ▼              │
│                               Bar Chart  Excel Writer       │
└─────────────────────────────────────────────────────────────┘
```

---

### สร้าง Workflow ใหม่

```
File → New Workflow... → ตั้งชื่อ "Day1_Lab2_Hello_KNIME" → Create
```

> **KNIME 5.3:** ใช้คำว่า "New Workflow..." ใน File menu หรือคลิก **+** ใน KNIME Explorer panel

---

## ส่วนที่ 1: Component — Create and Read Large CSV

### Node 1: Python Script

**ค้นหาใน Node Repository:** พิมพ์ `Python Script` ใน Search box → เลือก **Python Script** ใต้หมวด `Scripting`

**ลากวาง** node ลงบน Canvas → ดับเบิ้ลคลิกเปิด Dialog

**KNIME 5.3 Python Script Dialog:**

| ส่วน | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Python version** | `Python 3` | ใช้ Python 3 environment ที่ติดตั้งไว้ |
| **Script editor** | วางโค้ดด้านล่าง | พื้นที่เขียน script |

**วางโค้ดนี้ใน Script Editor:**

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

df['total_amount'] = (
    df['quantity'] * df['unit_price'] * (1 - df['discount'] / 100)
).astype(int)

# บันทึกเป็น CSV
output_path = r'C:\KNIME_MASTER\workspace\test_2million.csv'
df.to_csv(output_path, index=False)

# ส่ง 5 แถวแรกออกเป็น KNIME Table เพื่อยืนยัน
knio.output_tables[0] = knio.Table.from_pandas(df.head(5))

print(f"✅ สร้างไฟล์สำเร็จ: {n:,} แถว")
print(f"📁 บันทึกที่: {output_path}")
```

> ⚠️ แก้ `output_path` ให้ตรงกับ path จริงในเครื่องของตนเอง เช่น `C:\Users\YourName\Desktop\test_2million.csv`

กด **OK** → คลิกขวา node → **Execute**

**ผลลัพธ์ที่ต้องเห็นใน Console ด้านล่าง:**
```
✅ สร้างไฟล์สำเร็จ: 2,000,000 แถว
📁 บันทึกที่: C:\KNIME_MASTER\workspace\test_2million.csv
```

**Node เปลี่ยนเป็นสีเขียว** = Execute สำเร็จ, Output port มี 5 แถว (preview)

---

### Node 2: CSV Reader (อ่านไฟล์ 2 ล้านแถว)

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**เชื่อมต่อ:** ไม่ต้องเชื่อมกับ Python Script — วาง node แยกอิสระ

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**KNIME 5.3 CSV Reader — แท็บ Settings:**

> Dialog มี tabs: **Settings** / Transformation / Advanced Settings / Limit Rows / Encoding / Flow Variables / Job Manager Selection / Memory Policy

**ส่วน Input location:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Read from** | `Local File System` | dropdown เลือก Local File System |
| **Mode** | `File` (radio button) | เลือก File หรือ Files in folder |
| **File** | Browse → เลือก `test_2million.csv` | path ไฟล์ที่สร้างจาก Python Script |

**ส่วน Reader options → Format:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Autodetect format** | คลิกปุ่ม (ไม่บังคับ) | ให้ KNIME ตรวจจับ format อัตโนมัติ |
| **Column delimiter** | `,` | ตัวคั่น column (comma) |
| **Row delimiter** | `Line break` (radio) | ขึ้นบรรทัดใหม่ตาม OS (`\r\n`) |
| **Quote char** | `"` | ตัวครอบ text |
| **Quote escape char** | `"` | |
| **Comment char** | `#` | แถวที่ขึ้นต้นด้วย # จะถูกข้ามไป |
| **Has column header** | ✅ ติ๊ก | Row แรกเป็นชื่อ column |
| **Has RowID** | ❌ ไม่ติ๊ก | |
| **Support short data rows** | ❌ ไม่ติ๊ก | |

กด **OK** → **Execute**

**สังเกต:** KNIME 5.3 โหลด 2,000,000 แถวได้โดยไม่ค้าง — ลองเปรียบเทียบกับ Excel ที่รองรับสูงสุดเพียง 1,048,576 แถว

**ตรวจสอบ:** คลิกขวา → **Data Table** → เห็น 2,000,000 rows, 11 columns

---

## ส่วนที่ 2: Overview Workflow — ครอบคลุม Nodes พื้นฐานทั้งหมด

### Node 3: CSV Reader (sales_data_workshop.csv)

**ค้นหาใน Node Repository:** `IO → Read → CSV Reader`

**ลากวาง** node ใหม่บน Canvas (ส่วน Overview Workflow)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**KNIME 5.3 — แท็บ Settings:**

> Dialog มี tabs: **Settings** / Transformation / Advanced Settings / Limit Rows / Encoding / Flow Variables / Job Manager Selection / Memory Policy

**ส่วน Input location:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Read from** | `Local File System` | dropdown เลือก Local File System |
| **Mode** | `File` (radio button) | |
| **File** | Browse → เลือก `sales_data_workshop.csv` | path ไฟล์ข้อมูลการขาย |

**ส่วน Reader options → Format:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Column delimiter** | `,` | ตัวคั่น column |
| **Row delimiter** | `Line break` (radio) | |
| **Quote char** | `"` | |
| **Quote escape char** | `"` | |
| **Comment char** | `#` | |
| **Has column header** | ✅ ติ๊ก | Row แรกเป็นชื่อ column |
| **Has RowID** | ❌ ไม่ติ๊ก | |
| **Support short data rows** | ❌ ไม่ติ๊ก | |

**แท็บ Transformation** — ตรวจสอบ KNIME detect ประเภทข้อมูลให้อัตโนมัติ:

| Column | Type ที่ถูกต้อง | สัญลักษณ์ |
|--------|----------------|----------|
| `Transaction_ID` | String | `S` |
| `Date` | String (แปลงทีหลัง) | `S` |
| `Product_Category` | String | `S` |
| `Product_Name` | String | `S` |
| `Quantity` | Integer | `I` |
| `Price_Per_Unit` | Double | `D` |
| `Total_Sales` | Double | `D` |
| `Region` | String | `S` |
| `Customer_Type` | String | `S` |

กด **OK** → **Execute**

**ตรวจสอบ:** คลิกขวา → **Data Table** → ตรวจสอบจำนวน rows และ columns

---

### Node 4: Row Filter

**ค้นหาใน Node Repository:** พิมพ์ `Row Filter` → เลือก **Row Filter** (สัญลักษณ์ลูกศรกรอง)

**เชื่อมต่อ:** Output ของ CSV Reader (Node 3) → Input ของ Row Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ส่วน Filter — Criterion 1:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Filter column** | `Total_Sales` | เลือก column ที่ใช้เป็นเงื่อนไขกรอง |
| **Operator** | `Greater than or equal` | มากกว่าหรือเท่ากับ |
| **Value** | `1000` | กรองเฉพาะธุรกรรมยอดขาย ≥ 1,000 บาท |

> ถ้าต้องการเพิ่มเงื่อนไขใหม่ → คลิกปุ่ม **⊕ Add criterion**

**ส่วน Output:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Column domains** | `Retain` (toggle) | เก็บ domain เดิมของ column ไว้ |
| **Filter behavior** | `Output matching rows` (toggle) | เอาเฉพาะแถวที่ผ่านเงื่อนไข |

> **ทำไมกรองที่ 1,000?** ตัดธุรกรรมมูลค่าน้อยที่ไม่มีนัยสำคัญออก เพื่อวิเคราะห์เฉพาะยอดขายที่มีผลต่อธุรกิจ

กด **Ok and Execute** (ปุ่มสีเหลือง)

**ตรวจสอบ:** จำนวน rows ลดลงเหลือเฉพาะ rows ที่มี Total_Sales ≥ 1,000

---

### Node 5: Column Filter

**ค้นหาใน Node Repository:** `Manipulation → Column → Filter → Column Filter`

**เชื่อมต่อ:** Output ของ Row Filter → Input ของ Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**Column filter — tabs ด้านบน:** `Manual` (เลือกอยู่) / Wildcard / Regex / Type

> มีช่อง **Search** 🔍 พร้อมปุ่ม **Aa** (case sensitive) ใช้ค้นหา column ที่ต้องการได้เลย

**ย้าย `Transaction_ID` ไปฝั่ง Excludes** โดยคลิกชื่อ column แล้วกดปุ่มลูกศร `>` หรือ `>>` (ย้ายทั้งหมด):

| Panel | Columns |
|-------|---------|
| **Excludes** | `Transaction_ID` |
| **Includes** | `Date`, `Product_Category`, `Product_Name`, `Quantity`, `Price_Per_Unit`, `Total_Sales`, `Region`, `Customer_Type` |

> `Any unknown column` (ตัวเอียง) ที่ท้าย Includes panel — ทิ้งไว้ตามเดิม เพื่อให้ columns ใหม่ในอนาคต include อัตโนมัติ

กด **Ok** (ปุ่มสีเหลือง)

**ตรวจสอบ:** Output table มี 8 columns — Transaction_ID หายไปแล้ว

---

### Node 6: Column Resorter

**ค้นหาใน Node Repository:** พิมพ์ `Column Resorter` → เลือก **Column Resorter**

**จุดประสงค์:** จัดลำดับ column ใหม่ให้อ่านง่ายและสื่อความหมายมากขึ้น — เอา Date และ Total_Sales ไว้ด้านหน้า

**เชื่อมต่อ:** Output ของ Column Filter → Input ของ Column Resorter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ปุ่มลัดด้านบน:**

| ปุ่ม | หน้าที่ |
|------|--------|
| **A - Z** | เรียง column ตามตัวอักษร A→Z |
| **Z - A** | เรียง column ตามตัวอักษร Z→A |
| **Reset all** | คืนค่าลำดับเดิม |

**Columns list — คลิก column แล้วใช้ปุ่มลูกศรด้านขวาล่างเพื่อเรียงลำดับ:**

| ปุ่ม | หน้าที่ |
|------|--------|
| `⇈` (ลูกศรขึ้นสุด) | ย้าย column ที่เลือกขึ้นไปอันดับแรก |
| `↓` (ลงสุด) | ย้าย column ที่เลือกลงไปอันดับสุดท้าย |
| `↑` | เลื่อนขึ้น 1 ช่อง |
| `↓` | เลื่อนลง 1 ช่อง |

**ลำดับ column ที่ต้องการ (จากบนลงล่าง):**

| ลำดับ | Column |
|-------|--------|
| 1 | `Date` |
| 2 | `Total_Sales` |
| 3 | `Product_Category` |
| 4 | `Product_Name` |
| 5 | `Quantity` |
| 6 | `Price_Per_Unit` |
| 7 | `Region` |
| 8 | `Customer_Type` |
| — | `Any unknown column` (ทิ้งไว้ท้ายสุด) |

กด **Ok** (ปุ่มสีเหลือง)

**ตรวจสอบ:** คลิกขวา → **Data Table** → ดู column order ว่า Date อยู่ซ้ายสุด

---

### Node 7: String Manipulation

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → String Manipulation`

**จุดประสงค์:** ทำความสะอาด column `Region` — ตัดช่องว่างที่ติดมาหน้า-หลัง (เช่น `" North"` → `"North"`)

**เชื่อมต่อ:** Output ของ Column Resorter → Input ของ String Manipulation

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ String Manipulation:**

> Dialog มี tabs: **String Manipulation** (เลือกอยู่) / Flow Variables / Job Manager Selection / Memory Policy

**Layout ของ Dialog:**
- **Column List** (แผงซ้ายบน) — รายชื่อ column ทั้งหมด: ROWID, ROWINDEX, ROWCOUNT, S Date, D Total_Sales, S Product_Category, ... double-click เพื่อแทรกใน Expression
- **Flow Variable List** (แผงซ้ายล่าง) — แสดง flow variables ที่ใช้ได้
- **Category / Function** (แผงกลาง) — เลือก Category = `All` → เลือก function จากรายการ
- **Expression** (กล่องด้านล่าง) — พื้นที่เขียน expression สีเหลือง

**พิมพ์ใน Expression:**
```
strip($Region$)
```

> **อธิบาย:** `strip()` ตัด whitespace (space, tab, newline) หน้า-หลัง string
> `$Region$` = อ้างอิงค่าใน column `Region` ของแต่ละแถว — double-click ชื่อ column จาก Column List เพื่อแทรกอัตโนมัติ

**ตัวเลือก Output ด้านล่าง Dialog:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Append Column** | ❌ ไม่เลือก | — |
| **Replace Column** | ✅ เลือก (radio button) | แทนที่ค่าใน column เดิมโดยตรง |
| **Replace Column dropdown** | `S Region` | เลือก column Region ที่จะถูกแทนที่ |
| **Insert Missing As Null** | ❌ ไม่ติ๊ก | ถ้า Region ว่าง ให้คืน empty string |
| **Syntax check on close** | ✅ ติ๊ก | ตรวจ syntax expression อัตโนมัติเมื่อปิด dialog |

กด **OK** → **Execute**

> **ผลที่ได้:** column `Region` ถูก clean ไม่มีช่องว่างเกินแล้ว — พร้อมสำหรับ GroupBy และ Visualization

---

## สาขาที่ 1: String Manipulation → Color Manager → Scatter Plot

### Node 8: Color Manager

**ค้นหาใน Node Repository:** `Views → Property → Color Manager`

**จุดประสงค์:** กำหนดสีให้แต่ละ `Product_Category` เพื่อให้ Scatter Plot แยกแยะกลุ่มได้ชัดเจน

**เชื่อมต่อ:** Output ของ String Manipulation → Input ของ Color Manager

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Color Settings:**

> Dialog มี tabs: **Color Settings** (เลือกอยู่) / Flow Variables / Job Manager Selection / Memory Policy

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Color by...** | `S Product_Category` | เลือก column ที่ใช้กำหนดสี จาก dropdown |
| **Mode** | `Nominal` (radio button) | เลือก Nominal — ข้อมูล categorical (ไม่ใช่ Range) |

**Color list** — แสดง category ทั้งหมดพร้อม color swatch — คลิก swatch เพื่อเปลี่ยนสี:

| Category | สีที่ปรากฏในภาพ |
|----------|----------------|
| `Books` | 🟩 เขียว |
| `Clothing` | 🟥 แดง |
| `Electronics` | 🟨 เหลือง/ส้ม |
| `Home & Kitchen` | 🟪 ม่วง |
| `Sports` | 🟦 น้ำเงิน |

**ส่วน Palettes ด้านล่าง** — เลือก preset palette หรือกำหนดเอง:

| Palette | ลักษณะ |
|---------|--------|
| Set 1 | สีสดใส มาตรฐาน |
| Set 2 | สีโทนอ่อน pastel |
| Set 3 (colorblind safe) | ปลอดภัยสำหรับผู้บกพร่องตาสี |
| **Custom** | ✅ เลือกอยู่ — กำหนดสีเองทีละ category |

> เพิ่มเติม: แท็บ **Swatches / HSV / HSL / RGB / CMYK / Alpha** ใช้เปลี่ยนค่าสีแบบ manual ได้ตามต้องการ

กด **OK** → **Execute**

**ตรวจสอบ:** node เป็นสีเขียว — สีถูก assign ให้ข้อมูลแล้ว (ไม่เห็นการเปลี่ยนแปลงใน table แต่ metadata สีถูกเพิ่มแล้ว)

---

### Node 9: Scatter Plot

**ค้นหาใน Node Repository:** `Views → Scatter Plot`

**จุดประสงค์:** แสดงความสัมพันธ์ระหว่าง `Price_Per_Unit` (ราคา) กับ `Quantity` (จำนวน) แยกสีตาม `Product_Category`

**เชื่อมต่อ:** Output ของ Color Manager → Input ของ Scatter Plot

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**Scatter Plot Dialog — แบบ Split Panel (ซ้าย = Preview, ขวา = Settings):**

**ส่วน Data (แผงขวา):**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Horizontal dimension** | `Price_Per_Unit` | แกน X — ราคาต่อหน่วย |
| **Vertical dimension** | `Quantity` | แกน Y — จำนวนที่ขาย |
| **Color dimension** | `Product_Category` | แยกสีตาม Category (ใช้สีจาก Color Manager อัตโนมัติ) |
| **Max rows** | `2500` | จำนวน data points สูงสุดที่แสดง |

**ส่วน Plot:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Title** | `Scatter Plot` | หัวข้อกราฟ |
| **Axis limits** | `Automatic` (radio) | กำหนดแกนอัตโนมัติ |
| **Custom horizontal axis label** | ❌ ไม่ติ๊ก | ใช้ชื่อ column เป็น label |
| **Horizontal axis scale** | `Linear` (radio) | สเกลเชิงเส้น |
| **Custom vertical axis label** | ❌ ไม่ติ๊ก | |
| **Vertical axis scale** | `Linear` (radio) | |
| **Point size** | `10` | ขนาดจุดข้อมูล |

**ส่วน Interactivity:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Enable image download** | ✅ ติ๊ก |
| **Enable zoom** | ✅ ติ๊ก |
| **Show tooltip** | ✅ ติ๊ก |
| **Selection** | `Edit` (toggle) |
| **Enable animation** | ✅ ติ๊ก |

**ส่วน Image Generation:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Generate image** | ❌ ไม่ติ๊ก |

กด **Ok** (ปุ่มสีเหลือง) → Execute → คลิกขวา → **View: Scatter Plot**

**วิเคราะห์ผล:**
- แต่ละจุดในกราฟ = 1 รายการขาย
- จุดสีเดียวกัน = Category เดียวกัน
- สังเกตว่า Electronics มีราคาสูงแต่จำนวนน้อย vs Books ราคาต่ำแต่จำนวนมาก

---

## สาขาที่ 2: String Manipulation → GroupBy → Math Formula → Bar Chart + Excel Writer

### Node 10: GroupBy

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → GroupBy`

**จุดประสงค์:** รวมยอดขายทั้งหมด จัดกลุ่มตาม `Product_Category` เพื่อดูว่า Category ไหนมียอดขายสูงสุด

**เชื่อมต่อ:** Output ของ String Manipulation → Input ของ GroupBy (แยกจาก Color Manager — เป็นสาขาใหม่)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**GroupBy Dialog — แท็บ Settings:**

> Dialog มี tabs: **Settings** / Description / Flow Variables / Job Manager Selection / Memory Policy
> ใต้ Settings มี inner tabs: **Groups** / Manual Aggregation / Pattern Based Aggregation / Type Based Aggregation

**Inner tab: Groups**

- **Available column(s)** (กรอบสีแดง ซ้าย): แสดงทุก column ที่ยังไม่ได้ group
- **Group column(s)** (กรอบสีเขียว ขวา): columns ที่ใช้แบ่งกลุ่ม
- ใช้ปุ่ม `>` / `>>` / `<` / `<<` ย้าย column ระหว่างสองฝั่ง

**ย้าย `Product_Category` ไปฝั่ง Group column(s):**

| Available column(s) | Group column(s) |
|--------------------|-----------------|
| Date, Total_Sales, Product_Name, Quantity, Price_Per_Unit, Region, Customer_Type | **S Product_Category** |

**Advanced settings (ด้านล่าง Groups tab):**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Column naming** | `Aggregation method (column name)` | ชื่อ output column = `Sum(Total_Sales)` อัตโนมัติ |
| **Enable hiliting** | ❌ ไม่ติ๊ก | |
| **Process in memory** | ❌ ไม่ติ๊ก | |
| **Retain row order** | ❌ ไม่ติ๊ก | |
| **Maximum unique values per group** | `10,000` | |
| **Value delimiter** | `,` | |

**Inner tab: Manual Aggregation — เพิ่ม Aggregation:**

| Column | Aggregation | Output Column Name |
|--------|-------------|-------------------|
| `Total_Sales` | `Sum` | `Sum(Total_Sales)` |

กด **OK** → **Execute**

**ผลลัพธ์ตัวอย่าง:**

| Product_Category | Sum(Total_Sales) |
|-----------------|-----------------|
| Books | 125,430.50 |
| Clothing | 287,920.00 |
| Electronics | 1,458,300.75 |
| Home & Kitchen | 342,100.25 |
| Sports | 198,540.00 |

---

### Node 11: Math Formula

**ค้นหาใน Node Repository:** `Manipulation → Column → Calculate → Math Formula`

**จุดประสงค์:** แปลงยอดขายจากหน่วยบาทเป็น **ล้านบาท** และปัดทศนิยม 2 ตำแหน่ง — ทำให้กราฟอ่านง่ายขึ้น

**เชื่อมต่อ:** Output ของ GroupBy → Input ของ Math Formula

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Math Expression:**

> Dialog มี tabs: **Math Expression** (เลือกอยู่) / Flow Variables / Job Manager Selection / Memory Policy

**Layout ของ Dialog:**
- **Column List** (แผงซ้าย): ROWINDEX, ROWCOUNT, D Sum(Total_Sales) — double-click เพื่อแทรกใน Expression
- **Flow Variable List** (แผงซ้ายล่าง): ว่างเปล่า
- **Category / Function** (แผงกลาง): เลือก `All` — มีฟังก์ชัน ROWCOUNT, ROWINDEX, pi, e, COL_MIN, COL_MAX, COL_MEAN, ...
- **Expression** (กล่องสีเหลืองด้านล่าง): พื้นที่เขียน expression

**พิมพ์ใน Expression:**
```
round($Sum(Total_Sales)$ / 1000000 , 2)
```

> **อธิบาย Expression:**
> - `$Sum(Total_Sales)$` = อ้างอิงค่าจาก column `Sum(Total_Sales)` ที่ได้จาก GroupBy (double-click จาก Column List เพื่อแทรก)
> - `/ 1000000` = หารด้วย 1 ล้าน เพื่อแปลงหน่วยเป็นล้านบาท
> - `round(..., 2)` = ปัดทศนิยม 2 ตำแหน่ง

**ตัวเลือก Output ด้านล่าง:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Append Column** | ✅ เลือก (radio button) | สร้าง column ใหม่เพิ่มเติม (ไม่ลบ column เดิม) |
| **Append Column name** | `ยอดขาย (ล้านบาท)` | ชื่อ column ใหม่ |
| **Replace Column** | ❌ ไม่เลือก | D Sum(Total_Sales) |
| **Convert to Int** | ❌ ไม่ติ๊ก | คงเป็น Double |

กด **OK** → **Execute**

**ผลลัพธ์ตัวอย่าง:**

| Product_Category | Sum(Total_Sales) | ยอดขาย (ล้านบาท) |
|-----------------|-----------------|-----------------|
| Books | 125,430.50 | 0.13 |
| Clothing | 287,920.00 | 0.29 |
| Electronics | 1,490,000.00 | 1.49 |
| Home & Kitchen | 100,000.00 | 0.10 |
| Sports | 70,000.00 | 0.07 |

---

### Node 12: Bar Chart

**ค้นหาใน Node Repository:** `Views → Bar Chart`

**จุดประสงค์:** แสดงยอดขายรายหมวดหมู่สินค้าเป็น Bar Chart แบบ interactive

**เชื่อมต่อ:** Output ของ Math Formula → Input ของ Bar Chart

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**Bar Chart Dialog — แบบ Split Panel (ซ้าย = Preview, ขวา = Settings):**

**ส่วน Data (แผงขวา):**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Category dimension** | `Product_Category` | แกน X — ชื่อหมวดหมู่ |
| **Aggregation** | `None` (radio) | ข้อมูลถูก aggregate แล้วจาก GroupBy — ไม่ต้อง aggregate ซ้ำ |
| **Frequency dimensions tabs** | `Manual` (เลือกอยู่) | เลือก column แบบ manual |
| **Excludes** | `Sum(Total_Sales)` | column ที่ไม่ต้องการแสดง |
| **Includes** | `ยอดขาย (ล้านบาท)` | ✅ column ที่ใช้เป็นแกน Y |
| **Limit rows** | `Automatic` (toggle) | ดึงข้อมูลอัตโนมัติ |

**ส่วน Plot:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Title** | `ยอดขายตามหมวดหมู่` | หัวข้อกราฟ |
| **Custom category axis label** | ✅ ติ๊ก → `หมวดหมู่` | ชื่อแกน X (กำหนดเอง) |
| **Custom frequency axis label** | ✅ ติ๊ก → `ยอดขายรวม (ล้านบาท)` | ชื่อแกน Y (กำหนดเอง) |
| **Frequency axis limits** | `Automatic` (radio) | |
| **Orientation** | `Vertical` (radio) | แท่งตั้ง |
| **Arrange bars** | `Grouped` (radio) | แท่งแยกกัน |
| **Bar groups** | `Frequency dimensions` (radio) | |
| **Display legend** | ✅ ติ๊ก | แสดง legend |
| **Show bar values** | ✅ ติ๊ก | แสดงตัวเลขบนยอดแท่ง |

**ส่วน Interactivity:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Enable image download** | ✅ ติ๊ก |
| **Show tooltip** | ✅ ติ๊ก |
| **Selection** | `Edit` (toggle) |
| **Enable animation** | ✅ ติ๊ก |

**ส่วน Image Generation:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Generate image** | ✅ ติ๊ก |
| **Width** | `800` |
| **Height** | `600` |

กด **Ok** (ปุ่มสีเหลือง) → Execute → คลิกขวา → **View: Bar Chart**

**สังเกตผล:** Electronics มียอดขายสูงสุด (1.49 ล้านบาท), Books ต่ำสุด (0.01 ล้านบาท)

---

### Node 13: Excel Writer

**ค้นหาใน Node Repository:** `IO → Write → Excel Writer`

**จุดประสงค์:** Export ผลสรุปยอดขายเป็นไฟล์ Excel สำหรับ Management Report

**เชื่อมต่อ:** Output ของ Math Formula → Input ของ Excel Writer (แยกสาขาจาก Bar Chart)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Settings:**

> Dialog มี tabs: **Settings** (เลือกอยู่) / Encryption / Flow Variables / Job Manager Selection

**ส่วน File format & output location:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Excel format** | `XLSX` | Excel 2007+ format |
| **Write to** | `Local File System` | เขียนลงเครื่อง local |
| **File** | `C:\Users\Samit\OneDrive\Desktop\sale_summary_report.xlsx` | Browse หรือพิมพ์ path โดยตรง |
| **Create missing folders** | ❌ ไม่ติ๊ก | folder ต้องมีอยู่แล้ว |
| **If exists** | `overwrite` (radio) | เขียนทับถ้าไฟล์มีอยู่แล้ว |

**ส่วน Sheets:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **1. sheet name** | `sumary` | ชื่อ Sheet ใน Excel (สังเกต: สะกด `sumary` — ตรงตาม workflow จริง) |

**ส่วน Names and IDs:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write row key** | ❌ ไม่ติ๊ก |
| **Write column headers** | ✅ ติ๊ก |
| **Don't write column headers if sheet exists** | ✅ ติ๊ก |

**ส่วน Missing value handling:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Replace missing values by** | ❌ ไม่ติ๊ก |

**ส่วน Layout:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Autosize columns** | ❌ ไม่ติ๊ก |
| **Orientation** | `Portrait` (radio) |
| **Page size** | `A4 - 210x297 mm` |
| **Open file after execution** | ❌ ไม่ติ๊ก |

กด **OK** → **Execute**

**ตรวจสอบ:** เปิด Desktop → ต้องเห็นไฟล์ `sale_summary_report.xlsx` มี Sheet ชื่อ "sumary" พร้อมข้อมูลยอดขายรายหมวดหมู่

---

## สรุป Lab 2 — Node ทั้งหมด 13 Node

| Node # | ชื่อ | หมวด | จุดประสงค์ |
|--------|------|------|-----------|
| 1 | Python Script | Scripting | สร้างข้อมูลทดสอบ 2M rows |
| 2 | CSV Reader | IO | อ่านไฟล์ทดสอบ 2M rows |
| 3 | CSV Reader | IO | อ่าน sales_data_workshop.csv |
| 4 | Row Filter | Manipulation | กรอง Total_Sales ≥ 1,000 |
| 5 | Column Filter | Manipulation | ตัด Transaction_ID ออก |
| 6 | Column Resorter | Manipulation | จัดลำดับ column |
| 7 | String Manipulation | Manipulation | Clean Region (strip whitespace) |
| 8 | Color Manager | Views | กำหนดสี Product_Category |
| 9 | Scatter Plot | Views | กราฟ Price vs Quantity แยกสี Category |
| 10 | GroupBy | Manipulation | Sum(Total_Sales) per Category |
| 11 | Math Formula | Manipulation | แปลงเป็นล้านบาท |
| 12 | Bar Chart | Views | กราฟยอดขายรายหมวดหมู่ |
| 13 | Excel Writer | IO | Export รายงาน Excel |


---

## 🧪 Lab 3: Day1_Lab3_Data_Import_EDA

**วัตถุประสงค์:** นำเข้าข้อมูลธุรกรรม ตรวจสอบ Schema และวิเคราะห์ Missing Values

**Workflow (3 Nodes):**
```
                    ┌──▶ Statistics (Node 2)
CSV Reader (Node 1) ─┤
                    └──▶ Missing Value Column Filter (Node 3)
```

> **สร้าง Workflow ใหม่:** `File → New Workflow... → ตั้งชื่อ "Day1_Lab3_Data_Import_EDA" → Create`

---

### Node 1: CSV Reader

**ค้นหา:** `IO → Read → CSV Reader` → ลากวางบน Canvas

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

> Dialog มี tabs: **Settings** / Transformation / Advanced Settings / Limit Rows / Encoding / Flow Variables / Job Manager Selection / Memory Policy

**ส่วน Input location:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Read from** | `Local File System` | dropdown |
| **Mode** | `File` (radio button) | อ่านทีละ 1 ไฟล์ |
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\financial_transactions.csv` | Browse หรือพิมพ์ path โดยตรง |

**ส่วน Reader options → Format:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Autodetect format** | คลิกปุ่ม (ไม่บังคับ) | ให้ KNIME ตรวจจับ format อัตโนมัติ |
| **Column delimiter** | `,` | ตัวคั่น column |
| **Row delimiter** | `Line break` (radio) | ขึ้นบรรทัดใหม่ |
| **Quote char** | `"` | ตัวครอบ text |
| **Quote escape char** | `"` | |
| **Comment char** | `#` | |
| **Has column header** | ✅ ติ๊ก | Row แรกเป็นชื่อ column |
| **Has RowID** | ❌ ไม่ติ๊ก | |
| **Support short data rows** | ❌ ไม่ติ๊ก | |

กด **OK** → **Execute**

**ส่วน Preview** (ด้านล่าง Dialog) — ตรวจสอบ columns ที่ KNIME ตรวจพบ:

| Column | Type | คำอธิบาย |
|--------|------|----------|
| `transaction_id` | S (String) | รหัสธุรกรรม |
| `customer_id` | S (String) | รหัสลูกค้า |
| `transaction_date` | S (String) | วันที่ (แปลงทีหลัง) |
| `transaction_time` | S (String) | เวลา |
| `transaction_type` | S (String) | ประเภทธุรกรรม |
| `amount` | D (Double) | จำนวนเงิน |
| `currency` | S (String) | สกุลเงิน |
| `merchant_category` | S (String) | หมวดหมู่ร้านค้า |
| `merchant_name` | S (String) | ชื่อร้านค้า |
| `account_type` | S (String) | ประเภทบัญชี |
| `branch_code` | S (String) | รหัสสาขา |
| `status` | S (String) | สถานะ |
| ... | ... | (columns เพิ่มเติม) |

**ตรวจสอบ:** คลิกขวา → **Data Table** → ดูข้อมูลที่โหลดมา

---

### Node 2: Statistics

**ค้นหา:** `Analytics → Statistics → Statistics`

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Statistics (ต่อสาย **บนสุด**)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

> Dialog มี tabs: **Options** / Histogram / Flow Variables / Job Manager Selection / Memory Policy

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Calculate median values** | ❌ ไม่ติ๊ก | การคำนวณ Median ใช้เวลานาน — ปิดไว้ก่อน |

**Nominal values section — เลือกโหมด:** `Manual Selection` (radio, เลือกอยู่)

**ย้าย columns ที่เป็น Numeric ไปฝั่ง Exclude** เพื่อไม่ให้คำนวณ Nominal stats ซ้ำ:

| Panel | Columns |
|-------|---------|
| **Exclude** (กรอบสีแดง) | `D amount`, `D balance_after` |
| **Includes** (กรอบสีเขียว) | `S transaction_id`, `S customer_id`, `S transaction_date`, `S transaction_time`, `S transaction_type`, `S currency`, `S merchant_category`, `S merchant_name`, `S account_type`, `S branch_code`, ... |

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Enforce exclusion / inclusion** | `Enforce inclusion` (radio, เลือกอยู่) |
| **Max no. of most frequent and infrequent values (in view)** | `20` |
| **Max no. of possible values per column (in output table)** | `1,000` |
| **Enable HiLite** | ❌ ไม่ติ๊ก |

กด **OK** → **Execute**

**ดูผลลัพธ์:** คลิกขวา → **Statistics View** → สังเกต:
- `amount`: Min / Max / Mean / Std Dev
- Missing Count ของแต่ละ column — column ไหนมีค่าว่าง?
- Nominal columns: ดู Most/Least frequent values

---

### Node 3: Missing Value Column Filter

> ⚠️ **ชื่อ Node ที่ถูกต้อง:** `Missing Value Column Filter` (**ไม่ใช่** Missing Value Filter หรือ Missing Value Handler)

**ค้นหา:** `Manipulation → Column → Filter → Missing Value Column Filter`

**เชื่อมต่อ:** Output ของ CSV Reader → Input ของ Missing Value Column Filter (ต่อสาย **ล่าง** — แยกจาก Statistics)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ส่วน Input columns:**

- Tabs: **Manual** (เลือกอยู่) / Wildcard / Regex / Type
- มีช่อง Search 🔍 พร้อมปุ่ม **Aa** (case sensitive)

**กำหนด columns ที่ต้องการ:**

| Panel | Columns | คำอธิบาย |
|-------|---------|----------|
| **Retained columns** (ซ้าย) | `amount`, `status`, `is_fraud` | columns ที่ **ไม่** ต้องการทดสอบ Missing — เก็บไว้เสมอ |
| **Columns to test** (ขวา) | `transaction_id`, `customer_id`, `transaction_date`, `transaction_time`, `transaction_type`, `currency`, ... + `Any unknown column` | columns ที่จะตรวจสอบ Missing Values |

> ใช้ปุ่ม `>` / `>>` / `<` / `<<` ย้าย column ระหว่าง 2 panel

**ส่วน Remove columns — เลือกเงื่อนไขการลบ column:**

| Radio option | เลือก | คำอธิบาย |
|-------------|-------|----------|
| With at least one missing value | ❌ | ลบ column ถ้ามีค่าว่าง **อย่างน้อย 1 แถว** |
| With only missing values | ❌ | ลบ column ถ้า **ทุกแถว** ว่างหมด |
| By percentage of missing values | ❌ | ลบตาม % ของค่าว่าง |
| **By number of missing values** | ✅ **เลือกอยู่** | ลบ column ถ้ามีค่าว่าง **มากกว่าหรือเท่ากับ threshold ที่กำหนด** |

**Threshold number (equal or more than):** `10`

> **อธิบาย:** จะลบ column ที่มีค่าว่าง ≥ 10 แถวออก — เพื่อตัด columns ที่ข้อมูลไม่สมบูรณ์ออกก่อนวิเคราะห์

กด **Ok** (ปุ่มสีเหลือง)

**ตรวจสอบ:** คลิกขวา → **Data Table** → Output table มี columns น้อยลงถ้ามี column ใดมีค่าว่าง ≥ 10 แถว

---

## 🧪 Lab 4: Day1_Lab4_Data_Cleaning

**วัตถุประสงค์:** แปลงประเภทข้อมูลของ transaction_date และ transaction_time ให้เป็น Date/Time จริง และสร้าง Column `risk_level` ด้วย Rule Engine

**Dataset:** `financial_transactions.csv` — 350 rows, 15 columns

**Workflow:**
```
CSV Reader (12:1) → String to Date&Time "Convert Transaction Date" (12:2)
                  → String to Date&Time "Convert Transaction Time" (12:3)
                  → Rule Engine (12:4)
```

---

### Node 1: CSV Reader (12:1)

**ค้นหา:** `IO → Read → CSV Reader`

ลากวางใน Workflow Editor แล้วดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Input location** | `C:\KNIME_MASTER\datasets\Mockup Resources\financial_transactions.csv` |
| **Mode** | File |
| **Column delimiter** | `,` (comma) |
| **Row delimiter** | Line break |
| **Quote char** | `"` |
| **Quote escape char** | `"` |
| **Comment char** | `#` |
| **Has column header** | ✅ ติ๊ก |
| **Has RowID** | ❌ ไม่ติ๊ก |
| **Support short data rows** | ❌ ไม่ติ๊ก |

กด **OK** → คลิกขวา → **Execute**

> **ผลลัพธ์:** Data Table แสดง **350 rows × 15 columns** — ทุก column เป็น String (ยังไม่แปลง)

---

### Node 2: String to Date&Time — "Convert Transaction Date" (12:2)

**ค้นหา:** `Date & Time → Transform → String to Date&Time`

ลากวาง ต่อสาย Output Port ของ CSV Reader เข้า Input Port

คลิกขวาที่ node → **Node Description** → ตั้งชื่อ node เป็น **"Convert Transaction Date"**

ดับเบิลคลิกเพื่อเปิด dialog → เลือกแท็บ **Options**

**ส่วน Column Selection — Manual Selection:**

- **Exclude panel (ซ้าย):** transaction_id, customer_id, transaction_time, transaction_type, currency, merchant_category, merchant_name, account_type, branch_code, status, channel, amount, is_fraud, balance_after
- **Include panel (ขวา — กล่องสีเขียว):** `S transaction_date`
- ✅ ติ๊ก **Enforce inclusion**

**ส่วน Replace/Append Selection:**

- 🔘 เลือก **Replace selected columns** (radio button)

**ส่วน Type and Format Selection:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **New type** | `Date` |
| **Date format** | `yyyy-MM-dd` |
| **Locale** | `en-US` |
| **Content of first cell** | `2024-03-12` (แสดงตัวอย่างอัตโนมัติ) |

> 💡 **Tip:** คลิกปุ่ม **Guess data type and format** เพื่อให้ KNIME วิเคราะห์รูปแบบวันที่ให้อัตโนมัติ

**ส่วน Abort Execution:**

- ✅ ติ๊ก **Fail on error** — หากมีค่าที่แปลงไม่ได้จะหยุดการทำงานทันที

กด **OK** → **Execute**

> **ผลลัพธ์:** Column `transaction_date` เปลี่ยน icon จาก **S** (String) เป็น 🗓 (Date)

---

### Node 3: String to Date&Time — "Convert Transaction Time" (12:3)

**ค้นหา:** `Date & Time → Transform → String to Date&Time`

ลากวาง ต่อสายจาก Output Port ของ Node 2

คลิกขวา → ตั้งชื่อ node เป็น **"Convert Transaction Time"**

ดับเบิลคลิกเพื่อเปิด dialog → แท็บ **Options**

**ส่วน Column Selection — Manual Selection:**

- **Exclude panel (ซ้าย):** transaction_id, customer_id, 🗓 transaction_date, transaction_type, currency, merchant_category, merchant_name, account_type, branch_code, status, channel, amount, is_fraud, balance_after
  > *สังเกต:* transaction_date มี icon 🗓 แล้ว (แปลงใน Node 2 แล้ว)
- **Include panel (ขวา — กล่องสีเขียว):** `S transaction_time`
- ✅ ติ๊ก **Enforce inclusion**

**ส่วน Replace/Append Selection:**

- 🔘 เลือก **Replace selected columns** (radio button)

**ส่วน Type and Format Selection:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **New type** | `Time` |
| **Date format** | `HH:mm:ss` |
| **Locale** | `en-US` |
| **Content of first cell** | `23:06:43` (แสดงตัวอย่างอัตโนมัติ) |

**ส่วน Abort Execution:**

- ✅ ติ๊ก **Fail on error**

กด **OK** → **Execute**

> **ผลลัพธ์:** Column `transaction_time` เปลี่ยน icon จาก **S** (String) เป็น ⏰ (Time)

---

### Node 4: Rule Engine (12:4)

**ค้นหา:** `Manipulation → Column → Calculate → Rule Engine`

ลากวาง ต่อสายจาก Output Port ของ Node 3

ดับเบิลคลิกเพื่อเปิด dialog → แท็บ **Rule Editor**

**Column List (ด้านซ้าย) — ตัวอย่าง columns ที่มี:**

| Icon | Column |
|------|--------|
| — | ROWID, ROWINDEX, ROWCOUNT |
| S | transaction_id, customer_id |
| 🗓 | transaction_date |
| ⏰ | transaction_time |
| S | transaction_type |
| D | amount |
| S | currency, merchant_category, merchant_name |
| S | account_type, branch_code, status, channel |
| D | is_fraud, balance_after |

**กรอก Expression (4 rules) ในช่อง Rule Editor:**

```
$amount$ > 30000 AND $is_fraud$ = 1 => "HIGH RISK"
$amount$ > 10000 AND $is_fraud$ = 1 => "MEDIUM RISK"
$is_fraud$ = 1 => "LOW RISK"
TRUE => "NORMAL"
```

> 💡 **วิธีกรอก:** พิมพ์ทีละบรรทัด กด Enter ต่อบรรทัด หรือดับเบิลคลิก column ใน Column List เพื่อแทรกชื่อ column อัตโนมัติ

**ส่วน Output Column:**

| ตัวเลือก | ค่าที่ตั้ง |
|----------|-----------|
| 🔘 **Append Column** | `risk_level` — ✅ เลือกตัวนี้ |
| ○ Replace Column | `is_fraud` — ❌ ไม่เลือก |

> *Output column* `risk_level` จะมี icon **S** (String type)

กด **OK** → **Execute**

> **ผลลัพธ์:** Data Table แสดง **350 rows × 16 columns** — Column สุดท้ายคือ `risk_level` ที่มีค่า "HIGH RISK" / "MEDIUM RISK" / "LOW RISK" / "NORMAL"

---

### ✅ ตรวจสอบผลลัพธ์ Lab 4

คลิกขวาที่ Rule Engine → **Data Table** → ตรวจสอบ:

1. **จำนวน rows/columns:** 350 rows × 16 columns
2. **Column transaction_date:** icon 🗓 (Date type) — ไม่ใช่ S (String)
3. **Column transaction_time:** icon ⏰ (Time type) — ไม่ใช่ S (String)
4. **Column risk_level:** S (String) มีค่า "HIGH RISK", "MEDIUM RISK", "LOW RISK", หรือ "NORMAL"
5. **ตัวอย่างข้อมูล:** row ส่วนใหญ่ควรแสดง `risk_level = "NORMAL"` เนื่องจาก is_fraud = 0

---

## 🧪 Lab 5: Day1_Lab5_Filtering

**วัตถุประสงค์:** กรองข้อมูล และคอลัมน์ที่ต้องการ

**Dataset:** ต่อเนื่องจาก Lab 4 — 350 rows, 16 columns (มี risk_level แล้ว)

**Workflow:**
```
CSV Reader → String to Date&Time (Convert Transaction Date)
           → String to Date&Time (Convert Transaction Time)
           → Rule Engine → Row Filter (13:5) → Row Splitter (13:6) → CSV Writer (13:7)
```

> 💡 **หมายเหตุ:** Lab 5 ใช้ Workflow เดียวกับ Lab 4 โดยต่อ nodes เพิ่มเติมจาก Rule Engine ออกไป

---

### Node 5: Row Filter (13:5)

**ค้นหา:** `Manipulation → Row → Filter → Row Filter`

ลากวาง ต่อสายจาก Output Port ของ Rule Engine

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Filter — "Match row if matched by":**

เลือก **All criteria** (toggle button — ไม่ใช่ "Any criterion")
> *All criteria = ทุก Criterion ต้องผ่าน (AND logic)*

**Criterion 1 — กรอง status:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Filter column** | `status` |
| **Operator** | `Equals` |
| **Case matching** | **Case sensitive** (toggle button — ไม่ใช่ "Case insensitive") |
| **Value** | `Completed` |

คลิก **➕ Add criterion** เพื่อเพิ่ม Criterion 2

**Criterion 2 — กรอง amount:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Filter column** | `amount` |
| **Operator** | `Greater than or equal` |
| **Value** | `20000` |

**ส่วน Output:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column domains** | **Retain** (toggle button) |
| **Filter behavior** | **Output matching rows** (toggle button) |

กด **OK** → **Execute**

> **ผลลัพธ์:** เหลือเฉพาะ rows ที่ status = "Completed" **และ** amount ≥ 20,000

---

### Node 6: Row Splitter (13:6)

**ค้นหา:** `Manipulation → Row → Filter → Row Splitter`

ลากวาง ต่อสายจาก Output Port ของ Row Filter

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Filter — Criterion 1:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Filter column** | `is_fraud` |
| **Operator** | `Equals` |
| **Value** | `1` |

**ส่วน Output:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column domains** | **Retain** (toggle button) |
| **Splitting behavior** | 🔘 **Matching rows at first output, non-matching at second output** |

> *ตัวเลือกที่ไม่เลือก:* ○ Non-matching rows at first output, matching at second output

กด **OK** → **Execute**

> **ผลลัพธ์:**
> - **Output Port บน (Top)** = ธุรกรรม Fraud (is_fraud = 1)
> - **Output Port ล่าง (Bottom)** = ธุรกรรมปกติ (is_fraud = 0)

---

### Node 7: CSV Writer (13:7)

**ค้นหา:** `IO → Write → CSV Writer`

ลากวาง ต่อสายจาก **Output Port บน (Top)** ของ Row Splitter

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings:**

**ส่วน Output location:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write to** | `Local File System` |
| **File** | `C:\Users\Samit\Desktop\audit_high_risk_transactions.csv` |
| **Create missing folders** | ❌ ไม่ติ๊ก |
| **If exists** | ◉ **overwrite** (radio button) |

> ⚠️ หาก file มีอยู่แล้ว KNIME จะแสดง warning: *"There exists a file with the specified path ... that will be overwritten"* — ถือเป็นปกติ

**ส่วน Format:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column Delimiter** | `,` (comma) |
| **Row Delimiter** | `System Default` |
| **Quote Char** | `"` |
| **Quote Escape Char** | `"` |

**ส่วน Header:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write column header** | ✅ ติ๊ก |
| **Don't write column headers if file exists** | ❌ ไม่ติ๊ก |
| **Write RowID** | ❌ ไม่ติ๊ก |

กด **OK** → **Execute**

> **ผลลัพธ์:** ไฟล์ `audit_high_risk_transactions.csv` ถูกบันทึกที่ Desktop ของ Samit

---

### ✅ ตรวจสอบผลลัพธ์ Lab 5

1. **Row Filter:** คลิกขวา → **Data Table** — ตรวจสอบว่า column `status` ทุก row = "Completed" และ `amount` ≥ 20,000
2. **Row Splitter Top Output:** คลิกขวา → **Top Data Table** — ตรวจสอบว่า `is_fraud` = 1 ทุก row
3. **Row Splitter Bottom Output:** คลิกขวา → **Bottom Data Table** — ตรวจสอบว่า `is_fraud` = 0 ทุก row
4. **CSV Writer:** ตรวจสอบว่าไฟล์ `audit_high_risk_transactions.csv` ปรากฏบน Desktop

---

## 🧪 Lab 6: Day1_Lab6_Join_Aggregation

**วัตถุประสงค์:** การ Group และทำ Pivot ข้อมูล

**Dataset:** `financial_transactions.csv` (350 rows, 16 columns) + `sales_data.csv`

**Workflow:**
```
CSV Reader (financial_transactions.csv) → String to Date&Time (Convert Transaction Date)
    → String to Date&Time (Convert Transaction Time) → Rule Engine → Row Filter
    → Column Filter ──┬──→ GroupBy (14:8) — สรุปรายสาขา
                      ├──→ GroupBy (14:9) — สรุปรายหมวดหมู่ + Fraud
                      └──→ Pivot (14:10) — Cross-tab

CSV Reader (sales_data.csv) ─┐
                              └──→ Joiner (14:12) → CSV Writer (14:13)
CSV Reader (financial_transactions.csv) ─┘
```

> 💡 **หมายเหตุ:** Nodes ตั้งแต่ CSV Reader → Rule Engine ตั้งค่าเหมือนกับ Lab 4 ทุกประการ  
> Row Filter ตั้งค่าเหมือน Lab 5 (status = "Completed" AND amount ≥ 20,000)

---

### Node: Column Filter

**ค้นหา:** `Manipulation → Column → Filter → Column Filter`

ลากวาง ต่อสายจาก Output Port ของ Row Filter

ดับเบิลคลิกเพื่อเปิด dialog → ย้าย column ที่ไม่ต้องการออกไปฝั่ง **Excludes:**

| Excludes (ลบออก) | Includes (เก็บไว้) |
|------------------|-------------------|
| `currency` | transaction_id, customer_id |
| `channel` | transaction_date, transaction_time |
| `balance_after` | transaction_type, amount |
| | merchant_category, merchant_name |
| | account_type, branch_code, status |
| | is_fraud, risk_level |

กด **OK** → **Execute**

> ต่อ Output Port ของ Column Filter ออกไป 3 สาย → GroupBy (14:8), GroupBy (14:9), Pivot (14:10)

---

### Node: GroupBy (14:8) — สรุปรายสาขา

**ค้นหา:** `Manipulation → Row → Transform → GroupBy`

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings → แท็บย่อย Groups:**

**Group settings:**

- **Available column(s) (กล่องแดง — ซ้าย):** transaction_id, customer_id, 🗓 transaction_date, ⏰ transaction_time, S transaction_type, D amount, S merchant_category, S merchant_name, S account_type, S status, I is_fraud, S risk_level
- **Group column(s) (กล่องเขียว — ขวา):** `S branch_code`

> เลือก `branch_code` จากกล่องซ้าย กด **`>`** เพื่อย้ายไปกล่องขวา

**Advanced settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column naming** | `Aggregation method (column name)` |
| **Enable hiliting** | ❌ |
| **Process in memory** | ❌ |
| **Retain row order** | ❌ |
| **Maximum unique values per group** | `10,000` |
| **Value delimiter** | `,` |

**แท็บย่อย Manual Aggregation:**

คลิก `add >>` หรือดับเบิลคลิก column ใน Available columns เพื่อเพิ่ม → ตั้งค่า Aggregation ดังนี้:

| Column | Aggregation (คลิกเพื่อเปลี่ยน) | Missing |
|--------|--------------------------------|---------|
| `D amount` | `Sum` | ❌ |
| `D amount` | `Mean` | ❌ |
| `S transaction_id` | `Count` | ✅ ติ๊ก |
| `I is_fraud` | `Sum` | ❌ |

> 💡 **วิธีเพิ่ม column เดิมซ้ำ:** เลือก amount ใน Available columns แล้วกด `add >>` สองครั้ง → คลิกชื่อ Aggregation เพื่อเปลี่ยน (Sum / Mean)

กด **OK** → **Execute**

> **ผลลัพธ์:** ตารางสรุปยอดรายสาขา — จำนวน rows = จำนวน branch_code ที่ไม่ซ้ำกัน

---

### Node: GroupBy (14:9) — สรุปรายหมวดหมู่ + Fraud

**ค้นหา:** `Manipulation → Row → Transform → GroupBy`

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings → แท็บย่อย Groups:**

- **Available column(s) (กล่องแดง):** transaction_id, customer_id, 🗓 transaction_date, ⏰ transaction_time, S transaction_type, D amount, S merchant_name, S account_type, S branch_code, S status, S risk_level
- **Group column(s) (กล่องเขียว):** `S merchant_category`, `I is_fraud`

> ย้าย merchant_category และ is_fraud ไปกล่องขวา

**Advanced settings:** เหมือน GroupBy (14:8) ทุกประการ

**แท็บย่อย Manual Aggregation:**

| Column | Aggregation | Missing |
|--------|-------------|---------|
| `D amount` | `Sum` | ❌ |
| `S transaction_id` | `Count` | ✅ ติ๊ก |

กด **OK** → **Execute**

> **ผลลัพธ์:** ตารางสรุปยอดและจำนวน Transaction แยกตาม merchant_category และ is_fraud

---

### Node: Pivot (14:10)

**ค้นหา:** `Manipulation → Row → Transform → Pivot`

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings → แท็บย่อย Groups:**

- **Available column(s) (กล่องแดง):** transaction_id, customer_id, 🗓 transaction_date, ⏰ transaction_time, S transaction_type, D amount, S merchant_category, S merchant_name, S account_type, S status, I is_fraud, S risk_level
- **Group column(s) (กล่องเขียว):** `S branch_code`

**Advanced settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column name** | `Pivot name+Aggregation name` |
| **Aggregation name** | `Aggregation method (column name)` |
| **Sort lexicographically** | ❌ |
| **Maximum unique values per group** | `10,000` |
| **Value delimiter** | `,` |
| **Process in memory** | ❌ |
| **Retain row order** | ❌ |
| **Enable hiliting** | ❌ |

**แท็บย่อย Pivots:**

- **Available column(s) (กล่องแดง):** transaction_id, customer_id, 🗓 transaction_date, ⏰ transaction_time, D amount, S merchant_category, S merchant_name, S account_type, S branch_code, S status, I is_fraud, S risk_level
- **Pivot column(s) (กล่องเขียว):** `S transaction_type`

**ส่วนล่างของแท็บ Pivots:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Ignore missing values** | ✅ ติ๊ก |
| **Append overall totals** | ❌ ไม่ติ๊ก |
| **Ignore domain** | ✅ ติ๊ก |

**แท็บย่อย Manual Aggregation:**

| Column | Aggregation | Missing |
|--------|-------------|---------|
| `D amount` | `Sum` | ❌ |

กด **OK** → **Execute**

> **ผลลัพธ์:** ตาราง Cross-tab — แถวคือ branch_code, คอลัมน์คือ transaction_type แต่ละประเภท (แสดงยอด Sum ของ amount)

---

### Node: CSV Reader (14:11) — อ่าน sales_data.csv

**ค้นหา:** `IO → Read → CSV Reader`

ลากวางใน Workflow Editor — **node แยกต่างหาก** (ไม่ต่อสายจาก main pipeline)

ดับเบิลคลิกเพื่อเปิด dialog → ตั้งชื่อ node เป็น **"Read sales_data.csv"**

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Read from** | `Local File System` |
| **Mode** | ◉ **File** |
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\sales_data.csv` |
| **Column delimiter** | `,` |
| **Row delimiter** | ◉ **Line break** |
| **Quote char** | `"` |
| **Quote escape char** | `"` |
| **Comment char** | `#` |
| **Has column header** | ✅ ติ๊ก |
| **Has RowID** | ❌ ไม่ติ๊ก |
| **Support short data rows** | ❌ ไม่ติ๊ก |

> **Preview** แสดงตัวอย่างข้อมูล: order_id, customer_id, product_id, product_name, category, quantity, unit_price, discount, total_amount, order_date, delivery_date, region

กด **OK** → **Execute**

---

### Node: Joiner (14:12)

**ค้นหา:** `Manipulation → Row → Transform → Joiner`

ลากวาง → ต่อสาย:
- **Top input (left table):** Output Port ของ CSV Reader (financial_transactions.csv — node แรก)
- **Bottom input (right table):** Output Port ของ CSV Reader (sales_data.csv — 14:11)

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Matching Criteria:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Match** | **All of the following** (toggle button) |
| **Top input ('left' table)** | `RowID` |
| **Bottom input ('right' table)** | `RowID` |
| **Compare values in join columns by** | `Value and type` |

> 💡 ค่า default คือ RowID = RowID — เป็น Inner Join แบบ Row index matching

**ส่วน Include in Output:**

| ตัวเลือก | ค่า |
|----------|-----|
| ✅ **Matching rows** | รวม rows ที่ match (Inner Join) |
| ❌ **Left unmatched rows** | ไม่รวม rows ที่ไม่ match จาก left table |
| ❌ **Right unmatched rows** | ไม่รวม rows ที่ไม่ match จาก right table |

> *Join type diagram แสดงเป็น Inner Join (วงกลมกลาง)*

**ส่วน Output Columns — Top input ('left' table):**

- Selection mode: **Manual** (toggle)
- **Excludes:** (ว่าง — No columns in this list)
- **Includes:** transaction_id, customer_id, transaction_date, transaction_time, transaction_type, amount, ... Any unknown column

**ส่วน Output Columns — Bottom input ('right' table):**

- Selection mode: **Manual** (toggle)
- **Excludes:** (ว่าง — No columns in this list)
- **Includes:** order_id, customer_id, product_id, product_name, category, quantity, ... Any unknown column

**ส่วนล่าง:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Merge join columns** | ❌ ไม่ติ๊ก |
| **If there are duplicate column names** | ◉ **Append custom suffix** |
| **Custom suffix** | `(Right)` |

กด **OK** → **Execute**

> **ผลลัพธ์:** ตาราง Join ระหว่าง financial_transactions และ sales_data — columns ที่ซ้ำกัน (เช่น customer_id) จาก right table จะมี suffix `(Right)` ต่อท้าย

---

### Node: CSV Writer (14:13)

**ค้นหา:** `IO → Write → CSV Writer`

ลากวาง ต่อสายจาก Output Port ของ Joiner

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings:**

**ส่วน Output location:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write to** | `Local File System` |
| **File** | `C:\Users\Samit\Desktop\audit_report.csv` |
| **Create missing folders** | ❌ ไม่ติ๊ก |
| **If exists** | ◉ **overwrite** |

> ⚠️ หาก file มีอยู่แล้ว KNIME แสดง warning: *"There exists a file with the specified path ... that will be overwritten"* — ถือเป็นปกติ

**ส่วน Format:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column Delimiter** | `,` |
| **Row Delimiter** | `System Default` |
| **Quote Char** | `"` |
| **Quote Escape Char** | `"` |

**ส่วน Header:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write column header** | ✅ ติ๊ก |
| **Don't write column headers if file exists** | ❌ ไม่ติ๊ก |
| **Write RowID** | ❌ ไม่ติ๊ก |

กด **OK** → **Execute**

> **ผลลัพธ์:** ไฟล์ `audit_report.csv` ถูกบันทึกที่ Desktop ของ Samit

---

### ✅ ตรวจสอบผลลัพธ์ Lab 6

1. **GroupBy (14:8):** คลิกขวา → **Data Table** — ตรวจสอบ columns: branch_code, Sum(amount), Mean(amount), Count(transaction_id), Sum(is_fraud)
2. **GroupBy (14:9):** ตรวจสอบ columns: merchant_category, is_fraud, Sum(amount), Count(transaction_id)
3. **Pivot (14:10):** ตรวจสอบว่า columns แสดงชื่อ transaction_type แต่ละประเภทเป็น header
4. **Joiner (14:12):** ตรวจสอบว่า columns จาก sales_data ปรากฏต่อท้าย — customer_id ซ้ำจะมี `customer_id (Right)`
5. **CSV Writer:** ตรวจสอบว่าไฟล์ `audit_report.csv` ปรากฏบน Desktop

---

## 🧪 Lab 7: Day1_Lab7_Audit_Dashboard

**วัตถุประสงค์:** สร้าง End-to-End Workflow ครบกระบวนการสำหรับรายงาน Audit

**Dataset:** `financial_transactions.csv` — 350 rows, 15 columns

**Workflow สมบูรณ์:**
```
CSV Reader (15:1)
        │
        ▼
String to Date&Time (15:2)
(transaction_date → Date, format: yyyy-MM-dd)
        │
        ▼
Missing Value (15:3)
(merchant_category → "Uncategorized"; merchant_name → "Unknown"
 amount → Mean; balance_after → Mean)
        │
        ▼
Rule Engine (15:4)
(สร้าง risk_level: HIGH RISK / MEDIUM RISK / LOW RISK / NORMAL)
        │
        ▼
Row Filter (15:5)
(status = "Completed" AND amount ≥ 5,000)
        │
   ┌────┴──────────────────────────┐
   ▼                               ▼
GroupBy (15:6)               Row Splitter (15:9)
(Group: branch_code)          (is_fraud = 1)
   │                               │
   ├──→ Normalizer (15:7)     Column Filter (15:10)
   │    (Min-max normalize)         │
   └──→ Bar Chart (15:8)      CSV Writer (15:11)
        (ยอดรวมรายสาขา)       (audit_fraud_2569Q2.csv)
```

---

### Node 15:1 — CSV Reader

**ค้นหา:** `IO → Read → CSV Reader`

ลากวางใน Workflow Editor แล้วดับเบิลคลิก → ตั้งชื่อ node เป็น **"Read financial_transactions.csv"**

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Read from** | `Local File System` |
| **Mode** | ◉ **File** |
| **File** | `C:\KNIME_MASTER\datasets\Mockup Resources\financial_transactions.csv` |
| **Column delimiter** | `,` |
| **Row delimiter** | ◉ **Line break** |
| **Quote char** | `"` |
| **Quote escape char** | `"` |
| **Comment char** | `#` |
| **Has column header** | ✅ ติ๊ก |
| **Has RowID** | ❌ ไม่ติ๊ก |
| **Support short data rows** | ❌ ไม่ติ๊ก |

> **Preview** แสดงตัวอย่างข้อมูล — สังเกตว่า transaction_date, transaction_time เป็น S (String) ทั้งคู่ก่อนแปลง

กด **OK** → **Execute**

---

### Node 15:2 — String to Date&Time

**ค้นหา:** `Date & Time → Transform → String to Date&Time`

ลากวาง ต่อสายจาก Output Port ของ CSV Reader

ดับเบิลคลิกเพื่อเปิด dialog → แท็บ **Options**

**ส่วน Column Selection — Manual Selection:**

- **Exclude panel (กล่องแดง — ซ้าย):** transaction_id, customer_id, transaction_time, transaction_type, currency, merchant_category, merchant_name, account_type, branch_code, status, channel, ...
- **Include panel (กล่องเขียว — ขวา):** `S transaction_date`
- ◉ **Enforce inclusion**

**ส่วน Replace/Append Selection:**
- ◉ **Replace selected columns**

**ส่วน Type and Format Selection:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **New type** | `Date` |
| **Date format** | `yyyy-MM-dd` |
| **Locale** | `en-US` |
| **Content of first cell** | `2024-03-12` |

**ส่วน Abort Execution:**
- ✅ **Fail on error**

กด **OK** → **Execute**

> **หมายเหตุ:** Lab 7 แปลงเฉพาะ `transaction_date` เท่านั้น (ไม่แปลง transaction_time)

---

### Node 15:3 — Missing Value

**ค้นหา:** `Manipulation → Column → Transform → Missing Value`

ลากวาง ต่อสายจาก Output Port ของ String to Date&Time

ดับเบิลคลิกเพื่อเปิด dialog → เลือกแท็บ **Column Settings**

**วิธีเพิ่ม column settings:**
คลิกปุ่ม **Add** → เลือก column ที่ต้องการจาก Column Search → ตั้งวิธีจัดการ

**การตั้งค่าแต่ละ column (กล่องขวา — Column Settings panel):**

| Column | วิธีจัดการ | ค่าที่กรอก |
|--------|-----------|-----------|
| `S merchant_category` | **Fix Value** | `Uncategorized` |
| `D amount` | **Mean** | — (คำนวณอัตโนมัติ) |
| `D balance_after` | **Mean** | — (คำนวณอัตโนมัติ) |
| `S merchant_name` | **Fix Value** | `Unknown` |

> 💡 **สังเกต:** column ที่มีตัวหนา (bold) ใน Column list ด้านซ้าย = column ที่มี missing values ในข้อมูล

กด **OK** → **Execute**

---

### Node 15:4 — Rule Engine

**ค้นหา:** `Manipulation → Column → Calculate → Rule Engine`

ลากวาง ต่อสายจาก Output Port ของ Missing Value

ดับเบิลคลิกเพื่อเปิด dialog → แท็บ **Rule Editor**

**กรอก Expression (4 rules):**

```
$amount$ > 30000 AND $is_fraud$ = 1 => "HIGH RISK"
$amount$ > 10000 AND $is_fraud$ = 1 => "MEDIUM RISK"
$is_fraud$ = 1 => "LOW RISK"
TRUE => "NORMAL"
```

**ส่วน Output Column:**

- ◉ **Append Column:** `risk_level`
- ○ Replace Column: `is_fraud` — ❌ ไม่เลือก

กด **OK** → **Execute**

---

### Node 15:5 — Row Filter

**ค้นหา:** `Manipulation → Row → Filter → Row Filter`

ลากวาง ต่อสายจาก Output Port ของ Rule Engine

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Filter — "Match row if matched by":**

เลือก **All criteria** (AND logic)

**Criterion 1 — กรอง status:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Filter column** | `status` |
| **Operator** | `Equals` |
| **Case matching** | **Case sensitive** (toggle) |
| **Value** | `Completed` |

คลิก **➕ Add criterion** → เพิ่ม Criterion 2

**Criterion 2 — กรอง amount:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Filter column** | `amount` |
| **Operator** | `Greater than or equal` |
| **Value** | `5000` |

**ส่วน Output:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column domains** | **Retain** |
| **Filter behavior** | **Output matching rows** |

กด **OK** → **Execute**

> ต่อ Output Port ของ Row Filter ออก 2 สาย: → GroupBy (15:6) และ → Row Splitter (15:9)

---

### Node 15:6 — GroupBy

**ค้นหา:** `Manipulation → Row → Transform → GroupBy`

ลากวาง ต่อสายจาก Output Port ของ Row Filter

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings → แท็บย่อย Groups:**

- **Available column(s) (กล่องแดง):** transaction_id, customer_id, 🗓 transaction_date, S transaction_time, S transaction_type, D amount, S currency, S merchant_category, S merchant_name, S account_type, S status, D balance_after, S channel, I is_fraud, S risk_level
- **Group column(s) (กล่องเขียว):** `S branch_code`

**Advanced settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column naming** | `Aggregation method (column name)` |
| **Enable hiliting** | ❌ |
| **Process in memory** | ❌ |
| **Retain row order** | ❌ |
| **Maximum unique values per group** | `10,000` |
| **Value delimiter** | `,` |

**แท็บย่อย Manual Aggregation:**

| Column | Aggregation | Missing |
|--------|-------------|---------|
| `D amount` | `Sum` | ❌ |
| `D amount` | `Mean` | ❌ |
| `S transaction_id` | `Count` | ✅ ติ๊ก |
| `I is_fraud` | `Sum` | ❌ |

กด **OK** → **Execute**

> ต่อ Output Port ของ GroupBy ออก 2 สาย: → Normalizer (15:7) และ → Bar Chart (15:8)

---

### Node 15:7 — Normalizer

**ค้นหา:** `Manipulation → Column → Transform → Normalizer`

ลากวาง ต่อสายจาก Output Port ของ GroupBy

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Column Selection — Manual** (toggle button):

- **Excludes:** (ว่าง — No columns in this list)
- **Includes:** Sum(amount), Mean(amount), Count(transaction_id), Sum(is_fraud), Any unknown column

**ส่วน Normalization method:**

เลือก **Min-max** (toggle button)

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Minimum** | `0` |
| **Maximum** | `1` |

กด **Ok and Execute**

> **ผลลัพธ์:** ค่า Sum(amount), Mean(amount), Count(transaction_id), Sum(is_fraud) ถูก normalize ให้อยู่ในช่วง [0, 1]

---

### Node 15:8 — Bar Chart (ยอดรวมรายสาขา)

**ค้นหา:** `Views → JavaScript → Bar Chart`

ลากวาง ต่อสายจาก Output Port ของ GroupBy (ไม่ใช่จาก Normalizer)

> 💡 ต่อจาก GroupBy โดยตรง เพื่อให้ Bar Chart แสดงค่า **ยอดจริง** ไม่ใช่ค่า Normalized

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Data:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Category dimension** | `branch_code` |
| **Aggregation** | ◉ **Sum** |

**Frequency dimensions — Manual** (toggle):
- **Excludes:** Mean(amount), Count(transaction_id), Sum(is_fraud), Any unknown column
- **Includes:** `Sum(amount)`

**ส่วน Plot:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Title** | `ยอดธุรกรรมรวมรายสาขา — Audit Report 2569` |
| **Custom category axis label** | ✅ ติ๊ก → `สาขา` |
| **Custom frequency axis label** | ✅ ติ๊ก → `ยอดธุรกรรม (บาท)` |
| **Frequency axis limits** | ◉ **Automatic** |
| **Orientation** | ◉ **Vertical** |
| **Arrange bars** | ◉ **Grouped** |
| **Bar groups** | ◉ **Frequency dimensions** |
| **Display legend** | ✅ ติ๊ก |
| **Show bar values** | ✅ ติ๊ก |

**ส่วน Interactivity:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Enable image download** | ✅ ติ๊ก |
| **Show tooltip** | ✅ ติ๊ก |
| **Selection** | **Edit** (toggle) |
| **Enable animation** | ✅ ติ๊ก |

กด **OK** → **Execute** → คลิกขวา → **View: Bar Chart**

> **ผลลัพธ์:** กราฟแท่งแสดงยอดธุรกรรมรวมของแต่ละสาขา (BKK001, BKK002, BKK003, CMI001, CNX001, HYI001, KKN001, ...)

---

### Node 15:9 — Row Splitter (แยก Fraud)

**ค้นหา:** `Manipulation → Row → Filter → Row Splitter`

ลากวาง ต่อสายจาก Output Port ของ Row Filter (15:5)

ดับเบิลคลิกเพื่อเปิด dialog → ตั้งชื่อ node เป็น **"Row Splitter — แยก Fraud"**

**ส่วน Filter — Criterion 1:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Filter column** | `is_fraud` |
| **Operator** | `Equals` |
| **Value** | `1` |

**ส่วน Output:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column domains** | **Retain** |
| **Splitting behavior** | ◉ **Matching rows at first output, non-matching at second output** |

กด **OK** → **Execute**

---

### Node 15:10 — Column Filter (ก่อน Export)

**ค้นหา:** `Manipulation → Column → Filter → Column Filter`

ลากวาง ต่อสายจาก **Top Output Port** (Fraud rows) ของ Row Splitter

ดับเบิลคลิกเพื่อเปิด dialog → ตั้งชื่อ node เป็น **"Column Filter — ก่อน Export"**

**Column filter — Manual** (toggle button):

| Excludes (ลบออก) | Includes (เก็บไว้) |
|------------------|-------------------|
| `transaction_time` | `transaction_id` |
| `currency` | `customer_id` |
| `account_type` | `transaction_date` |
| `status` | `transaction_type` |
| `balance_after` | `amount` |
| `channel` | `merchant_category` |
| Any unknown column | `merchant_name` |
| | `branch_code`, `is_fraud`, `risk_level` |

กด **Ok and Execute**

---

### Node 15:11 — CSV Writer (Export รายงาน Fraud)

**ค้นหา:** `IO → Write → CSV Writer`

ลากวาง ต่อสายจาก Output Port ของ Column Filter

ดับเบิลคลิกเพื่อเปิด dialog → ตั้งชื่อ node เป็น **"Export รายงาน Fraud"**

**แท็บ Settings:**

**ส่วน Output location:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write to** | `Local File System` |
| **File** | `C:\Users\Samit\Desktop\audit_fraud_2569Q2.csv` |
| **Create missing folders** | ❌ ไม่ติ๊ก |
| **If exists** | ◉ **overwrite** |

**ส่วน Format:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column Delimiter** | `,` |
| **Row Delimiter** | `System Default` |
| **Quote Char** | `"` |
| **Quote Escape Char** | `"` |

**ส่วน Header:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Write column header** | ✅ ติ๊ก |
| **Don't write column headers if file exists** | ❌ ไม่ติ๊ก |
| **Write RowID** | ❌ ไม่ติ๊ก |

กด **OK** → **Execute**

> **ผลลัพธ์:** ไฟล์ `audit_fraud_2569Q2.csv` ถูกบันทึกที่ Desktop ของ Samit — เก็บเฉพาะ rows ที่ is_fraud = 1

---

### ✅ ตรวจสอบผลลัพธ์ Lab 7

1. **Missing Value:** คลิกขวา → **Data Table** — ตรวจสอบว่า merchant_category, merchant_name ไม่มีค่าว่างเหลือ
2. **Rule Engine:** ตรวจสอบ column `risk_level` มีค่า "HIGH RISK" / "MEDIUM RISK" / "LOW RISK" / "NORMAL"
3. **Row Filter:** ตรวจสอบว่า status = "Completed" ทุก row และ amount ≥ 5,000 ทุก row
4. **GroupBy:** ตรวจสอบ columns: branch_code, Sum(amount), Mean(amount), Count(transaction_id), Sum(is_fraud)
5. **Normalizer:** ตรวจสอบว่าค่าใน Sum(amount), Mean(amount) ฯลฯ อยู่ในช่วง [0, 1]
6. **Bar Chart:** คลิกขวา → **View** — กราฟแท่งแสดงยอดรายสาขา
7. **Row Splitter Top Output:** ตรวจสอบว่า `is_fraud` = 1 ทุก row
8. **Column Filter:** ตรวจสอบว่าไม่มี transaction_time, currency, account_type, status, balance_after, channel
9. **CSV Writer:** ตรวจสอบว่าไฟล์ `audit_fraud_2569Q2.csv` ปรากฏบน Desktop

---

## 🧪 Lab 8: Day1_Lab8_Database_PostgreSQL

**วัตถุประสงค์:** เชื่อมต่อ Neon PostgreSQL / SQLite → ดึงข้อมูลการขาย → วิเคราะห์ยอดขายตาม Region และ Product Category → Export รายงาน Excel

**Database:** `sales_data_workshop` — ตาราง SQLite (ไฟล์ .sqlite) หรือ Neon PostgreSQL (Cloud)

---

### ทำไมต้องเชื่อมต่อ Database?

ในงาน Internal Audit ของธนาคาร ข้อมูลจริงไม่ได้อยู่ในไฟล์ CSV เสมอไป — ส่วนใหญ่เก็บอยู่ใน **ฐานข้อมูล (Database)** เช่น PostgreSQL, Oracle, SQL Server ซึ่ง KNIME สามารถเชื่อมต่อและดึงข้อมูลออกมาวิเคราะห์ได้โดยตรง ไม่ต้อง Export เป็น CSV ก่อน

### สร้าง Workflow ใหม่

```
File → New Workflow... → ตั้งชื่อ "Day1_Lab8_Database_PostgreSQL" → Create
```

### Workflow สมบูรณ์

```
[ตัวเลือก A — PostgreSQL]                [ตัวเลือก B — SQLite (ใช้ใน Lab นี้)]
PostgreSQL Connector                      SQLite Connector (17:1)
        │                                         │
        ▼                                         ▼
DB Table Selector                         DB Query Reader (17:6)
        │                                         │
        ▼                                         ▼
DB Reader                                 String to Date&Time (17:4)
                                                  │
                                                  ▼
                                          Column Filter (17:7)
                                                  │
                                                  ▼
                                          Math Formula (17:8)
                                             │         │
                                             ▼         ▼
                                           Pivot    GroupBy
                                             │         │
                                             ▼         ▼
                                        Excel Writer  Sorter
                                                    │       │
                                                    ▼       ▼
                                             Bar Chart   Bar Chart
                                          (ยอดขายต่อ  (ยอดขายต่อ
                                             Region)   หมวดหมู่)
```

---

### โครงสร้างตาราง `sales_data_workshop`

| Column | Type | คำอธิบาย |
|--------|------|----------|
| `Transaction_ID` | VARCHAR | รหัสธุรกรรม |
| `Date` | DATE/VARCHAR | วันที่ทำธุรกรรม |
| `Product_Category` | VARCHAR | หมวดหมู่สินค้า |
| `Product_Name` | VARCHAR | ชื่อสินค้า |
| `Quantity` | INTEGER | จำนวน |
| `Price_Per_Unit` | NUMERIC | ราคาต่อหน่วย |
| `Total_Sales` | NUMERIC | ยอดขายรวม |
| `Region` | VARCHAR | ภูมิภาค |
| `Customer_Type` | VARCHAR | ประเภทลูกค้า |

---

### Node 17:1 — SQLite Connector

**ค้นหา:** `Database → Connection → SQLite Connector`

ลากวางใน Workflow Editor (ไม่ต้องต่อ input — เป็น node แรก)

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Connection Settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Database Dialect** | `SQLite` |
| **Driver Name** | `Driver for SQLite v.3.45.2.0 [ID: built-in-sqlite-3.45.2.0]` |

**ส่วน Location:**

| ตัวเลือก | ค่าที่ตั้ง |
|----------|-----------|
| ◉ **Path** | `C:\KNIME_MASTER\datasets\Mockup Resources\sales_data_workshop.sqlite` |
| ○ In-memory | temporary (ไม่เลือก) |

> 💡 **SQLite เป็น embedded database** ไม่ต้องตั้งค่า host/port/username/password — ชี้ไปที่ไฟล์ .sqlite โดยตรง

กด **OK** → **Execute**

> **ผลลัพธ์:** node สีเขียว = เชื่อมต่อสำเร็จ — DB Connection port พร้อมต่อไปยัง node ถัดไป

---

### Node 17:6 — DB Query Reader

**ค้นหา:** `Database → Query → DB Query Reader`

ลากวาง ต่อสาย DB Connection Port จาก SQLite Connector

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Settings:**

**ส่วน Database Metadata Browser (ซ้าย):**
> แสดงข้อความ *"Please load metadata by clicking the Refresh button."* — คลิก 🔄 Refresh เพื่อโหลด schema ของ SQLite

**ส่วน SQL Statement (ขวา) — กรอก Query:**

```sql
SELECT * FROM sales_data_workshop
```

> 💡 **วิธีกรอก SQL:** พิมพ์ใน SQL Statement panel ด้านขวา — syntax highlighting แสดงสี keyword/column ให้อัตโนมัติ

**ปุ่ม Evaluate:** คลิกเพื่อ Preview ผลลัพธ์ก่อน Execute

กด **OK** → **Execute**

> **ผลลัพธ์:** ดึงข้อมูลทั้งตาราง sales_data_workshop ออกมาเป็น KNIME Data Table

---

### Node 17:4 — String to Date&Time

**ค้นหา:** `Date & Time → Transform → String to Date&Time`

ลากวาง ต่อสายจาก Output Port ของ DB Query Reader

ดับเบิลคลิกเพื่อเปิด dialog → แท็บ **Options**

**ส่วน Column Selection — Manual Selection:**

- **Exclude panel (กล่องแดง):** Transaction_ID, Product_Category, Product_Name, Region, Customer_Type
- **Include panel (กล่องเขียว):** `S Date`
- ◉ **Enforce inclusion**

**ส่วน Replace/Append Selection:**
- ◉ **Replace selected columns**

**ส่วน Type and Format Selection:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **New type** | `Date` |
| **Date format** | `yyyy-MM-dd` |
| **Locale** | `en-US` |
| **Content of first cell** | `2026-05-02` (แสดงตัวอย่างอัตโนมัติ) |

**ส่วน Abort Execution:**
- ✅ **Fail on error**

กด **OK** → **Execute**

> **ผลลัพธ์:** Column `Date` เปลี่ยน icon จาก **S** (String) เป็น 🗓 (Date)

---

### Node 17:7 — Column Filter

**ค้นหา:** `Manipulation → Column → Filter → Column Filter`

ลากวาง ต่อสายจาก Output Port ของ String to Date&Time

ดับเบิลคลิกเพื่อเปิด dialog

**Column filter — Manual** (toggle button):

| Excludes (ลบออก) | Includes (เก็บไว้) |
|------------------|-------------------|
| `Price_Per_Unit` | `Transaction_ID` |
| Any unknown column | `Date` |
| | `Product_Category` |
| | `Product_Name` |
| | `Quantity` |
| | `Total_Sales` |
| | `Region` |
| | `Customer_Type` |

> **เหตุผล:** ตัด `Price_Per_Unit` ออก เนื่องจาก Lab นี้จะคำนวณ avg_price_check จาก Total_Sales/Quantity แทน

กด **OK** → **Execute**

---

### Node 17:8 — Math Formula

**ค้นหา:** `Manipulation → Column → Convert & Replace → Math Formula`

ลากวาง ต่อสายจาก Output Port ของ Column Filter

ดับเบิลคลิกเพื่อเปิด dialog → แท็บ **Math Expression**

**Column List (ด้านซ้าย):**

| Icon | Column |
|------|--------|
| — | ROWINDEX, ROWCOUNT |
| I | Quantity |
| D | Total_Sales |

**กรอก Expression:**

```
$Total_Sales$ / $Quantity$
```

> 💡 **วิธีกรอก:** ดับเบิลคลิก column ใน Column List เพื่อแทรกชื่อ column อัตโนมัติ พร้อม `$...$` ครอบอยู่แล้ว

**ส่วน Output Column:**

| ตัวเลือก | ค่าที่ตั้ง |
|----------|-----------|
| ◉ **Append Column** | `avg_price_check` |
| ○ Replace Column | `S Customer_Type` — ❌ ไม่เลือก |
| **Convert to Int** | ❌ ไม่ติ๊ก |

> **ประโยชน์สำหรับ Audit:** เปรียบเทียบ `avg_price_check` กับราคาจริง — ถ้าต่างกันมากอาจมีความผิดปกติในบัญชี

กด **OK** → **Execute**

> ต่อ Output Port ของ Math Formula ออก 2 สาย: → Pivot และ → GroupBy

---

### Node: Pivot

**ค้นหา:** `Manipulation → Row → Transform → Pivot`

ลากวาง ต่อสายจาก Output Port ของ Math Formula

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Groups:**
- Group column(s): `Region`

**แท็บ Pivots:**
- Pivot column(s): `Customer_Type`
- ✅ Ignore missing values, ✅ Ignore domain

**แท็บ Manual Aggregation:**
- Column: `Total_Sales` → Aggregation: `Sum`

กด **OK** → **Execute**

> **ผลลัพธ์:** ตาราง Cross-tab — แถวคือ Region, คอลัมน์คือ Customer_Type แต่ละประเภท

---

### Node: Excel Writer

**ค้นหา:** `IO → Write → Excel Writer`

ลากวาง ต่อสายจาก Output Port ของ Pivot

ดับเบิลคลิกเพื่อเปิด dialog

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Output location** | `C:\Users\Samit\Desktop\Sales_Report_DB.xlsx` |
| **Sheet name** | `Regional Summary` |
| **If file exists** | `Overwrite` |
| **Write column headers** | ✅ ติ๊ก |
| **Write RowID** | ❌ ไม่ติ๊ก |

กด **OK** → **Execute**

---

### Node: GroupBy — ยอดขายต่อ Region

**ค้นหา:** `Manipulation → Row → Transform → GroupBy`

ลากวาง ต่อสายจาก Output Port ของ Math Formula

ดับเบิลคลิกเพื่อเปิด dialog

**แท็บ Groups:**
- Group column(s): `Region`

**แท็บ Manual Aggregation:**

| Column | Aggregation | Missing |
|--------|-------------|---------|
| `Total_Sales` | `Sum` | ❌ |
| `Transaction_ID` | `Count` | ✅ |

กด **OK** → **Execute**

---

### Node: Sorter

**ค้นหา:** `Manipulation → Row → Transform → Sorter`

ลากวาง ต่อสายจาก Output Port ของ GroupBy

ดับเบิลคลิกเพื่อเปิด dialog

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column** | `Sum(Total_Sales)` |
| **Sort order** | `Descending` (มากไปน้อย) |

กด **OK** → **Execute**

> ต่อ Output Port ของ Sorter ออก 2 สาย: → Bar Chart (ยอดขายรวมต่อ Region) และ → Bar Chart (ยอดขายต่อหมวดหมู่)

---

### Node: Bar Chart — ยอดขายรวมต่อ Region

**ค้นหา:** `Views → JavaScript → Bar Chart`

ลากวาง ต่อสายจาก Output Port ของ Sorter

ดับเบิลคลิกเพื่อเปิด dialog

**ส่วน Data:**
- Category dimension: `Region`
- Aggregation: ◉ Sum
- Frequency dimensions — Includes: `Sum(Total_Sales)`

**ส่วน Plot:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Title** | `ยอดขายรวมต่อ Region` |
| **Custom category axis label** | ✅ → `ภูมิภาค` |
| **Custom frequency axis label** | ✅ → `ยอดขาย (บาท)` |
| **Orientation** | ◉ Vertical |
| **Show bar values** | ✅ |

กด **OK** → **Execute** → คลิกขวา → **View: Bar Chart**

---

### Node: Bar Chart — ยอดขายต่อหมวดหมู่

**ค้นหา:** `Views → JavaScript → Bar Chart`

ลากวาง ต่อสายจาก Output Port ของ Sorter (สายที่ 2)

ตั้งค่าเหมือน Bar Chart แรก แต่เปลี่ยน:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Title** | `ยอดขายต่อหมวดหมู่` |

กด **OK** → **Execute** → **View: Bar Chart**

---

### ข้อมูล Connection — Neon PostgreSQL (ตัวเลือก A)

ถ้าต้องการเชื่อมต่อ Neon PostgreSQL แทน SQLite ให้ใช้ **PostgreSQL Connector** แทน SQLite Connector:

| ค่า | รายละเอียด |
|-----|-----------|
| **Host** | `ep-delicate-frost-aoasxaid-pooler.c-2.ap-southeast-1.aws.neon.tech` |
| **Port** | `5432` |
| **Database** | `neondb` |
| **Username** | `neondb_owner` |
| **Password** | `npg_2qkIU8PWMNVQ` |
| **SSL Mode** | `require` |

**JDBC Parameters ที่ต้องเพิ่ม:**

| Parameter | Value |
|-----------|-------|
| `sslmode` | `require` |
| `channel_binding` | `require` |

> ⚠️ Neon บังคับใช้ SSL — ถ้าไม่ตั้งค่าจะ connect ไม่ได้

---

### ✅ ตรวจสอบผลลัพธ์ Lab 8

1. **SQLite Connector:** node สีเขียว = เชื่อมต่อ SQLite สำเร็จ
2. **DB Query Reader:** คลิกขวา → **Data Table** — ตรวจสอบว่าข้อมูลครบทุก column (Transaction_ID, Date, Product_Category, ...)
3. **String to Date&Time:** ตรวจสอบว่า column `Date` มี icon 🗓 แล้ว
4. **Column Filter:** ตรวจสอบว่าไม่มี `Price_Per_Unit` ใน output
5. **Math Formula:** ตรวจสอบว่ามี column `avg_price_check` เพิ่มขึ้นมา
6. **Pivot:** ตรวจสอบว่า columns แสดง Customer_Type แต่ละประเภทเป็น header
7. **Excel Writer:** ตรวจสอบว่าไฟล์ `Sales_Report_DB.xlsx` ปรากฏบน Desktop
8. **Bar Charts:** คลิกขวา → **View** — กราฟแสดงผลสวยงาม

---

## สรุป Labs ทั้งหมด Day 1

| Lab | Workflow Name | Node หลัก | Output |
|-----|---------------|-----------|--------|
| **Lab 1** | Day1_Lab1_KNIME_Interface | Data Generator | เข้าใจ Node/Port/สีสถานะ |
| **Lab 2** | Day1_Lab2_Hello_KNIME | Python Script, CSV Reader, Row Filter, Column Filter, Column Resorter, String Manipulation, Color Manager, Scatter Plot, GroupBy, Math Formula, Bar Chart, Excel Writer | Overview ครบทุก Node type |
| **Lab 3** | Day1_Lab3_Data_Import_EDA | CSV Reader, Statistics, Missing Value Filter | Schema + Missing analysis |
| **Lab 4** | Day1_Lab4_Data_Cleaning | Data Type Caster, String to Date&Time, Rule Engine | ข้อมูลสะอาด + risk_level |
| **Lab 5** | Day1_Lab5_Filtering | Row Filter ×2, Column Filter, Row Splitter, CSV Writer | audit_high_risk.csv |
| **Lab 6** | Day1_Lab6_Join_Aggregation | GroupBy ×2, Pivot, Joiner | ตารางสรุปหลายมิติ |
| **Lab 7** | Day1_Lab7_Audit_Dashboard | End-to-End 12 Nodes | รายงาน Audit ครบกระบวนการ |
| **Lab 8** | Day1_Lab8_Database_PostgreSQL | PostgreSQL Connector, DB Table Selector, DB Row Filter, DB Reader, GroupBy, Pivot, Bar Chart, Excel Writer | Sales Report จาก Neon DB |

---

## เคล็ดลับ KNIME 5.3 สำหรับงาน Audit

| เคล็ดลับ | วิธีทำใน KNIME 5.3 |
|----------|-------------------|
| ค้นหา Node | พิมพ์ชื่อใน Search box (Node Repository) — ไม่ต้องรู้ path |
| Execute เร็ว | กด **F7** หรือคลิก ▶ Execute all |
| ดูข้อมูล | คลิกขวา node → **Data Table** (หรือ Shift+F6) |
| Reset node | คลิกขวา → **Reset** — ล้าง cache เพื่อ Execute ใหม่ |
| บันทึก | Ctrl+S — บันทึกทุกครั้งที่ Execute สำเร็จ |
| ตั้งชื่อ Node | คลิกขวา → **Edit Node Name** — ช่วยให้ workflow อ่านง่าย |
| เพิ่ม Annotation | คลิกขวาบน Canvas เปล่า → **New Annotation** |
| Zoom | Ctrl+Scroll หรือ Ctrl+= / Ctrl+- |
| Workflow Overview | Ctrl+Shift+W — ดูภาพรวม workflow ทั้งหมด |

---

*จัดทำโดย: อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.*
*อ้างอิง: KNIME Analytics Platform Version 5.3 | สำหรับกลุ่มงานตรวจสอบภายใน ธนาคารกรุงศรีอยุธยา*
*Day 1: 18 พฤษภาคม 2569*
