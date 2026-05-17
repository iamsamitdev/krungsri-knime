# คำแนะนำการติดตั้ง KNIME Bundle — สำหรับผู้เรียน

## ✅ วิธีแตกไฟล์ที่ถูกต้อง

แตกไฟล์ `KNIME_MASTER_bundle.7z` ไปที่ **root ของ Drive C เท่านั้น**

```
C:\KNIME_MASTER\        ← ✅ ถูกต้อง
```

---

## ❌ ตำแหน่งที่ห้ามใช้

| ตำแหน่งที่แตกไฟล์ | ผลที่เกิดขึ้น |
|---|---|
| `C:\Users\KNIME_MASTER\` | ❌ path ภายใน conda พัง |
| `C:\Users\ชื่อผู้ใช้\Desktop\KNIME_MASTER\` | ❌ ถ้าชื่อเป็นภาษาไทยหรือมี space จะ error |
| `D:\KNIME_MASTER\` หรือ Drive อื่น | ⚠️ ต้องรัน fix_paths ก่อน |
| ใน subfolder ใดก็ตาม | ❌ ห้ามทั้งหมด |

---

## 🔧 กรณีแตกไฟล์ผิดที่ — วิธีแก้ไข

### ถ้าแตกไปที่ `C:\Users\KNIME_MASTER` หรือ path อื่นที่ไม่ใช่ `C:\KNIME_MASTER`

ต้องทำ 3 ขั้นตอนนี้ก่อนเปิด KNIME:

**ขั้นตอนที่ 1 — แก้ path ภายใน conda environment**

เปิด Command Prompt (Run as Administrator) แล้วรันคำสั่งนี้
(แก้ `NEW_PATH` ให้ตรงกับที่แตกไฟล์จริง):

```cmd
powershell -ExecutionPolicy Bypass -Command "$old='C:\KNIME_MASTER'; $new='C:\Users\KNIME_MASTER'; Get-ChildItem -Path '.\miniconda3\envs\knime_python_env' -Recurse -File -Include '*.cfg','*.bat','*.json','*.txt','*.py','*._pth' | ForEach-Object { $c = Get-Content $_.FullName -Raw -ErrorAction SilentlyContinue; if ($c -and $c.Contains($old)) { $c.Replace($old,$new) | Set-Content $_.FullName -NoNewline }}"
```

> ⚠️ ถ้าแตกไปที่ Drive D ให้เปลี่ยน `C:\Users\KNIME_MASTER` เป็น `D:\KNIME_MASTER`

---

**ขั้นตอนที่ 2 — แก้ไฟล์ knime.ini**

เปิดไฟล์ `knime.ini` ในโฟลเดอร์ KNIME_MASTER ด้วย Notepad
แล้วแก้ 2 บรรทัดนี้ให้ตรงกับ path จริง:

```
-data
C:\Users\KNIME_MASTER\workspace
```

---

**ขั้นตอนที่ 3 — ตั้งค่าใน KNIME Preferences**

เปิด KNIME แล้วไปที่ `Preferences` (มุมขวาบน) ตั้งค่าตามนี้:

| หน้า Preferences | สิ่งที่ต้องตั้งค่า |
|---|---|
| KNIME → Conda | ใส่ path ของโฟลเดอร์ miniconda3 เช่น `C:\Users\KNIME_MASTER\miniconda3` |
| KNIME → Python | เลือก **Conda** → เลือก `knime_python_env` |
| KNIME → Python Deep Learning | เลือก **TensorFlow 2** → เลือก `knime_python_env` ทั้งสองช่อง |

กด **Apply and Close** แล้ว **Restart KNIME**

---

## 🧪 ทดสอบว่าพร้อมใช้งาน

เปิด workflow `TestPythonDeeplearning` แล้ว Execute ทีละ node

| Node | ผลที่ต้องได้ |
|---|---|
| Test Python | TensorFlow 2.13.0, Keras 2.13.1, NumPy, Pandas, Scikit-learn → Status ✅ OK ทุก row |
| Test Deep Learning | Final Accuracy ≥ 0.90, Final Loss < 0.40 → Status ✅ OK |

ถ้า node indicator (วงกลมด้านล่างขวา) เป็นสีเขียว = พร้อมใช้งาน ✅

---

## 📋 Specs ของ Bundle

- **KNIME:** 5.3.x (Portable ZIP)
- **Python:** 3.10.14 (conda env ชื่อ `knime_python_env`)
- **TensorFlow:** 2.13.0 | **Keras:** 2.13.1
- **NumPy:** 1.24.3 | **Pandas:** 2.0.3 | **Scikit-learn:** 1.3.2
- **Conda (Miniconda3):** 26.1.1
