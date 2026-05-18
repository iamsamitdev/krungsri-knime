# Section 7: การทำงานกับฐานข้อมูล PostgreSQL บน Neon
## เพิ่มเติมสำหรับ Day 1 — Machine Learning with KNIME
**วิทยากร:** อาจารย์สามิตร โกยม | **สถาบัน:** IT Genius | **วันที่:** 18 พฤษภาคม 2569

---

## 7.1 ภาพรวม: ทำไมต้องเชื่อมต่อ Database?

ในงาน Internal Audit ของธนาคาร ข้อมูลจริงไม่ได้อยู่ในไฟล์ CSV เสมอไป — ส่วนใหญ่เก็บอยู่ใน **ฐานข้อมูล (Database)** เช่น PostgreSQL, Oracle, SQL Server ซึ่ง KNIME สามารถเชื่อมต่อและดึงข้อมูลออกมาวิเคราะห์ได้โดยตรง ไม่ต้อง Export เป็น CSV ก่อน

```
Database (Neon PostgreSQL)
         │
         ▼
    KNIME Workflow
    ┌─────────────────────────────────────┐
    │ PostgreSQL Connector                │
    │        ↓                            │
    │ DB Table Selector / DB Query Reader │
    │        ↓                            │
    │ DB Row Filter  (WHERE clause)       │
    │        ↓                            │
    │ DB Reader  ←  ข้อมูลเข้า KNIME Table│
    │        ↓                            │
    │ [Data Processing Nodes]             │
    │        ↓                            │
    │ [Report: Excel / Bar Chart / PDF]   │
    └─────────────────────────────────────┘
```

---

## 7.2 ข้อมูล Connection — Neon PostgreSQL

**Database ที่ใช้ใน Workshop:**

| ค่า | รายละเอียด |
|-----|-----------|
| **Database Engine** | PostgreSQL (Neon Cloud) |
| **Host** | `ep-delicate-frost-aoasxaid-pooler.c-2.ap-southeast-1.aws.neon.tech` |
| **Port** | `5432` |
| **Database** | `neondb` |
| **Schema** | `public` |
| **Table** | `sales_data_workshop` |
| **Username** | `neondb_owner` |
| **Password** | `npg_2qkIU8PWMNVQ` |
| **SSL Mode** | `require` |

**โครงสร้างตาราง `sales_data_workshop`:**

| Column | Type | คำอธิบาย |
|--------|------|----------|
| `transaction_id` | VARCHAR(50) PRIMARY KEY | รหัสธุรกรรม |
| `date` | DATE | วันที่ทำธุรกรรม |
| `product_category` | VARCHAR(100) | หมวดหมู่สินค้า |
| `product_name` | VARCHAR(100) | ชื่อสินค้า |
| `quantity` | INTEGER | จำนวน |
| `price_per_unit` | NUMERIC(10,2) | ราคาต่อหน่วย |
| `total_sales` | NUMERIC(12,2) | ยอดขายรวม |
| `region` | VARCHAR(50) | ภูมิภาค |
| `customer_type` | VARCHAR(50) | ประเภทลูกค้า |

---

## 7.3 ข้อกำหนดเบื้องต้น: ติดตั้ง PostgreSQL Driver ใน KNIME

ก่อนเชื่อมต่อ Database ครั้งแรก ต้องตรวจสอบว่า KNIME มี PostgreSQL JDBC Driver:

**ขั้นตอน:**
1. เมนู **File → Preferences**
2. ขยาย **KNIME → Databases**
3. ดูว่ามี **PostgreSQL** อยู่ในรายการ Driver หรือไม่
4. ถ้าไม่มี → คลิก **Download** หรือ **Add** แล้วเลือก Driver ที่ดาวน์โหลดมา

> ✅ **KNIME 5.x มี Built-in Driver** สำหรับ PostgreSQL แล้ว — ส่วนใหญ่ไม่ต้องติดตั้งเพิ่ม

---

## 🧪 Lab 7: ดึงข้อมูลจาก PostgreSQL และสร้าง Sales Report

**วัตถุประสงค์:** เชื่อมต่อ Neon PostgreSQL → ดึงข้อมูลการขาย → วิเคราะห์ยอดขายตาม Region และ Product Category → Export รายงาน Excel

