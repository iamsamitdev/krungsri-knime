## Workshop: Machine Learning and Deep Learning with KNIME — Day 1

## Foundation: พื้นฐาน KNIME และการเตรียมข้อมูลสำหรับงานวิเคราะห์

---

### บทนำ (Introduction)

หลักสูตร **Machine Learning and Deep Learning with KNIME** ออกแบบมาเพื่อให้บุคลากร **กลุ่มงานตรวจสอบภายใน (Internal Audit Group)** ธนาคารกรุงศรีอยุธยา สามารถนำเทคโนโลยี **Data Analytics และ Machine Learning** มาประยุกต์ใช้ในงานตรวจสอบได้จริง โดยใช้ **KNIME Analytics Platform** ซึ่งเป็นเครื่องมือแบบ **Visual Workflow** ที่ไม่ต้องเขียนโค้ดจำนวนมาก เหมาะสำหรับผู้ที่ต้องการเริ่มต้นสาย Data โดยเฉพาะ

วันที่ 1 นี้จะวางรากฐานที่แข็งแกร่งตั้งแต่การเข้าใจ KNIME ไปจนถึงการเตรียมข้อมูล (Data Preparation) ซึ่งเป็นขั้นตอนสำคัญที่สุดในกระบวนการ Machine Learning — **"Garbage in, Garbage out"** หากข้อมูลไม่ดี โมเดลก็ไม่มีทางดีได้

---

### สถาบันผู้จัดอบรม

| รายละเอียด  | ข้อมูล |
| ----------- | ------- |
| สถานที่อบรม | กลุ่มงานตรวจสอบภายใน (Internal Audit Group) ธนาคารกรุงศรีอยุธยา |
| วิทยากร     | อาจารย์สามิตร โกยม |
| สถาบัน      | สถาบันไอทีจีเนียส (IT Genius) |
| วันอบรม Day 1 | วันอาทิตย์ที่ 18 พฤษภาคม 2569 |
| เวลา        | 09:00–16:00 น. (6 ชั่วโมง) |
| รูปแบบ      | Concept + Visual Workflow 40% · Hands-on Lab 60% |
| เครื่องมือหลัก | KNIME Analytics Platform (Free & Open Source) |

---

### วัตถุประสงค์ Day 1

1. เข้าใจแนวคิดของ KNIME Analytics Platform และการทำงานแบบ Visual Node-based Workflow
2. สามารถนำเข้าข้อมูลจากไฟล์ CSV, Excel และแหล่งข้อมูลต่างๆ ได้
3. ตรวจสอบและวิเคราะห์ข้อมูลเบื้องต้น (Exploratory Data Analysis) ได้
4. ทำ Data Manipulation ครบกระบวนการ: Type Conversion, Filtering, String Replace, Missing Values
5. ใช้งาน Join, Aggregation, Grouping และ Pivot ได้อย่างถูกต้อง
6. ทำ Data Transformation: Normalization, Binning และสร้าง Visualization เพื่อดู Pattern

---

### จุดเด่นของหลักสูตร (Highlights)

- 🔧 **No-Code / Low-Code** — สร้าง workflow ด้วยการลาก-วาง node โดยไม่ต้องเขียนโค้ด
- 🏦 **บริบทธนาคาร** — ใช้ข้อมูลจำลองที่ออกแบบมาสำหรับงาน Internal Audit โดยเฉพาะ
- 📊 **Hands-on ทุก Section** — ฝึกปฏิบัติด้วย financial_transactions.csv และ sales_data.csv จริง
- 🔍 **เน้นการตรวจสอบข้อมูล** — เหมาะกับงาน Audit ที่ต้องการ Data Quality สูง
- 🔄 **Reproducible Workflow** — บันทึก workflow แล้วนำกลับมาใช้ซ้ำได้ทันที

---

### กลุ่มเป้าหมาย (Target Audience)

- **เจ้าหน้าที่ตรวจสอบภายใน** ที่ต้องการวิเคราะห์ข้อมูลธุรกรรมได้ด้วยตนเอง
- **นักวิเคราะห์ข้อมูล** ที่ต้องการเครื่องมือ visual workflow แทนการเขียน SQL/Python
- **ผู้บริหาร/หัวหน้างาน** ที่ต้องการเข้าใจกระบวนการวิเคราะห์ข้อมูลเชิงลึก

---

### ผู้เรียนต้องมีพื้นฐานอะไรบ้าง (Prerequisites)

- ✅ **ไม่ต้องมีพื้นฐานการเขียนโปรแกรม** — KNIME ออกแบบมาสำหรับทุกคน
- ✅ **Microsoft Excel** — ใช้งาน Excel เบื้องต้นได้ (เปิดไฟล์ .csv, ดูตาราง)
- ✅ **ความเข้าใจข้อมูลทางการเงิน** — รู้จักคำศัพท์ธุรกรรม เช่น transaction, balance, account
- ✅ **คอมพิวเตอร์** — RAM ≥ 8 GB, พื้นที่ว่าง ≥ 5 GB, ติดตั้ง KNIME เรียบร้อยแล้ว

---

### ชุดข้อมูลที่ใช้ใน Day 1

| ไฟล์ | รายการ | ใช้ใน Section |
| ---- | ------ | ------------- |
| `financial_transactions.csv` | 350 รายการ, 15 columns | S2, S3, S4, S5, S6 |
| `sales_data.csv` | 250 รายการ, 16 columns | S5, S6 |

---

### เนื้อหาการอบรม Day 1 (Course Outline)

