# Machine Learning and Deep Learning with KNIME

> **หลักสูตรอบรมเชิงปฏิบัติการ** สำหรับกลุ่มงานตรวจสอบภายใน  
> ธนาคารกรุงศรีอยุธยา จำกัด (มหาชน) — Bank of Ayudhya (Krungsri)

---

## 📋 ข้อมูลหลักสูตร

| รายการ | รายละเอียด |
|--------|-----------|
| **หลักสูตร** | Machine Learning and Deep Learning with KNIME |
| **กลุ่มเป้าหมาย** | กลุ่มงานตรวจสอบภายใน (Internal Audit Group) |
| **ระดับ** | Beginner to Intermediate |
| **ระยะเวลา** | 3 วัน (24 ชั่วโมง) |
| **วันอบรม** | 18–19 และ 28 พฤษภาคม 2569 |
| **เวลา** | 09:00 – 16:00 น. |
| **วิทยากร** | อาจารย์สามิตร โกยม - สถาบันไอทีจีเนียส |
| **เว็บไซต์** | [www.itgenius.co.th](https://www.itgenius.co.th) |

---

## 🎯 วัตถุประสงค์ของหลักสูตร

หลังจากผ่านการอบรม ผู้เข้าร่วมจะสามารถ:

1. ใช้งาน **KNIME Analytics Platform** เพื่อสร้าง Visual Workflow ด้าน Data Science ได้โดยไม่ต้องเขียนโค้ด
2. เตรียมข้อมูล (ETL) ทำความสะอาด แปลง และสรุปข้อมูลด้วย KNIME Nodes
3. สร้างและประเมิน **Machine Learning Models** สำหรับ Classification, Association Rules และ Clustering
4. เข้าใจหลักการของ **Deep Learning** และสร้างโมเดล Neural Network, CNN และ LSTM ด้วย TensorFlow/Keras ใน KNIME
5. นำความรู้ไปประยุกต์ใช้กับข้อมูลจริงในงาน **ตรวจสอบภายใน** เช่น Fraud Detection, Risk Scoring และ Anomaly Detection

---

## 📅 โครงสร้างหลักสูตร

### วันที่ 1 — KNIME & Data Preparation (18 พ.ค. 2569)

| เวลา | หัวข้อ |
|------|--------|
| 09:00–10:30 | แนะนำ KNIME Analytics Platform และ Node-based Workflow |
| 10:45–12:00 | การนำเข้าข้อมูล (CSV Reader) และสำรวจข้อมูลเบื้องต้น (EDA) |
| 13:00–14:30 | Data Cleaning — Type Conversion, Missing Values, Rule Engine |
| 14:45–16:00 | Data Transformation — Filter, Join, GroupBy, Pivot, Normalization, Visualization |

**Topics ที่ครอบคลุม:**
- KNIME Interface: Node Repository, Workflow Editor, Explorer
- Node Status: Configured / Executed / Error
- CSV Reader, Statistics, Data Explorer, Table View
- String to Number / Date Conversion
- Rule Engine — สร้าง Column ใหม่จาก Business Rules
- Missing Value Handler — Mean, Median, Mode, Fixed Value
- Row Filter, Column Filter, Row Splitter
- Concatenate, Joiner (Inner / Left / Right / Full Join)
- GroupBy, Pivot — สรุปและ Cross-tab ข้อมูล
- Numeric Binner, Normalizer (Min-Max / Z-Score)
- Bar Chart, Line Chart, Scatter Plot, Histogram

**Dataset ที่ใช้:** `financial_transactions.csv`, `sales_data.csv`

---

### วันที่ 2 — Machine Learning (19 พ.ค. 2569)

| เวลา | หัวข้อ |
|------|--------|
| 09:00–10:30 | ML Concepts, Evaluation Metrics (Confusion Matrix, ROC/AUC) |
| 10:45–12:00 | Naive Bayes และ Decision Tree Classifier |
| 13:00–14:30 | k-NN, SVM และ Association Rule Mining |
| 14:45–16:00 | Clustering — k-Means, Hierarchical, DBSCAN |

**Topics ที่ครอบคลุม:**
- Supervised vs Unsupervised Learning
- Confusion Matrix — TP, TN, FP, FN
- Accuracy, Precision, Recall, F1-Score
- ROC Curve และ AUC Score
- Naive Bayes — Bayes' Theorem, Laplace Smoothing
- Decision Tree — Gini Impurity, Information Gain, Max Depth
- k-Nearest Neighbors — Euclidean Distance, Lazy Learner
- Support Vector Machine — Hyperplane, Kernel Trick
- Association Rules — Support, Confidence, Lift, Apriori Algorithm
- k-Means Clustering — Elbow Method
- Hierarchical Clustering — Dendrogram, Linkage Methods
- DBSCAN — Density-based, Outlier Detection

**Dataset ที่ใช้:** `loan_default.csv`, `customer_churn.csv`, `market_basket.csv`

---

### วันที่ 3 — Deep Learning (28 พ.ค. 2569)

| เวลา | หัวข้อ |
|------|--------|
| 09:00–10:30 | ML vs Deep Learning, Neural Network Architecture |
| 10:45–12:00 | Activation Functions, Backpropagation, Overfitting & Dropout |
| 13:00–14:30 | TensorFlow/Keras ใน KNIME, CNN — Image Classification |
| 14:45–16:00 | LSTM — Time Series Forecasting, Capstone Project |

**Topics ที่ครอบคลุม:**
- ML vs Deep Learning — เมื่อไรควรใช้ Deep Learning
- Neuron, Weight, Bias, Activation Function
- ReLU, Sigmoid, Softmax, Tanh
- Forward Pass, Backward Pass, Loss Function
- Learning Rate, Optimizer (Adam, SGD)
- Overfitting, Dropout, Early Stopping, Regularization
- TensorFlow / Keras Integration ใน KNIME
- CNN — Convolution Layer, Pooling Layer, Feature Map, Flatten
- LSTM — Forget Gate, Input Gate, Output Gate
- Vanishing Gradient Problem
- Sliding Window Approach สำหรับ Time Series

**Dataset ที่ใช้:** `loan_default.csv`, `customer_churn.csv`, `time_series_daily_transactions.csv`

---

## 📁 โครงสร้าง Repository

```
krungsri-knime/
│
├── README.md                          # ไฟล์นี้
│
├── notes/                             # เนื้อหาการเรียนรู้แต่ละวัน
│   ├── Day1-KNIME-Data-Preparation.md
│   ├── Day2-KNIME-Machine-Learning.md
│   └── Day3-KNIME-Deep-Learning.md
│
├── outline/                           # Course Outline และแผนการสอน
│   └── Machine-Learning-and-Deep-Learning-with-KNIME.md
│
├── datasets/                          # ไฟล์ข้อมูลสำหรับ Workshop
│   ├── financial_transactions.csv     # 350 rows — Fraud Detection
│   ├── sales_data.csv                 # 250 rows — Sales Analysis
│   ├── loan_default.csv               # 400 rows — Credit Risk
│   ├── customer_churn.csv             # 350 rows — Churn Prediction
│   ├── market_basket.csv              # 778 rows — Association Rules
│   └── time_series_daily_transactions.csv  # 730 rows — LSTM Forecasting
│
└── presentations/                        # สไลด์และสื่อประกอบการสอน
    ├── Day1-KNIME-Data-Preparation.pptx
    ├── Day2-KNIME-Machine-Learning.pptx
    └── Day3-KNIME-Deep-Learning.pptx
```

---

## 🗂️ Dataset Reference

| ไฟล์ | Rows | Columns | ใช้ใน | Target |
|------|------|---------|-------|--------|
| `financial_transactions.csv` | 350 | 15 | Day 1 | `is_fraud` |
| `sales_data.csv` | 250 | 16 | Day 1 | `total_amount` |
| `loan_default.csv` | 400 | 22 | Day 2–3 | `default` |
| `customer_churn.csv` | 350 | 21 | Day 2–3 | `churn` |
| `market_basket.csv` | 778 | 8 | Day 2 | Association Rules |
| `time_series_daily_transactions.csv` | 730 | 17 | Day 3 | `next_day_total_amount` |

> **Public Dataset References สำหรับค้นคว้าเพิ่มเติม:**  
> [Kaggle Datasets](https://www.kaggle.com/datasets) | [UCI ML Repository](https://archive.ics.uci.edu) | [data.go.th](https://data.go.th)

---

## 🛠️ เครื่องมือที่ต้องติดตั้ง

### KNIME Analytics Platform
- ดาวน์โหลดฟรีได้ที่: [www.knime.com/downloads](https://www.knime.com/downloads)
- เวอร์ชันแนะนำ: **KNIME 5.x** (64-bit)
- RAM: ขั้นต่ำ 8 GB (แนะนำ 16 GB)

### KNIME Extensions ที่ต้องติดตั้งเพิ่ม
```
File > Install Extensions:
- KNIME Python Integration
- KNIME Deep Learning - Keras Integration
- KNIME Statistics (อยู่ใน Core แล้ว)
```

### Python Environment (สำหรับ Day 3)
```bash
pip install tensorflow==2.13.0
pip install keras
pip install numpy pandas scikit-learn matplotlib
```
---

## 🏢 เกี่ยวกับผู้จัดอบรม

**บริษัท ไอทีจีเนียส เอ็นจิเนียริ่ง จำกัด**  
IT Genius Engineering Co., Ltd.

- Training Center ด้าน IT ชั้นนำของประเทศไทย
- เชี่ยวชาญด้าน DevOps, AI/ML, Cloud Computing, Mobile Development
- ประสบการณ์ฝึกอบรมองค์กรชั้นนำมากกว่า 10 ปี
- 🌐 [www.itgenius.co.th](https://www.itgenius.co.th)

---

## 📄 License

เนื้อหาและ Dataset ในหลักสูตรนี้จัดทำขึ้นเพื่อการศึกษาภายในองค์กร  
ห้ามนำไปเผยแพร่หรือใช้เพื่อการค้าโดยไม่ได้รับอนุญาต

© 2569 IT Genius Engineering Co., Ltd. — Bank of Ayudhya (Krungsri)