**Workflow สมบูรณ์:**
```
PostgreSQL Connector
        │
        ▼
DB Table Selector
        │
        ▼
DB Row Filter  (กรองข้อมูลด้วย SQL)
        │
        ▼
DB Reader  ────────────────────────────────────────┐
        │                                           │
        ▼                                           ▼
String to Date&Time               Column Filter (เลือก columns)
        │                                           │
        ▼                                           ▼
Math Formula (คำนวณ)            Missing Value Handler
        │
        ▼
   ┌────┴──────────────────┐
   ▼                       ▼
GroupBy                   Pivot
(ยอดขายต่อ Region)    (เปรียบเทียบ Customer Type)
   │                       │
   ▼                       ▼
Bar Chart              Bar Chart
   │                       │
   └──────────┬────────────┘
              ▼
        Excel Writer (รายงานสรุป)
```

---

### Node 1: PostgreSQL Connector

**ค้นหาใน Node Repository:** `Database → Connection → PostgreSQL Connector`

> ถ้าหาไม่เจอให้ลองค้นหา: `DB Connector` หรือ `Database Connector`

**เชื่อมต่อ:** วาง node ไว้บน Canvas (ไม่ต้องต่อ input ใดๆ — เป็น node แรกสุด)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Connection Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Database Dialect** | `Default` | ใช้ PostgreSQL dialect |
| **Driver Name** | `PostgreSQL` | เลือก driver จาก dropdown |
| **Hostname** | `ep-delicate-frost-aoasxaid-pooler.c-2.ap-southeast-1.aws.neon.tech` | Host ของ Neon (Pooler URL) |
| **Port** | `5432` | Port มาตรฐาน PostgreSQL |
| **Database name** | `neondb` | ชื่อ database |
| **Username** | `neondb_owner` | ชื่อผู้ใช้ |
| **Password** | `npg_2qkIU8PWMNVQ` | รหัสผ่าน |

**แท็บ JDBC Parameters** (สำคัญมาก! ต้องตั้งค่า SSL):

คลิก **Add** เพิ่ม Parameter ดังนี้:

| Parameter Name | Value | คำอธิบาย |
|----------------|-------|----------|
| `sslmode` | `require` | บังคับใช้ SSL encryption — Neon กำหนดบังคับ |
| `channel_binding` | `require` | เพิ่มความปลอดภัยในการ authenticate |

> ⚠️ **ถ้าไม่ตั้งค่า SSL → จะ connect ไม่ได้!** Neon ปฏิเสธ connection ที่ไม่ใช้ SSL ทุกกรณี

**ทางเลือก: ใช้ JDBC URL โดยตรง**

ถ้าต้องการความแน่นอน ให้เลือก **Custom JDBC URL** แล้วใส่ค่าดังนี้:

```
jdbc:postgresql://ep-delicate-frost-aoasxaid-pooler.c-2.ap-southeast-1.aws.neon.tech:5432/neondb?sslmode=require&channel_binding=require
```

กด **Test connection** → ต้องเห็นข้อความ **"Connection successful"** สีเขียว

กด **OK** → **Execute**

**สัญลักษณ์ไฟเขียว (✅) บน node = เชื่อมต่อสำเร็จ**

---

### Node 2: DB Table Selector

**ค้นหาใน Node Repository:** `Database → Query → DB Table Selector`

**จุดประสงค์:** เลือกตารางจาก database โดยไม่ต้องเขียน SQL

**เชื่อมต่อ:** Output (DB Connection port) ของ PostgreSQL Connector → Input ของ DB Table Selector

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Schema** | `public` | Schema ที่ตารางอยู่ |
| **Table** | `sales_data_workshop` | ชื่อตารางที่ต้องการ |

> ถ้า dialog มีปุ่ม **Browse** → คลิก Browse จะเห็นรายชื่อตารางทั้งหมดใน neondb

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวา → **DB Table Spec** → เห็น 9 columns ของตาราง

---

### Node 3: DB Row Filter

**ค้นหาใน Node Repository:** `Database → Manipulation → Row → DB Row Filter`

**จุดประสงค์:** กรองข้อมูลด้วย SQL WHERE clause — ดึงเฉพาะข้อมูลที่ต้องการ ลดภาระ network

