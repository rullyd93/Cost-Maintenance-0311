# Dashboard Power BI - Repair & Maintenance Cost Monitoring

## 📊 Deskripsi Dashboard
Dashboard ini dirancang untuk monitoring dan analisis **Repair & Maintenance Cost** dengan perbandingan antara **Plan Cost vs Actual Cost** terhadap **Target Produksi**, dengan periode monitoring harian, mingguan, bulanan, kuartalan, dan tahunan.

---

## 📋 Struktur Data yang Dibutuhkan

### 1. **Tabel: MaintenanceData**
Date (DateTime)
Section (Text) - Nama Bagian/Area
Equipment (Text) - Nama Equipment
Equipment_Category (Text) - Kategori Equipment
Material_Type (Text) - Jenis Material
Description (Text) - Deskripsi Maintenance
Plan_Cost (Decimal) - Biaya Perencanaan
Actual_Cost (Decimal) - Biaya Aktual
Target_Production (Decimal) - Target Produksi (Unit/Jam)
Actual_Production (Decimal) - Produksi Aktual (Unit/Jam)
Maintenance_Type (Text) - Preventive / Breakdown / Scheduled
Material_Cost (Decimal) - Biaya Material yang Dipakai
Labour_Cost (Decimal) - Biaya Tenaga Kerja
Status (Text) - Completed / Ongoing / Pending

### 2. **Tabel: EquipmentMaster**
Equipment_ID (Text)
Equipment_Name (Text)
Equipment_Category (Text)
Section (Text)
Installation_Date (DateTime)

### 3. **Tabel: MaterialMaster**
Material_ID (Text)
Material_Name (Text)
Material_Type (Text)
Unit_Cost (Decimal)
Stock_Level (Decimal)

### 4. **Tabel: Calendar (Date Dimension)**
Date (DateTime)
Year (Integer)
Quarter (Text)
Month (Integer)
Month_Name (Text)
Week (Integer)
Day_Of_Week (Text)
Day (Integer)
YearMonth (Text)
YearQuarter (Text)

---

## 🎯 Metrik & KPI yang Akan Ditampilkan

### **1. Bagian Atas - Summary Cards**
- **Total Actual Cost** - Total biaya maintenance yang sudah terpakai
- **Total Plan Cost** - Total biaya yang direncanakan
- **Variance (Cost)** - Selisih Plan vs Actual
- **Variance %** - Persentase perbedaan
- **Total Production (Actual)** - Total produksi aktual
- **Target Production** - Target produksi yang direncanakan
- **Production Achievement %** - Capaian produksi terhadap target
- **CPH All Unit** - Cost Per Hour untuk semua unit

### **2. Visualisasi Utama**

#### **A. Plan Cost vs Actual Cost (Time Series)**
- Grafik garis yang membandingkan Plan vs Actual Cost
- X-axis: Periode (Daily/Weekly/Monthly/Quarterly/Yearly)
- Y-axis: Cost (IDR)
- Trend line untuk analisis

#### **B. Cost Breakdown by Material Type (Pie/Donut)**
- Proporsi cost berdasarkan jenis material
- Interaktif untuk drill-down

#### **C. Cost by Equipment Category (Bar Chart)**
- Horizontal bar chart menampilkan cost per kategori equipment
- Ranking tertinggi ke terendah

#### **D. Production vs Target (Gauge/Indicator)**
- Circular gauge menunjukkan achievement percentage
- Target produksi vs actual production

#### **E. Cost by Section/Area (Stacked Bar)**
- Cost distribution per section
- Plan vs Actual side-by-side

#### **F. Material Cost Detail (Table)**
- Daftar detail material yang digunakan
- Columns: Material Name, Type, Quantity, Unit Cost, Total Cost, Date
- Sortable dan filterable

#### **G. CPH All Unit by Period (Line Chart)**
- Cost Per Hour trend over time
- Multiple lines untuk berbagai sections

#### **H. Maintenance Type Distribution (Pie)**
- Breakdown Preventive vs Breakdown vs Scheduled

---

## 🔧 DAX Formulas yang Diperlukan

### **Measures:**

```dax
// Total Metrics
Total Actual Cost = SUM('MaintenanceData'[Actual_Cost])
Total Plan Cost = SUM('MaintenanceData'[Plan_Cost])
Total Material Cost = SUM('MaintenanceData'[Material_Cost])
Total Labour Cost = SUM('MaintenanceData'[Labour_Cost])

// Variance Metrics
Cost Variance = [Total Plan Cost] - [Total Actual Cost]
Cost Variance % = DIVIDE([Cost Variance], [Total Plan Cost], 0) * 100

// Production Metrics
Total Actual Production = SUM('MaintenanceData'[Actual_Production])
Total Target Production = SUM('MaintenanceData'[Target_Production])
Production Achievement % = DIVIDE([Total Actual Production], [Total Target Production], 0) * 100

// CPH Calculation
CPH All Unit = DIVIDE([Total Actual Cost], [Total Actual Production], 0)

// Period-based Calculations
Actual Cost YTD = CALCULATE([Total Actual Cost], DATESYTD('Calendar'[Date]))
Plan Cost YTD = CALCULATE([Total Plan Cost], DATESYTD('Calendar'[Date]))

// Performance Indicators
Cost Status = 
    IF([Cost Variance %] < -10, "Over Budget", 
       IF([Cost Variance %] > 10, "Under Budget", "On Track"))

Production Status = 
    IF([Production Achievement %] >= 95, "Good",
       IF([Production Achievement %] >= 80, "Fair", "Poor"))
