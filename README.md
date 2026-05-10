<p align="center">
  <img src="https://storage.googleapis.com/kaggle-datasets-images/10074547/15724359/c9f62588cfe1713b0398d714166a0de1/dataset-cover.jpg?t=2026-04-14-05-41-39" width="100%" alt="Ember Global Electricity Data Banner"/>
</p>

<h1 align="center">⚡ Ember Global Electricity Data</h1>
<h3 align="center">Monthly Long Format — Preprocessing Pipeline</h3>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
  <img src="https://img.shields.io/badge/Pandas-2.0+-150458?style=for-the-badge&logo=pandas&logoColor=white" alt="Pandas"/>
  <img src="https://img.shields.io/badge/NumPy-1.26+-013243?style=for-the-badge&logo=numpy&logoColor=white" alt="NumPy"/>
  <img src="https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge" alt="Status"/>
</p>

---

## 📋 Deskripsi

Dataset ini menyediakan **time-series bulanan** statistik listrik global yang dikurasi oleh [Ember](https://ember-climate.org/), sebuah think tank energi independen. Data mencakup **pembangkitan listrik, emisi sektor ketenagalistrikan, dan kapasitas pembangkit** dari lebih dari **80 negara dan region** di seluruh dunia.

Project ini berfokus pada **preprocessing & data cleaning** agar dataset siap digunakan untuk analisis lanjutan.

---

## 📊 Statistik Dataset

| Metrik | Nilai |
|:-------|------:|
| 📝 Total Baris (Mentah) | **501.483** |
| 📁 Kolom Awal | **18** |
| 📁 Kolom Setelah Preprocessing | **24** |
| 🌍 Jumlah Negara | **87** |
| 🗺️ Jumlah Region | **11** |
| 📅 Rentang Waktu | **Des 1998 — Mar 2026** |
| ❌ Duplikat | **0** |

---

## 🔧 Pipeline Preprocessing

Berikut adalah **10 tahapan preprocessing** yang dilakukan pada `main.py`:

### 1️⃣ Load Data
> Membaca file CSV mentah (`monthly_full_release_long_format.csv`) ke dalam DataFrame.

### 2️⃣ Ekstrak Format Date
> Mengubah kolom `Date` dari string ke tipe `datetime`, lalu mengekstrak komponen:
> - `Year` — Tahun (2024)
> - `Month` — Bulan (1–12)
> - `Quarter` — Kuartal (1–4)
> - `YearMonth` — Format gabungan ("2024-01")

### 3️⃣ Cek Data Kosong & Duplikat
> - Mengidentifikasi missing values per kolom
> - Mengecek baris duplikat → **Hasil: 0 duplikat** ✅
> - Missing values pada `ISO 3 code`, `Continent`, dll. wajar karena baris Region memang tidak punya data tersebut

### 4️⃣ Cek & Perbaikan Tipe Data
> - Kolom flag keanggotaan (`EU`, `OECD`, `G20`, `G7`, `ASEAN`) → dikonversi dari `float` ke `bool`
> - Kolom kategorikal (`Area`, `Category`, `Variable`, dll.) → dikonversi ke tipe `category` untuk efisiensi memori

### 5️⃣ Menghindari Double Counting
> Dataset memiliki data **agregat** dan **individual**:
> ```
> Renewables (agregat) = Wind + Solar + Hydro + Bioenergy + Other Renewables
> Fossil (agregat)     = Coal + Gas + Other Fossil
> ```
> Jika dijumlahkan bersamaan → terjadi **penghitungan ganda**. Solusi: ditandai dengan kolom `Adalah_Agregat`.

### 6️⃣ Memisahkan Region & Country
> Data dipisah menjadi:
> - **`data_negara`** — 87 negara (445.688 baris)
> - **`data_region`** — 11 region seperti EU, ASEAN, World (55.795 baris)

### 7️⃣ Label Provisional
> Data 12 bulan terakhir dari setiap negara ditandai sebagai **provisional** (kemungkinan masih direvisi). Ditandai dengan kolom `Adalah_Provisional`.

### 8️⃣ Pengecekan Ketersediaan Data
> - Rata-rata ketersediaan: **108 bulan/negara**
> - Missing `Value`: hanya **0.51%** dari data negara

### 9️⃣ Cek Outlier (Metode IQR)
> Menggunakan **Interquartile Range (IQR)**:
> ```
> Q1           = Persentil ke-25
> Q3           = Persentil ke-75
> IQR          = Q3 - Q1
> Batas Bawah  = Q1 - 1.5 × IQR
> Batas Atas   = Q3 + 1.5 × IQR
> ```
> Terdeteksi **45.053 outlier** — **tidak dihapus** karena bisa merupakan data valid dari negara besar (China, US, India).

### 🔟 Ringkasan & Simpan Data
> Menyimpan 3 file hasil preprocessing ke folder `dataset/`.

---

## 📂 Struktur Project

```
📦 Ember Global Electricity Data Monthly Long Format
├── 📁 dataset/
│   ├── monthly_full_release_long_format.csv   # Dataset mentah (sumber)
│   ├── df_clean.csv                           # Hasil: semua data (501K baris)
│   ├── df_country_clean.csv                   # Hasil: hanya negara (445K baris)
│   └── df_region_clean.csv                    # Hasil: hanya region (55K baris)
├── 📄 main.py                                 # Script preprocessing utama
├── 📓 main.ipynb                              # Notebook (job list & dokumentasi)
├── 📄 requirements.txt                        # Dependensi Python
└── 📄 README.md                               # Dokumentasi ini
```

---

## 🚀 Cara Menjalankan

```bash
# 1. Clone repository
git clone https://github.com/Bujanking1660/preprocessing-ember-global-electricity-data-montly.git
cd preprocessing-ember-global-electricity-data-montly

# 2. Buat virtual environment (opsional, tapi disarankan)
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS/Linux

# 3. Install dependensi
pip install -r requirements.txt

# 4. Jalankan preprocessing
python main.py
```

---

## 📌 Output yang Dihasilkan

| File Output | Deskripsi | Jumlah Baris |
|:------------|:----------|-------------:|
| `df_clean.csv` | Data gabungan (negara + region) yang sudah bersih | **501.483** |
| `df_country_clean.csv` | Hanya data per negara | **445.688** |
| `df_region_clean.csv` | Hanya data per region | **55.795** |

**Kolom baru yang ditambahkan:**

| Kolom | Tipe | Keterangan |
|:------|:-----|:-----------|
| `Year` | `int` | Tahun dari kolom Date |
| `Month` | `int` | Bulan (1–12) dari kolom Date |
| `Quarter` | `int` | Kuartal (1–4) dari kolom Date |
| `YearMonth` | `str` | Format "YYYY-MM" |
| `Adalah_Agregat` | `bool` | `True` jika variable adalah gabungan/total |
| `Adalah_Provisional` | `bool` | `True` jika data kemungkinan masih sementara |

---

## 📚 Sumber Data

Dataset diambil dari **Kaggle**:

🔗 [Ember Global Electricity Data: Monthly Long Format](https://www.kaggle.com/datasets) — dikurasi oleh **Ember Climate**

> *Ember adalah think tank energi independen yang menyediakan data dan analisis untuk mempercepat transisi energi global dari bahan bakar fosil ke energi bersih.*

---