**เชื่อมต่อ:** Output ของ DB Table Selector → Input ของ DB Row Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Filter Criteria:**

เลือก **Add condition** แล้วตั้งค่า:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Column** | `date` | กรองตาม column date |
| **Operator** | `>=` | มากกว่าหรือเท่ากับ |
| **Value** | `2024-01-01` | ดึงข้อมูลตั้งแต่ต้นปี 2024 |

> **ทางเลือก: ใช้ Custom SQL WHERE Clause**
> เปลี่ยนเป็น Custom SQL แล้วพิมพ์:
> ```sql
> date >= '2024-01-01' AND total_sales > 0
> ```

กด **OK** → **Execute**

> **ทำไมต้องกรองที่ Database (ไม่กรองใน KNIME)?**
> ถ้า filter ที่ DB ก่อน → ดึงข้อมูลมาน้อยกว่า → เร็วกว่ามาก
> ถ้าดึงมาทั้งหมดก่อนแล้วค่อย filter ใน KNIME → เปลืองเวลาและ network โดยไม่จำเป็น

---

### Node 4: DB Column Filter *(ไม่บังคับ)*

**ค้นหาใน Node Repository:** `Database → Manipulation → Column → DB Column Filter`

**จุดประสงค์:** เลือก columns ที่ต้องการก่อน DB Reader — ลดขนาดข้อมูลที่ดึงมา

**เชื่อมต่อ:** Output ของ DB Row Filter → Input ของ DB Column Filter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ฝั่ง Include — เลือก columns ที่ต้องการ:**
- `transaction_id`, `date`, `product_category`, `product_name`
- `quantity`, `total_sales`, `region`, `customer_type`
- (Exclude `price_per_unit` ถ้าไม่ต้องการ)

กด **OK** → **Execute**

---

### Node 5: DB Reader

**ค้นหาใน Node Repository:** `Database → Query → DB Reader`

**จุดประสงค์:** Execute query ทั้งหมดและดึงผลลัพธ์เข้ามาเป็น KNIME Table จริงๆ — นี่คือจุดที่ข้อมูลออกจาก database เข้า KNIME

**เชื่อมต่อ:** Output ของ DB Row Filter (หรือ DB Column Filter) → Input ของ DB Reader

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Cache entire table in memory** | ✅ ติ๊ก | เก็บข้อมูลใน RAM เพื่อความเร็ว — เหมาะกับข้อมูลไม่ใหญ่มาก |

กด **OK** → **Execute**

**ตรวจสอบผล:** คลิกขวา → **File Table** → เห็นข้อมูลตาราง sales_data_workshop

> ✅ **หลังจาก Node นี้ — ข้อมูลอยู่ใน KNIME แล้ว** สามารถใช้ Node ปกติทุกตัวได้เลย

---

### ทางเลือก: DB Query Reader (SQL แบบ Custom)

**ค้นหาใน Node Repository:** `Database → Query → DB Query Reader`

**จุดประสงค์:** เขียน SQL Query เองได้ทั้งหมด — เหมาะสำหรับ Query ซับซ้อน

**เชื่อมต่อ:** Output ของ PostgreSQL Connector → Input ของ DB Query Reader

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ช่อง SQL Query — พิมพ์:**
```sql
SELECT
    transaction_id,
    date,
    product_category,
    product_name,
    quantity,
    price_per_unit,
    total_sales,
    region,
    customer_type
FROM sales_data_workshop
WHERE date >= '2024-01-01'
  AND total_sales > 0
ORDER BY date ASC
```

กด **OK** → **Execute**

> **เหมาะกับกรณี:**
> - ต้องการ JOIN หลายตาราง
> - ต้องการ Aggregate ที่ Database ก่อน (เร็วกว่ามาก)
> - ต้องการใช้ SQL function เช่น `DATE_TRUNC`, `EXTRACT`, `CASE WHEN`

---