1. [Section 1: Introduction to KNIME และ Visual Workflow](#section-1-introduction-to-knime-และ-visual-workflow)
2. [Section 2: การนำเข้าข้อมูลและการสำรวจข้อมูลเบื้องต้น](#section-2-การนำเข้าข้อมูลและการสำรวจข้อมูลเบื้องต้น)
3. [Section 3: Data Manipulation และ Preprocessing I](#section-3-data-manipulation-และ-preprocessing-i)
4. [Section 4: Data Manipulation และ Preprocessing II](#section-4-data-manipulation-และ-preprocessing-ii)
5. [Section 5: Join, Grouping, Aggregation และ Pivot](#section-5-join-grouping-aggregation-และ-pivot)
6. [Section 6: Data Transformation และ Visualization เบื้องต้น](#section-6-data-transformation-และ-visualization-เบื้องต้น)

---

### ตารางเวลา — Day 1: วันอาทิตย์ที่ 18 พฤษภาคม 2569

| Section | เวลา        | หัวข้อ                                              | รูปแบบ        |
| ------- | ----------- | --------------------------------------------------- | ------------- |
| S1      | 09:00–10:00 | Introduction to KNIME และ Visual Workflow            | Concept + Demo |
| —       | 10:00–10:15 | พักเบรก                                             | —             |
| S2      | 10:15–11:15 | การนำเข้าข้อมูลและการสำรวจข้อมูลเบื้องต้น            | Hands-on Lab  |
| S3      | 11:15–12:00 | Data Manipulation และ Preprocessing I               | Hands-on Lab  |
| —       | 12:00–13:00 | พักรับประทานอาหาร                                   | —             |
| S4      | 13:00–14:00 | Data Manipulation และ Preprocessing II              | Hands-on Lab  |
| —       | 14:00–14:15 | พักเบรก                                             | —             |
| S5      | 14:15–15:00 | Join, Grouping, Aggregation และ Pivot               | Hands-on Lab  |
| S6      | 15:00–16:00 | Data Transformation และ Visualization + Workshop    | Hands-on Lab  |

---

## Section 1: Introduction to KNIME และ Visual Workflow

`09:00–10:00 (60 นาที) | Concept + Demo`

### 1.1 KNIME คืออะไร?

**KNIME** (Konstanz Information Miner) คือ **Open-Source Analytics Platform** ที่พัฒนาโดย University of Konstanz ประเทศเยอรมนี ตั้งแต่ปี 2004 ปัจจุบันเป็นหนึ่งในเครื่องมือ Data Science ยอดนิยมระดับโลก

```
┌─────────────────── KNIME Analytics Platform ───────────────────┐
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    KNIME Workbench                       │   │
│  │                                                          │   │
│  │  ┌─────────────┐   ┌─────────────────────────────────┐   │   │
│  │  │ Node        │   │         Workflow Editor          │   │   │
│  │  │ Repository  │   │                                  │   │   │
│  │  │             │   │  [CSV Reader]──▶[Filter]──▶...  │   │   │
│  │  │ • IO        │   │                                  │   │   │
│  │  │ • Manipulat │   └─────────────────────────────────┘   │   │
│  │  │ • Analytics │                                          │   │
│  │  │ • ML        │   ┌─────────────────────────────────┐   │   │
│  │  │ • Deep Lrn  │   │         Data Output View         │   │   │
│  │  └─────────────┘   │  (ดูผลลัพธ์ได้ทันทีทุก node)    │   │   │
│  │                    └─────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

**จุดเด่นของ KNIME:**

| คุณสมบัติ | รายละเอียด |
| --------- | ---------- |
| **Free & Open Source** | ดาวน์โหลดและใช้งานได้ฟรี ไม่มีค่าใช้จ่าย |
| **Visual Workflow** | ลาก-วาง Node เชื่อมต่อกัน ไม่ต้องเขียนโค้ด |
| **5,000+ Nodes** | ครอบคลุมทุกกระบวนการ: Import → Clean → Analyze → Model → Report |
| **Reproducible** | บันทึก workflow แล้วรันซ้ำได้เสมอ |
| **Python/R Integration** | เชื่อมต่อกับ Python และ R Script ได้เมื่อต้องการ |
| **Enterprise Ready** | ใช้งานในองค์กรชั้นนำทั่วโลก รวมถึงธนาคารและสถาบันการเงิน |

---

### 1.2 ทำไม Internal Audit ต้องใช้ KNIME?

งานตรวจสอบภายในในยุคดิจิทัลต้องวิเคราะห์ข้อมูลขนาดใหญ่ที่ Excel ไม่สามารถรับมือได้:

| ความท้าทาย | KNIME แก้ปัญหาอย่างไร |
| ----------- | --------------------- |
| ข้อมูลธุรกรรมหลักล้านรายการ | รองรับข้อมูลขนาดใหญ่ได้โดยไม่ค้าง |
| ต้องทำซ้ำทุกเดือน | บันทึก workflow ครั้งเดียว รันซ้ำอัตโนมัติ |
| ต้องตรวจหาความผิดปกติ (Anomaly) | มี ML nodes สำหรับ Fraud Detection |
| หลายแหล่งข้อมูล (DB, Excel, CSV) | Connector หลากหลาย รวมข้อมูลได้ในที่เดียว |
| ต้องอธิบายวิธีวิเคราะห์ให้ผู้อื่น | Visual workflow เข้าใจง่าย อธิบายได้ทันที |

---

### 1.3 ส่วนประกอบหน้าจอ KNIME

```
┌───────────────────────────────────────────────────────────────┐
│  Menu Bar: File | Edit | View | Node | Help                   │
├──────────────┬────────────────────────────────────────────────┤
│              │                                                 │
│  KNIME       │          Workflow Editor (พื้นที่หลัก)          │
│  Explorer    │                                                 │
│  (จัดการ     │   ┌──────────┐      ┌──────────┐               │
│   workflow)  │   │   Node   │─────▶│   Node   │──▶ ...        │
│              │   │  (สีเขียว│      │          │               │
│              │   │  = OK)   │      │          │               │
├──────────────┤   └──────────┘      └──────────┘               │
│              │                                                 │
│  Node        ├────────────────────────────────────────────────┤
│  Repository  │                                                 │
│  (ค้นหา      │  Console / Node Description / Flow Variables    │
│   Node)      │  (ข้อความ log, คำอธิบาย node ที่เลือก)         │
│              │                                                 │
└──────────────┴────────────────────────────────────────────────┘
```

**สีของ Node แต่ละสถานะ:**

| สี | ความหมาย |
| -- | -------- |
| 🔴 แดง | Node มีปัญหา / ยังไม่ได้ตั้งค่า |
| 🟡 เหลือง | ตั้งค่าแล้ว แต่ยังไม่ได้ execute |
| 🟢 เขียว | Execute สำเร็จ มีข้อมูลพร้อมใช้ |
| 🔵 น้ำเงิน | กำลัง execute อยู่ |

---

### 1.4 แนวคิด Node และ Port

**Node** คือ "กล่องคำสั่ง" แต่ละ Node รับข้อมูลเข้า (Input) และส่งข้อมูลออก (Output):

```
                ┌──────────────────┐
Input Port ───▶│   Node ชื่อ...   │────▶ Output Port
(สามเหลี่ยม ◀) │  (ฟังก์ชันการทำงาน)│     (สามเหลี่ยม ▶)
                └──────────────────┘
```

**ประเภท Port:**

| สัญลักษณ์ | ประเภท | ความหมาย |
| --------- | ------ | -------- |
| ◀ / ▶ สามเหลี่ยม | Data Table | ตารางข้อมูลหลัก |
| ● วงกลม | Model | โมเดล ML ที่ train แล้ว |
| ■ สี่เหลี่ยม | Connection | การเชื่อมต่อฐานข้อมูล |

---

### 1.5 Workflow คืออะไร?

**Workflow** คือลำดับการทำงานที่เชื่อม Node เข้าด้วยกัน จาก Input ไปจนถึง Output:

```
  ข้อมูลดิบ                                         ผลลัพธ์
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────────┐
│   CSV    │─▶│  Filter  │──▶│Normalizer│─▶│  Chart /     │
│  Reader  │   │  (Row)   │   │          │   │  Model       │
└──────────┘   └──────────┘   └──────────┘   └──────────────┘
  นำเข้า       คัดกรอง        ปรับสเกล         แสดงผล
```

> **หลักการ:** ทุก step ของการวิเคราะห์ข้อมูลสามารถแทนด้วย Node — ทำให้กระบวนการทั้งหมดโปร่งใส ตรวจสอบได้ และทำซ้ำได้

---

### 🎯 Real-world Scenario สำหรับ Internal Audit

**สถานการณ์:** กลุ่มงานตรวจสอบได้รับข้อมูลธุรกรรมทางการเงิน 350,000 รายการ ต้องการ:
1. ตรวจสอบว่ามีธุรกรรมผิดปกติ (Fraud) หรือไม่
2. สรุปยอดธุรกรรมรายสาขา รายประเภท
3. ค้นหาลูกค้าที่มีพฤติกรรมเสี่ยง

**คำถามให้คิดก่อนเริ่ม:**
- ปัจจุบันทีมท่านวิเคราะห์ข้อมูลด้วยเครื่องมืออะไร?
- ถ้าต้องวิเคราะห์ข้อมูล 1 ล้านรายการด้วย Excel จะเกิดอะไรขึ้น?
- งาน Audit ที่ทำซ้ำทุกเดือน ทำอย่างไรให้ Reproducible?

---
### 🧪 Lab 0: Hello KNIME
**วัตถุประสงค์:** ทำความรู้จักกับ KNIME และสร้าง workflow แรก
**ขั้นตอนที่ 1:** เปิด KNIME แล้วสร้าง Workflow ใหม่
```
File → New KNIME Workflow → ตั้งชื่อ "Hello_KNIME" → Finish
```

**ขั้นตอนที่ 2:** เพิ่ม node แรก — CSV Reader
```
Node Repository → IO → Read → CSV Reader → ลากวางในพื้นที่ Workflow
```

**ขั้นตอนที่ 3:** ตั้งค่า CSV Reader
```
ดับเบิ้ลคลิกที่ node → เลือกไฟล์ sales_data_workshop.csv
```
** หมายเหตุ:** ดาวน์โหลดข้อมูลตัวอย่างได้ที่  github.com/iamsamitdev/krungsri-knime/blob/main/datasets/sales_data_workshop.csv



**ขั้นตอนที่ 4:** Execute node และดูผลลัพธ์
```
คลิกขวาที่ Node → Execute
คลิกขวาที่ Node → Data Table → ดูข้อมูลที่สร้างขึ้น
```

**ขั้นตอนที่ 5:** บันทึก Workflow
```
File → Save  (หรือ Ctrl+S)
```

### ตัวอย่างการทดสอบโหลดไฟล์ขนาดใหญ่ (มากกว่า 1 ล้านแถว)

**ขั้นตอนที่ 1:** ลาก node python script เข้ามาใน workflow
```
Node Repository → Scripting → Python → Python Script (1⇒1) → ลากวางในพื้นที่ Workflow
```

**ขั้นตอนที่ 2:** คัดลอกโค้ด Python ด้านล่างนี้ไปวางใน Python Script node เพื่อสร้างไฟล์ CSV ขนาดใหญ่ (2 ล้านแถว) สำหรับทดสอบการโหลดข้อมูลใน KNIME
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

# ส่งออกเป็น KNIME Table ด้วย (แค่ 5 แถวแรก เพื่อยืนยัน)
knio.output_tables[0] = knio.Table.from_pandas(df.head(5))

print(f"✅ สร้างไฟล์สำเร็จ: {n:,} แถว")
print(f"📁 บันทึกที่: {output_path}")
```

**ขั้นตอนที่ 3:** รัน Python Script node เพื่อสร้างไฟล์ CSV ขนาดใหญ่
```
คลิกขวาที่ Python Script node → Execute
```

จะเห็นข้อความใน Console ว่าไฟล์ถูกสร้างสำเร็จ พร้อมกับที่อยู่ของไฟล์
**ผลลัพธ์ที่ต้องได้:**
```
✅ สร้างไฟล์สำเร็จ: 2,000,000 แถว
📁 บันทึกที่: C:\KNIME_MASTER\workspace\test_2million.csv
```

**ขั้นตอนที่ 4:** ใช้ CSV Reader node เพื่อนำเข้าไฟล์ CSV ขนาดใหญ่ที่สร้างขึ้น
```
Node Repository → IO → Read → CSV Reader → ลากวางในพื้นที่ Workflow
ดับเบิ้ลคลิกที่ CSV Reader → เลือกไฟล์ test_2million.csv → OK
คลิกขวาที่ CSV Reader → Execute
คลิกขวาที่ CSV Reader → Data Table → ดูข้อมูลที่สร้างขึ้น
```

---

### 🧪 Lab 1: ทำความรู้จัก KNIME — สร้าง Workflow แรก

**วัตถุประสงค์:** สร้าง workflow ง่ายๆ เพื่อทำความคุ้นเคยกับ interface

**ขั้นตอนที่ 1:** เปิด KNIME แล้วสร้าง Workflow ใหม่
```
File → New KNIME Workflow → ตั้งชื่อ "Day1_Hello_KNIME" → Finish
```

**ขั้นตอนที่ 2:** เพิ่ม node แรก — Data Generator
```
Node Repository → ค้นหา "Data Generator" → ลากวางในพื้นที่ Workflow
```

**ขั้นตอนที่ 3:** Execute node และดูผลลัพธ์
```
คลิกขวาที่ Node → Execute
คลิกขวาที่ Node → Data Table → ดูข้อมูลที่สร้างขึ้น
```

**ขั้นตอนที่ 4:** บันทึก Workflow
```
File → Save  (หรือ Ctrl+S)
```

> **Checkpoint:** เห็น node เป็นสีเขียว และสามารถดูตารางข้อมูลได้

---

## Section 2: การนำเข้าข้อมูลและการสำรวจข้อมูลเบื้องต้น

`10:15–11:15 (60 นาที) | Hands-on Lab`

### 2.1 Node สำหรับการนำเข้าข้อมูล (IO Nodes)

KNIME มี Reader nodes หลายประเภทตามแหล่งที่มาของข้อมูล:

| Node | ใช้สำหรับ | ตัวอย่าง |
| ---- | --------- | -------- |
| **CSV Reader** | ไฟล์ .csv | financial_transactions.csv |
| **Excel Reader** | ไฟล์ .xlsx, .xls | รายงาน Excel ต่างๆ |
| **File Reader** | ไฟล์ text ทั่วไป | Log files |
| **Database Reader** | ฐานข้อมูล SQL | Oracle, MySQL, MSSQL |
| **Table Reader** | ไฟล์ .table ของ KNIME | บันทึก output จาก workflow อื่น |

---

### 2.2 การใช้ CSV Reader

**CSV Reader** คือ node พื้นฐานที่สุดในการเริ่มต้น workflow ทุกอัน:

```
┌─────────────────── CSV Reader Configuration ───────────────────┐
│                                                                  │
│  File: [Browse...] financial_transactions.csv                   │
│                                                                  │
│  ☑ Has column headers (Row 1 = Column names)                    │
│  ☑ Support short data lines                                     │
│                                                                  │
│  Column Delimiter: ,  (comma)                                   │
│  Quote char: "  (double quote)                                  │
│  Encoding: UTF-8                                                │
│                                                                  │
│  [Preview] แสดงตัวอย่างข้อมูล 20 แถวแรก                         │
└──────────────────────────────────────────────────────────────────┘
```

---

### 2.3 การตรวจสอบ Schema และประเภทข้อมูล

หลังจาก import ข้อมูลแล้ว ขั้นตอนแรกคือตรวจสอบ **Schema** ของข้อมูล:

**ประเภทข้อมูลใน KNIME:**

| ประเภท | สัญลักษณ์ | ตัวอย่าง |
| ------ | --------- | -------- |
| **String** | `S` | "TXN000001", "Bangkok" |
| **Integer** | `I` | 1, 100, 350 |
| **Double** | `D` | 1299.29, 0.05 |
| **Boolean** | `B` | true, false |
| **Date & Time** | `T` | 2024-03-12 |

> **ปัญหาที่พบบ่อย:** KNIME อาจ detect ประเภทข้อมูลผิด เช่น วันที่อาจถูก detect เป็น String → ต้องแก้ไขด้วย **Data Type Caster**

---

### 2.4 Nodes สำหรับสำรวจข้อมูลเบื้องต้น

| Node | หน้าที่ | ข้อมูลที่ได้ |
| ---- | ------- | ------------ |
| **Statistics** | สถิติเชิงพรรณนา | Min, Max, Mean, Std Dev, Missing Count |
| **Data Explorer** | สำรวจแบบ Interactive | ดูการกระจาย, Histogram, Correlation |
| **Column Names Extractor** | ดึงชื่อ column ทั้งหมด | รายชื่อ columns |
| **Missing Value** | วิเคราะห์ค่าว่าง | จำนวน/% Missing ต่อ column |

---

### 2.5 การตรวจสอบ Missing Values

ใน dataset จริงมักพบค่าว่าง (Missing Values) ซึ่งมีผลต่อการวิเคราะห์:

```
ตัวอย่าง financial_transactions.csv:
┌────────────────┬───────────┬──────────┬─────────┐
│ transaction_id │ amount    │ merchant │ status  │
├────────────────┼───────────┼──────────┼─────────┤
│ TXN000001      │ 1299.29   │ KFC TH   │ Failed  │
│ TXN000002      │ ???       │ PTT      │ Pending │  ← Missing amount!
│ TXN000003      │ 5500.00   │          │ Done    │  ← Missing merchant!
└────────────────┴───────────┴──────────┴─────────┘

Missing Values อาจเกิดจาก:
• ข้อมูลสูญหายระหว่างส่ง
• ระบบ source ไม่บันทึกค่านั้น
• ข้อผิดพลาดในการ export ข้อมูล
```

---

### 🧪 Lab 2: นำเข้าข้อมูล financial_transactions.csv

**วัตถุประสงค์:** นำเข้าข้อมูลธุรกรรมและสำรวจโครงสร้างข้อมูล

**ขั้นตอนที่ 1:** เพิ่ม CSV Reader node
```
Node Repository → IO → Read → CSV Reader → ลากวางใน workflow
```

**ขั้นตอนที่ 2:** ตั้งค่า CSV Reader
```
ดับเบิ้ลคลิกที่ node → เลือกไฟล์ financial_transactions.csv
ตรวจสอบ: Column Delimiter = Comma, Has column header = ✓
คลิก OK → Execute
```

**ขั้นตอนที่ 3:** ดูข้อมูล
```
คลิกขวา CSV Reader → Data Table → ตรวจสอบ:
• จำนวน rows = 350
• จำนวน columns = 15
• ชื่อ column ถูกต้อง
```

**ขั้นตอนที่ 4:** เพิ่ม Statistics node
```
Node Repository → Statistics → Statistics → ลาก, เชื่อมกับ CSV Reader → Execute
คลิกขวา Statistics → Statistics View → ดู Min, Max, Mean ของแต่ละ column
```

**ขั้นตอนที่ 5:** ตรวจสอบ Missing Values
```
Node Repository → Manipulation → Row → Filter → Missing Value Filter
เชื่อมกับ CSV Reader → Execute
ดูว่า column ไหนมีค่าว่าง
```

**ผลลัพธ์ที่ต้องได้:**
```
จำนวน rows ทั้งหมด:     350
Columns มี Missing:     ตรวจสอบแต่ละ column
Amount range:           50 - 50,000 THB
is_fraud = 1:           ประมาณ 5-10% ของทั้งหมด
```

> **Audit Insight:** ในงาน Audit ให้ระวัง column `is_fraud`, `status`, และ `amount` เป็นพิเศษ

---

## Section 3: Data Manipulation และ Preprocessing I

`11:15–12:00 (45 นาที) | Hands-on Lab`

### 3.1 Data Manipulation คืออะไร?

**Data Manipulation** คือการแปลงและจัดการข้อมูลให้อยู่ในรูปแบบที่เหมาะสมสำหรับการวิเคราะห์:

```
Raw Data (ข้อมูลดิบ)          Clean Data (ข้อมูลสะอาด)
┌──────────────────┐          ┌──────────────────────────┐
│ "2024-3-12"      │          │ Date: 2024-03-12         │
│ "1,299.29 บาท"   │  ──────▶ │ Amount: 1299.29          │
│ ""               │  Clean   │ Merchant: "Unknown"      │
│ "TRUE"           │          │ is_fraud: 1 (Integer)    │
└──────────────────┘          └──────────────────────────┘
```

---

### 3.2 Type Conversion — เปลี่ยนประเภทข้อมูล

Node: **Data Type Caster** (หรือ String to Date/Time, String to Number)

| สถานการณ์ | Node ที่ใช้ |
| --------- | ---------- |
| String → Integer/Double | Data Type Caster |
| String → Date | String to Date&Time |
| Integer → String | Data Type Caster |
| String → Boolean | Rule Engine |

```
ตัวอย่าง: column "amount" ถูก import เป็น String "1299.29"
ต้องแปลงเป็น Double เพื่อคำนวณได้

CSV Reader ──▶ Data Type Caster ──▶ Statistics
              (String→Double       (ตอนนี้คำนวณ
               สำหรับ amount)       Mean ได้แล้ว)
```

---

### 3.3 String Manipulation

| Node | หน้าที่ |
| ---- | ------- |
| **String Replacer** | แทนค่า String ด้วย pattern ง่ายๆ |
| **String Manipulation** | ใช้ function เช่น `upperCase()`, `trim()`, `replace()` |
| **Rule-based Row Splitter** | แยก row ตาม condition |
| **Rule Engine** | สร้าง column ใหม่ตามกฎที่กำหนด |

**ตัวอย่าง String Manipulation functions:**

```
upperCase($Status$)           → "COMPLETED"
lowerCase($Status$)           → "completed"
trim($merchant_name$)         → ลบช่องว่างหน้า-หลัง
length($transaction_id$)      → จำนวนตัวอักษร
substr($transaction_id$, 0, 3) → "TXN"
```

---

### 3.4 การจัดการ Missing Values

**Missing Value node** ใน KNIME มีวิธีจัดการหลายแบบ:

| กลยุทธ์ | วิธีใน KNIME | เหมาะกับ |
| ------- | ------------ | -------- |
| **ลบ row ที่มี Missing** | Row Filter → Missing Value | ข้อมูลน้อย missing |
| **แทนด้วยค่าเฉลี่ย (Mean)** | Missing Value node → Mean | ตัวเลขต่อเนื่อง |
| **แทนด้วย Median** | Missing Value node → Median | ตัวเลขที่มี outlier |
| **แทนด้วยค่าที่พบบ่อยสุด (Mode)** | Missing Value node → Most Frequent | categorical |
| **แทนด้วยค่าคงที่** | Missing Value node → Fixed Value | กำหนดเอง |

> **หลักการ Internal Audit:** ในงานตรวจสอบ **ห้ามลบข้อมูล** โดยไม่มีเหตุผล — ให้บันทึกว่า record ไหน missing และทำไม

---

### 🧪 Lab 3: Type Conversion และ String Manipulation

**วัตถุประสงค์:** แปลงประเภทข้อมูลและทำความสะอาด column ต่างๆ

**ขั้นตอนที่ 1:** เพิ่ม Data Type Caster
```
Node Repository → Manipulation → Column → Convert & Replace → Data Type Caster
เชื่อมกับ CSV Reader → ดับเบิ้ลคลิกตั้งค่า:
• column "amount" → Double
• column "is_fraud" → Integer
• column "balance_after" → Double
คลิก OK → Execute
```

**ขั้นตอนที่ 2:** แปลง transaction_date เป็น Date
```
Node Repository → Date & Time → Transform → String to Date&Time
เชื่อมกับ Data Type Caster → ตั้งค่า:
• Column: transaction_date
• Format: yyyy-MM-dd
Execute
```

**ขั้นตอนที่ 3:** สร้าง column ใหม่ด้วย Rule Engine — จำแนกระดับความเสี่ยง
```
Node Repository → Manipulation → Column → Calculate → Rule Engine
เชื่อมต่อ → ดับเบิ้ลคลิก → ใส่กฎ:

$amount$ > 30000 AND $is_fraud$ = 1 => "HIGH RISK"
$amount$ > 10000 AND $is_fraud$ = 1 => "MEDIUM RISK"
$is_fraud$ = 1 => "LOW RISK"
TRUE => "NORMAL"

Output column name: risk_level
Execute
```

**ผลลัพธ์ที่ต้องได้:**
- Column `amount` เป็นตัวเลข Double
- Column `transaction_date` เป็น Date
- Column `risk_level` มีค่า HIGH RISK / MEDIUM RISK / LOW RISK / NORMAL

---

## Section 4: Data Manipulation และ Preprocessing II

`13:00–14:00 (60 นาที) | Hands-on Lab`

### 4.1 Filtering ข้อมูล

**Filtering** คือการคัดเลือกข้อมูลที่ต้องการ มี 2 แบบหลัก:

```
                    ┌─────────────────┐
Data ──────────────▶│   Row Filter    │──▶ Rows ที่ผ่านเงื่อนไข
                    │ (คัดกรอง row)   │
                    └─────────────────┘
                              │
                              └──▶ Rows ที่ไม่ผ่าน (Second output port)


                    ┌─────────────────┐
Data ──────────────▶│ Column Filter   │──▶ เฉพาะ Columns ที่เลือก
                    │ (คัดกรอง column)│
                    └─────────────────┘
```

**Row Filter — เงื่อนไขที่ใช้ได้:**

| ประเภทเงื่อนไข | ตัวอย่าง |
| -------------- | -------- |
| ตัวเลข: >, <, >=, <= | amount > 10000 |
| ข้อความ: equals, contains, starts with | status = "Completed" |
| Missing Values | column X is/is not missing |
| Row number range | rows 1–100 |
| Regular Expression | transaction_id matches "TXN0001.*" |

---

### 4.2 Column Filter — เลือก Column ที่ต้องการ

```
Column Filter Configuration:
┌──────────────────────────────────────────────┐
│  Exclude:               Include:              │
│  ┌──────────────────┐  ┌────────────────────┐ │
│  │ balance_after    │  │ transaction_id     │ │
│  │ currency         │  │ customer_id        │ │
│  │ channel          │  │ transaction_date   │ │
│  │                  │  │ amount             │ │
│  │                  │  │ is_fraud           │ │
│  └──────────────────┘  └────────────────────┘ │
│           [▶▶ Include All]  [◀◀ Exclude All]  │
└──────────────────────────────────────────────┘
```

---

### 4.3 Splitter และ Concatenate

**การแยกและรวมข้อมูล:**

```
SPLIT:
              ┌──▶ Fraud Transactions (is_fraud = 1)
Data ──▶ Splitter
              └──▶ Normal Transactions (is_fraud = 0)


CONCATENATE (รวมกลับ):
Data A ──┐
          ├──▶ Concatenate ──▶ Combined Data
Data B ──┘
```

**ตัวอย่างการใช้งานใน Audit:**
- แยกธุรกรรมสงสัยออกมาตรวจสอบแยก
- รวมข้อมูลธุรกรรมจากหลายสาขาเข้าด้วยกัน

---

### 4.4 Concatenate — รวม Dataset หลายไฟล์

```
ตัวอย่าง: รวมข้อมูลธุรกรรมจาก Q1 และ Q2

transactions_Q1.csv ──▶ CSV Reader ──┐
                                      ├──▶ Concatenate ──▶ All Data
transactions_Q2.csv ──▶ CSV Reader ──┘
```

**ข้อควรระวัง Concatenate:**
- Schema ต้องตรงกัน (ชื่อ column, ประเภทข้อมูล)
- ถ้า column ไม่ตรงกัน ให้ใช้ Column Appender หรือ Column Filter ก่อน

---

### 🧪 Lab 4: Filtering ธุรกรรมที่มีความเสี่ยง

**วัตถุประสงค์:** คัดกรองธุรกรรมที่น่าสงสัยสำหรับงาน Audit

**ขั้นตอนที่ 1:** Filter เฉพาะธุรกรรมที่ Completed
```
Node Repository → Row Filter → เชื่อมกับ workflow ก่อนหน้า
ดับเบิ้ลคลิก → Column: status, Operator: equals, Value: Completed
Execute
```

**ขั้นตอนที่ 2:** Filter ธุรกรรมยอดสูง (High Value)
```
เพิ่ม Row Filter อีกตัว → เชื่อมต่อ
Column: amount, Operator: >=, Value: 20000
Execute
```

**ขั้นตอนที่ 3:** เลือกเฉพาะ Column ที่จำเป็นสำหรับ Audit
```
Column Filter → ลาก, เชื่อมต่อ
Exclude: currency, channel, balance_after
Include: transaction_id, customer_id, date, amount, merchant, is_fraud, risk_level
Execute
```

**ขั้นตอนที่ 4:** แยก Fraud จาก Non-Fraud
```
Row Splitter → เชื่อมต่อ
Condition: is_fraud = 1
Execute
Output port 1 = ธุรกรรม Fraud (น่าสงสัย)
Output port 2 = ธุรกรรมปกติ
```

**ขั้นตอนที่ 5:** บันทึกผล Fraud transactions ออกเป็น CSV
```
CSV Writer → เชื่อมกับ Output port 1 ของ Row Splitter
ตั้งชื่อไฟล์: audit_high_risk_transactions.csv
Execute
```

---

## Section 5: Join, Grouping, Aggregation และ Pivot

`14:15–15:00 (45 นาที) | Hands-on Lab`

### 5.1 Joiner — รวมข้อมูลจากหลายตาราง

**Joiner** คือ node ที่รวมข้อมูล 2 ตารางเข้าด้วยกัน เหมือน SQL JOIN:

```
ตาราง A (Transactions)     ตาราง B (Customer Info)
┌──────────┬──────────┐    ┌──────────┬──────────┐
│ cust_id  │ amount   │    │ cust_id  │ name     │
├──────────┼──────────┤    ├──────────┼──────────┤
│ CUS001   │ 5,000    │    │ CUS001   │ สมชาย    │
│ CUS002   │ 12,000   │ ──▶ │ CUS002   │ กานดา    │
└──────────┴──────────┘    └──────────┴──────────┘

                    ┌────────────────────────────┐
              JOIN  │ cust_id │ amount │ name     │
              ────▶ │ CUS001  │ 5,000  │ สมชาย   │
                    │ CUS002  │ 12,000 │ กานดา   │
                    └────────────────────────────┘
```

**ประเภท JOIN:**

| ประเภท | ความหมาย | ใช้เมื่อ |
| ------ | -------- | -------- |
| **Inner Join** | เฉพาะแถวที่มีคู่กันในทั้ง 2 ตาราง | ต้องการข้อมูลที่ครบทั้ง 2 ฝั่ง |
| **Left Outer Join** | ทุกแถวจากตาราง A + แถวที่มีคู่จาก B | ต้องการทุก transaction แม้ไม่มีข้อมูล customer |
| **Right Outer Join** | ทุกแถวจากตาราง B + แถวที่มีคู่จาก A | ต้องการทุก customer แม้ไม่มี transaction |
| **Full Outer Join** | ทุกแถวจากทั้ง 2 ตาราง | ต้องการข้อมูลครบทุกฝั่ง |

---

### 5.2 GroupBy — สรุปข้อมูลเชิงกลุ่ม

**GroupBy** คือ node ที่รวมข้อมูลตามกลุ่ม เหมือน SQL GROUP BY:

```
ข้อมูล transactions:             ผลลัพธ์ GroupBy branch:
┌──────────┬────────┐           ┌──────────────┬───────────┬──────────┐
│ branch   │ amount │           │ branch       │ txn_count │ total    │
├──────────┼────────┤   ──────▶ ├──────────────┼───────────┼──────────┤
│ BKK001   │ 5,000  │  GroupBy  │ BKK001       │ 45        │ 520,000  │
│ BKK001   │ 12,000 │           │ BKK002       │ 38        │ 430,000  │
│ BKK002   │ 8,000  │           │ CMI001       │ 22        │ 180,000  │
└──────────┴────────┘           └──────────────┴───────────┴──────────┘
```

**Aggregation Functions ที่ใช้บ่อย:**

| Function | ความหมาย | ใช้กับ |
| -------- | -------- | ------ |
| `Count` | นับจำนวน row | ทุกประเภท |
| `Sum` | รวมผลบวก | ตัวเลข |
| `Mean` | ค่าเฉลี่ย | ตัวเลข |
| `Min` / `Max` | ค่าต่ำสุด / สูงสุด | ตัวเลข |
| `Std Deviation` | ส่วนเบี่ยงเบนมาตรฐาน | ตัวเลข |
| `Concatenate` | รวม string ทั้งหมด | ข้อความ |
| `First` / `Last` | ค่าแรก / ค่าสุดท้าย | ทุกประเภท |

---

### 5.3 Pivot — เปลี่ยน Row เป็น Column

**Pivot** คือการ "หมุน" ข้อมูล เปลี่ยนค่าใน column มาเป็น column ใหม่:

```
Before Pivot:                          After Pivot:
┌──────────┬────────────┬────────┐    ┌──────────┬────────┬─────────┬────────┐
│ branch   │ txn_type   │ amount │    │ branch   │ ATM    │ Online  │ Branch │
├──────────┼────────────┼────────┤ ──▶ ├──────────┼────────┼─────────┼────────┤
│ BKK001   │ ATM        │ 5,000  │    │ BKK001   │ 5,000  │ 12,000  │ 8,000  │
│ BKK001   │ Online     │ 12,000 │    │ BKK002   │ 3,000  │ 15,000  │ 6,000  │
│ BKK001   │ Branch     │ 8,000  │    └──────────┴────────┴─────────┴────────┘
│ BKK002   │ ATM        │ 3,000  │
└──────────┴────────────┴────────┘
```

---

### 🧪 Lab 5: วิเคราะห์ธุรกรรมรายสาขาและประเภท

**วัตถุประสงค์:** สรุปยอดธุรกรรมตามมิติต่างๆ สำหรับรายงาน Audit

**ขั้นตอนที่ 1:** GroupBy รายสาขา
```
GroupBy node → เชื่อมกับข้อมูลที่ผ่านการ clean แล้ว

Tab "Groups": เลือก branch_code
Tab "Aggregation":
• amount → Sum (ตั้งชื่อ: total_amount)
• amount → Mean (ตั้งชื่อ: avg_amount)
• amount → Count (ตั้งชื่อ: txn_count)
• is_fraud → Sum (ตั้งชื่อ: fraud_count)

Execute → ดูผลลัพธ์: ยอดรวมธุรกรรมต่อสาขา
```

**ขั้นตอนที่ 2:** GroupBy รายประเภทธุรกรรม + Fraud
```
GroupBy node ใหม่ → Groups: merchant_category, is_fraud
Aggregation: amount → Count, Sum
Execute
```

**ขั้นตอนที่ 3:** Pivot — เปรียบเทียบยอดตามประเภทรายสาขา
```
Pivot node → เชื่อมต่อ
Row Columns: branch_code
Column Columns: transaction_type
Aggregation: amount → Sum
Execute → ดู table ที่หมุนแล้ว
```

**ขั้นตอนที่ 4:** Join กับ sales_data.csv
```
CSV Reader ใหม่ → อ่าน sales_data.csv
Joiner → Input port 1: transactions, Input port 2: sales
Join condition: customer_id = customer_id
Join mode: Inner Join
Execute
```

---

## Section 6: Data Transformation และ Visualization เบื้องต้น

`15:00–16:00 (60 นาที) | Hands-on Lab + Workshop`

### 6.1 Binning — แบ่งกลุ่มตัวเลข

**Binning** (หรือ Discretization) คือการเปลี่ยนตัวเลขต่อเนื่องให้เป็น category:

```
amount (ต่อเนื่อง) → Bin (category)
┌─────────────────────────────────────────────────────┐
│  50 - 1,000     → "Micro Transaction"               │
│  1,001 - 10,000 → "Small Transaction"               │
│ 10,001 - 30,000 → "Medium Transaction"              │
│ 30,001 - 50,000 → "Large Transaction"  ← Audit Focus │
└─────────────────────────────────────────────────────┘
```

**Node: Numeric Binner** — ตั้งค่า bin boundaries เอง

**Node: Equal Width Binner** — แบ่ง bin ขนาดเท่ากันอัตโนมัติ

**Node: Equal Frequency Binner** — แบ่งให้แต่ละ bin มีข้อมูลจำนวนเท่ากัน

---

### 6.2 Normalization — ปรับสเกลข้อมูล

**Normalization** คือการปรับค่าตัวเลขให้อยู่ในช่วงที่เหมาะสม สำคัญมากก่อนทำ ML:

| วิธี | สูตร | ช่วงผลลัพธ์ | ใช้เมื่อ |
| ---- | ---- | ----------- | -------- |
| **Min-Max Normalization** | (x - min) / (max - min) | [0, 1] | ข้อมูลมีขอบเขตชัดเจน |
| **Z-score Standardization** | (x - mean) / std | ไม่จำกัด, mean=0 | ข้อมูลมี Outlier |
| **Decimal Scaling** | x / 10^k | [-1, 1] | ข้อมูลทศนิยม |

**ตัวอย่าง Min-Max:**
```
amount: [50, 500, 25,000, 50,000]
min = 50, max = 50,000

amount_normalized:
50     → (50-50)/(50000-50)    = 0.000
500    → (500-50)/(50000-50)   = 0.009
25,000 → (25000-50)/(50000-50) = 0.499
50,000 → (50000-50)/(50000-50) = 1.000
```

**Node: Normalizer** → เลือกวิธีและ columns ที่ต้องการ normalize

---

### 6.3 Visualization ใน KNIME

KNIME มี visualization nodes หลายประเภท:

| Node | ใช้สำหรับ | เหมาะกับ |
| ---- | --------- | -------- |
| **Bar Chart** | เปรียบเทียบค่าระหว่างกลุ่ม | ยอดธุรกรรมรายสาขา |
| **Line Chart** | แนวโน้มตามเวลา | ยอดธุรกรรมรายวัน/เดือน |
| **Scatter Plot** | ความสัมพันธ์ระหว่าง 2 ตัวแปร | amount vs credit_score |
| **Pie Chart** | สัดส่วน | % ธุรกรรมแต่ละประเภท |
| **Histogram** | การกระจายตัวของข้อมูล | การกระจายของ amount |
| **Box Plot** | เปรียบเทียบกลุ่มและ outlier | amount ตามประเภท |
| **Heatmap** | ความเข้มข้นของข้อมูล 2 มิติ | Correlation matrix |

---

### 6.4 ตัวอย่าง Workflow สมบูรณ์ Day 1

```
CSV Reader ──▶ Data Type Caster ──▶ String to Date&Time
                      │
                      ▼
               Missing Value ──▶ Rule Engine (risk_level)
                      │
              ┌───────┤
              ▼       ▼
          Row Filter  Statistics (EDA)
         (Completed)
              │
              ▼
         Column Filter
              │
        ┌─────┤
        ▼     ▼
    GroupBy  Row Splitter
  (by branch) (fraud/normal)
        │           │
        ▼           ▼
      Pivot      CSV Writer
  (txn_type)  (audit_fraud.csv)
        │
        ▼
   Normalizer
        │
        ▼
   Bar Chart / Line Chart / Histogram
```

---

### 🧪 Lab 6: Workshop สรุป Day 1 — สร้าง Audit Dashboard Workflow

**วัตถุประสงค์:** สร้าง workflow ครบกระบวนการสำหรับรายงาน Audit

**โจทย์:** สร้าง workflow ที่:
1. นำเข้า financial_transactions.csv
2. แปลงประเภทข้อมูลให้ถูกต้องทั้งหมด
3. จัดการ Missing Values
4. สร้าง column `risk_level` ด้วย Rule Engine
5. Filter เฉพาะธุรกรรม Completed ยอดเกิน 5,000 บาท
6. สรุปยอดรายสาขาและประเภทธุรกรรมด้วย GroupBy
7. แสดง Bar Chart ยอดธุรกรรมรายสาขา
8. Export ธุรกรรมที่มี `is_fraud = 1` ออกเป็น CSV

**เส้นทาง Workflow:**
```
CSV Reader
    │
    ▼
Data Type Caster (amount, balance_after → Double; is_fraud → Integer)
    │
    ▼
String to Date&Time (transaction_date → Date)
    │
    ▼
Missing Value (Merchant → "Unknown", Amount → Mean)
    │
    ▼
Rule Engine (สร้าง risk_level)
    │
    ▼
Row Filter (status = Completed AND amount >= 5000)
    │
    ├──────────────────────┬─────────────────────┐
    ▼                      ▼                     ▼
GroupBy (by branch)    Row Splitter          Column Filter
Sum/Count amount      (is_fraud = 1)         (เลือก columns)
    │                      │
    ▼                      ▼
Bar Chart            CSV Writer (audit_fraud.csv)
```

> **Tip สำหรับ Audit:** บันทึกชื่อ workflow ให้ชัดเจน เช่น "Audit_Fraud_Detection_2026Q2" พร้อมวันที่ เพื่อ traceability

---

### สรุป Day 1

เมื่อจบวันที่ 1 อาจารย์สามิตรและผู้เรียนได้เรียนรู้และฝึกปฏิบัติครบดังนี้:

**Section 1: Introduction to KNIME**

- ✅ เข้าใจแนวคิด KNIME Analytics Platform และ Visual Workflow
- ✅ รู้จักส่วนประกอบหน้าจอ: Workflow Editor, Node Repository, Console
- ✅ เข้าใจ Node, Port และสีสถานะต่างๆ
- ✅ สร้างและบันทึก Workflow ได้

**Section 2: Data Import & Exploration**

- ✅ นำเข้าข้อมูลด้วย CSV Reader และ Excel Reader
- ✅ ตรวจสอบ Schema และประเภทข้อมูล
- ✅ ใช้ Statistics node ดูสถิติเบื้องต้น
- ✅ ตรวจสอบและวิเคราะห์ Missing Values

**Section 3: Data Manipulation I**

- ✅ แปลงประเภทข้อมูลด้วย Data Type Caster
- ✅ จัดการ String ด้วย String Manipulation และ String Replacer
- ✅ จัดการ Missing Values ตามกลยุทธ์ที่เหมาะสม
- ✅ สร้าง column ใหม่ด้วย Rule Engine

**Section 4: Data Manipulation II**

- ✅ Filter ข้อมูลด้วย Row Filter และ Column Filter
- ✅ แยกข้อมูลด้วย Row Splitter
- ✅ รวมข้อมูลด้วย Concatenate
- ✅ Export ข้อมูลด้วย CSV Writer

**Section 5: Join, GroupBy, Aggregation, Pivot**

- ✅ รวมตารางด้วย Joiner (Inner, Left, Right, Full)
- ✅ สรุปข้อมูลเชิงกลุ่มด้วย GroupBy และ Aggregation
- ✅ Reshape ข้อมูลด้วย Pivot

**Section 6: Transformation & Visualization**

- ✅ แบ่งกลุ่มข้อมูลด้วย Binning (Numeric Binner, Equal Width/Frequency)
- ✅ ปรับสเกลข้อมูลด้วย Normalization (Min-Max, Z-score)
- ✅ สร้าง Visualization: Bar Chart, Histogram, Line Chart
- ✅ สร้าง End-to-End Audit Workflow ครบกระบวนการ

> **วันพรุ่งนี้ (Day 2):** เราจะนำข้อมูลที่เตรียมสะอาดแล้วนี้ไปสร้าง Machine Learning Models สำหรับงาน Classification (Fraud Detection, Loan Default) และ Clustering (Customer Segmentation) ครับ