### Node 6: String to Date&Time

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → String to Date&Time`

**จุดประสงค์:** แปลง column `date` ให้เป็น Date type จริงๆ เพื่อให้สามารถกรองตามช่วงวันที่หรือ GroupBy ตามเดือนได้

**เชื่อมต่อ:** Output ของ DB Reader → Input ของ String to Date&Time

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Selected columns** | `date` | เลือก column ที่ต้องการแปลง |
| **Date format** | `yyyy-MM-dd` | รูปแบบวันที่ในฐานข้อมูล เช่น 2024-03-15 |
| **Type** | `Date` (ไม่ใช่ DateTime) | ข้อมูลใน DB เป็น DATE ไม่มีเวลา |
| **Replace column** | ✅ ติ๊ก | แทนที่ column เดิม (ไม่สร้างใหม่) |

กด **OK** → **Execute**

---

### Node 7: Math Formula — คำนวณ Revenue per Unit

**ค้นหาใน Node Repository:** `Manipulation → Column → Convert & Replace → Math Formula`

**จุดประสงค์:** คำนวณ column ใหม่จาก column ที่มีอยู่

**เชื่อมต่อ:** Output ของ String to Date&Time → Input ของ Math Formula

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**ตัวอย่างที่ 1: คำนวณ Average Price Check**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Expression** | `$total_sales$ / $quantity$` | ราคาเฉลี่ยต่อหน่วย |
| **New column name** | `avg_price_check` | ชื่อ column ใหม่ |
| **Result type** | `Double` | ประเภทผลลัพธ์ |

> **ประโยชน์สำหรับ Audit:** เปรียบเทียบ `avg_price_check` กับ `price_per_unit` — ถ้าต่างกันมาก อาจมีความผิดปกติ

กด **OK** → **Execute**

---

### Node 8: GroupBy — ยอดขายต่อ Region

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → GroupBy`

**จุดประสงค์:** รวมยอดขายจัดกลุ่มตาม Region และ Product Category

**เชื่อมต่อ:** Output ของ Math Formula → Input ของ GroupBy

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Groups:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Group column(s)** | `region`, `product_category` |

**แท็บ Manual Aggregation:**

| Column | Aggregation | New Column Name |
|--------|-------------|-----------------|
| `total_sales` | `Sum` | `total_sales_sum` |
| `quantity` | `Sum` | `quantity_sum` |
| `transaction_id` | `Count` | `transaction_count` |
| `total_sales` | `Mean` | `avg_sales` |
| `total_sales` | `Max` | `max_sales` |

**แท็บ Advanced Settings:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Sort groups** | ✅ ติ๊ก — เรียงกลุ่มตามตัวอักษร |
| **Missing values handling** | `Ignore` |

กด **OK** → **Execute**

**ตรวจสอบผล:** ตารางสรุปยอดขายแต่ละ Region × Product Category

---

### Node 9: Pivot — เปรียบเทียบ Customer Type ต่อ Region

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Pivot`

**จุดประสงค์:** เปรียบเทียบยอดขายระหว่าง Customer Type แต่ละประเภทในแต่ละ Region

**เชื่อมต่อ:** Output ของ Math Formula → Input ของ Pivot (แยกสาขาจาก GroupBy)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ส่วน | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Group columns** | `region` | แต่ละแถวคือ 1 Region |
| **Pivot columns** | `customer_type` | แต่ละ column คือ 1 ประเภทลูกค้า |
| **Aggregation** | `Sum` บน `total_sales` | รวมยอดขายของแต่ละกลุ่ม |
| **Ignore missing values** | ✅ ติ๊ก | |

กด **OK** → **Execute**

**ตัวอย่างผลลัพธ์:**

| region | Corporate_total_sales | Individual_total_sales | SME_total_sales |
|--------|----------------------|------------------------|-----------------|
| North | 2,450,000 | 1,230,000 | 890,000 |
| South | 1,870,000 | 1,540,000 | 720,000 |
| East | 3,120,000 | 980,000 | 1,100,000 |
| West | 1,650,000 | 2,100,000 | 560,000 |

---

### Node 10: Sorter — เรียงลำดับก่อน Visualize

**ค้นหาใน Node Repository:** `Manipulation → Row → Transform → Sorter`

**จุดประสงค์:** เรียงข้อมูลตาม `total_sales_sum` จากมากไปน้อย

**เชื่อมต่อ:** Output ของ GroupBy → Input ของ Sorter

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Column** | `total_sales_sum` |
| **Sort order** | `Descending` (มากไปน้อย) |

กด **OK** → **Execute**

---

### Node 11: Bar Chart — ยอดขายต่อ Region

**ค้นหาใน Node Repository:** `Views → JavaScript → Bar Chart`

**จุดประสงค์:** แสดงผลยอดขายรวมแต่ละ Region เป็น Bar Chart แบบ interactive

**เชื่อมต่อ:** Output ของ Sorter → Input ของ Bar Chart

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Options:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Category column** | `region` | แกน X — ชื่อ Region |
| **Frequency columns** | `total_sales_sum` | แกน Y — ยอดขาย |
| **Chart title** | `ยอดขายรวมต่อ Region` | หัวข้อกราฟ |
| **Orientation** | `Vertical` | แท่งตั้ง |
| **Color** | เลือกสีที่ต้องการ | |

กด **OK** → **Execute** → คลิกขวา → **View: Bar Chart** เพื่อดูกราฟ

---

### Node 12: Bar Chart — เปรียบเทียบ Category ต่อ Region

**ตั้งค่าเหมือน Node 11 แต่:**

| ช่อง | ค่าที่ตั้ง |
|------|-----------|
| **Category column** | `product_category` |
| **Frequency columns** | `total_sales_sum` |
| **Group columns** | `region` (ถ้า Bar Chart รองรับ stacked) |
| **Chart title** | `ยอดขายต่อหมวดหมู่สินค้า` |

กด **OK** → **Execute**

---

### Node 13: Excel Writer — Export รายงาน

**ค้นหาใน Node Repository:** `IO → Write → Excel Writer`

**จุดประสงค์:** Export ผลการวิเคราะห์ทั้งหมดเป็นไฟล์ Excel หลายชีต

**เชื่อมต่อ:** Output ของ GroupBy → Input ของ Excel Writer (สายแรก)

**ดับเบิ้ลคลิก** เพื่อเปิด Dialog:

**แท็บ Settings:**

| ช่อง | ค่าที่ตั้ง | คำอธิบาย |
|------|-----------|----------|
| **Output location** | คลิก Browse → เลือก folder ที่ต้องการ | ที่เก็บไฟล์ |
| **File name** | `Sales_Report_NeonDB.xlsx` | ชื่อไฟล์ |
| **If file exists** | `Overwrite` | เขียนทับถ้าไฟล์มีอยู่แล้ว |
| **Sheet name** | `Regional Summary` | ชื่อ Sheet |
| **Write row ID** | ❌ ไม่ติ๊ก | ไม่ต้องการ Row ID ในรายงาน |
| **Write column headers** | ✅ ติ๊ก | ต้องการ header row |

> **เพิ่ม Sheet ที่ 2:** ต่อ Pivot output → Excel Writer ตัวเดียวกัน (ถ้า Excel Writer รองรับ Multiple Sheets) หรือใช้ Excel Sheet Appender แยก

กด **OK** → **Execute**

**ตรวจสอบผล:** ไฟล์ `Sales_Report_NeonDB.xlsx` ถูกสร้างในโฟลเดอร์ที่เลือก

---

## 7.4 Workflow สำเร็จรูป — ภาพรวมทั้งหมด

```
[PostgreSQL Connector]
        │ (DB Connection)
        ▼
[DB Table Selector]
  Schema: public
  Table: sales_data_workshop
        │ (DB Data)
        ▼
[DB Row Filter]
  WHERE date >= '2024-01-01'
        │ (DB Data - filtered)
        ▼
[DB Reader]
  Cache in memory: ✅
        │ (KNIME Table)
        ▼
[String to Date&Time]
  Column: date
  Format: yyyy-MM-dd
        │
        ▼
[Math Formula]
  avg_price_check = total_sales / quantity
        │
   ┌────┴──────────────────┬─────────────────┐
   ▼                       ▼                 ▼
[GroupBy]              [Pivot]          [Column Filter]
 Region,Category    Region×CustomerType  เลือก columns
 Sum(total_sales)   Sum(total_sales)     ที่ต้องการ
 Count(tx_id)
   │                    │                    │
   ▼                    ▼                    ▼
[Sorter]           [Bar Chart 2]       [Statistics]
Desc by sales      Customer Type        ดู Summary
   │
   ▼
[Bar Chart 1]
Regional Sales
   │
   └──────────────────────┐
                          ▼
                    [Excel Writer]
                    Sales_Report_NeonDB.xlsx
```

---

## 7.5 การ Debug ปัญหาที่พบบ่อย

| ปัญหา | สาเหตุ | วิธีแก้ไข |
|-------|--------|-----------|
| `Connection refused` | SSL ไม่ถูกตั้งค่า | เพิ่ม `sslmode=require` ใน JDBC Parameters |
| `SCRAM authentication failed` | channel_binding ขาด | เพิ่ม `channel_binding=require` ใน JDBC Parameters |
| `Table not found` | Schema ไม่ถูกต้อง | ตรวจสอบว่า Schema = `public` |
| `Timeout` | Connection pooler busy | รอสักครู่แล้ว Execute ใหม่ หรือลด rows ที่ดึง |
| `Column date type error` | Date column เป็น String | เพิ่ม String to Date&Time node หลัง DB Reader |
| Node สีแดง (Error) | Password ผิด | ตรวจสอบ Username/Password ใน PostgreSQL Connector |

---

## 7.6 Advanced: DB Query Reader พร้อม SQL Analysis

สำหรับผู้เรียนที่มีพื้นฐาน SQL — ดึงข้อมูลพร้อม Aggregate จาก Database เลย:

```sql
-- Query 1: ยอดขายรายเดือนต่อ Region
SELECT
    DATE_TRUNC('month', date) AS month,
    region,
    SUM(total_sales) AS monthly_sales,
    COUNT(*) AS transaction_count,
    AVG(total_sales) AS avg_transaction_size
FROM sales_data_workshop
WHERE date >= '2024-01-01'
GROUP BY DATE_TRUNC('month', date), region
ORDER BY month, region;

-- Query 2: Top 5 Product ยอดขายสูงสุด
SELECT
    product_name,
    product_category,
    SUM(total_sales) AS total_revenue,
    SUM(quantity) AS total_qty
FROM sales_data_workshop
GROUP BY product_name, product_category
ORDER BY total_revenue DESC
LIMIT 5;

-- Query 3: Customer Type Analysis
SELECT
    customer_type,
    region,
    COUNT(*) AS num_transactions,
    SUM(total_sales) AS total_sales,
    AVG(total_sales) AS avg_sales,
    MAX(total_sales) AS max_sales
FROM sales_data_workshop
GROUP BY customer_type, region
ORDER BY customer_type, total_sales DESC;
```

> **วิธีใช้:** คัดลอก SQL แต่ละ Query ใส่ใน **DB Query Reader** แยกแต่ละ node → Execute → ได้ผลลัพธ์พร้อม Visualize ทันที

---

## 7.7 สรุป Section 7 — Database Integration

| Concept | Node | จุดสำคัญ |
|---------|------|----------|
| เชื่อมต่อ DB | PostgreSQL Connector | ต้องใส่ SSL parameters เสมอ |
| เลือกตาราง | DB Table Selector | ง่ายกว่าเขียน SQL |
| กรองข้อมูล | DB Row Filter | Filter ที่ DB ก่อน = เร็วกว่า |
| ดึงข้อมูล | DB Reader | จุดเปลี่ยนจาก DB → KNIME Table |
| Query ซับซ้อน | DB Query Reader | ใช้ SQL เต็มรูปแบบ |
| วิเคราะห์ | GroupBy + Pivot | เหมือน Day 1 Labs ปกติ |
| รายงาน | Excel Writer | Export หลาย Sheet ได้ |

> **ข้อแนะนำสำหรับงาน Internal Audit ธนาคารกรุงศรี:**
> ในการใช้งานจริง ให้บันทึก workflow ที่เชื่อมกับ DB ไว้เป็น template
> ครั้งต่อไปเพียงแก้ไข SQL Query หรือช่วงวันที่ใน DB Row Filter
> แล้ว Execute ใหม่ — ได้รายงานล่าสุดทันที โดยไม่ต้อง Export CSV ก่อนเสมอ

---

*จัดทำโดย: อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.*
*Section 7 — เพิ่มเติมสำหรับ Day 1: 18 พฤษภาคม 2569*
