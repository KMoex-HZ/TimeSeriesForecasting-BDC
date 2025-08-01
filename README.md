# 📊 Big Data Challenge: Analisis dan Prediksi Penjualan

<div align="center">
  <img src="output.png" width="500" alt="K-Means Clustering Visualization">
</div>

Proyek ini adalah solusi untuk tantangan "Big Data Challenge" yang berfokus pada analisis deret waktu (Time Series Analysis) dan pembangunan model prediksi penjualan. Dataset yang digunakan berisi data transaksi penjualan dan dianalisis menggunakan Python dengan library Pandas, Matplotlib, Seaborn, Statsmodels (untuk SARIMAX), dan Prophet.

## 🚀 Tujuan Proyek

Tujuan utama dari proyek ini adalah untuk:
- Memuat dan melakukan eksplorasi awal pada dataset penjualan
- Menganalisis pola tren, musiman, dan sisa dalam data penjualan
- Membangun dan mengevaluasi model prediksi menggunakan SARIMAX dan Prophet
- Membandingkan kinerja kedua model untuk menentukan model terbaik untuk prediksi penjualan

## 🗂️ Struktur Proyek

```
.
├── data/
│   └── data.csv
│   └── data.xls
├── notebooks/
│   └── BigDataChallenge.ipynb
└── README.md
```

- **data/**: Berisi dataset penjualan yang digunakan (data.csv, data.xls)
- **notebooks/**: Berisi notebook Jupyter utama (BigDataChallenge.ipynb) yang mendokumentasikan seluruh proses analisis dan prediksi
- **README.md**: File dokumentasi proyek ini

## 🛠️ Library yang Digunakan

- **pandas**: Untuk manipulasi dan analisis data
- **matplotlib**: Untuk visualisasi data (dasar)
- **seaborn**: Untuk visualisasi data (statistik dan estetika)
- **statsmodels**: Untuk dekomposisi deret waktu dan model SARIMAX
- **prophet**: Untuk model prediksi Prophet
- **numpy**: Untuk operasi numerik
- **sklearn**: Untuk metrik evaluasi model (RMSE, MAPE)

## 📈 Tahapan Analisis dan Prediksi

Notebook `BigDataChallenge.ipynb` mencakup tahapan-tahapan berikut:

### 1. Pemuatan dan Pratinjau Data Awal (Easy)
Data dimuat dari `data.csv` atau `data.xls` ke dalam Pandas DataFrame. Dilakukan pratinjau 5 baris pertama data (`df.head()`) dan pemeriksaan informasi umum (`df.info()`).

**Poin Penting**: Kolom tanggal (Order Date dan Ship Date) awalnya bertipe object dan telah dikonversi ke tipe datetime agar sesuai untuk analisis deret waktu. Tidak ditemukan adanya missing values (data kosong).

### 2. Statistik Deskriptif Dasar (Easy)
Statistik deskriptif dasar (`df.describe()`) dihasilkan untuk memahami distribusi data numerik.

**Poin Penting**: Ditemukan adanya nilai negatif pada kolom Profit, yang mengindikasikan kerugian pada beberapa transaksi. Hal ini menunjukkan karakteristik data bisnis yang penting.

### 3. Analisis Missing Values (Medium)
Dilakukan pemeriksaan missing values di seluruh dataset. **Hasil**: Tidak ditemukan adanya missing values. Visualisasi menggunakan heatmap juga mengkonfirmasi data yang bersih.

### 4. Dekomposisi Deret Waktu (Medium)
Data penjualan (Sales) diagregasikan ke frekuensi bulanan dan didekomposisi menjadi komponen tren, musiman, dan sisa (residual) menggunakan `statsmodels.tsa.seasonal.decompose`. Model multiplicative dipilih karena fluktuasi musiman cenderung membesar seiring tren penjualan naik.

**Wawasan**:
- **Tren**: Menunjukkan pertumbuhan penjualan yang jelas dari waktu ke waktu
- **Musiman**: Terdapat pola musiman yang kuat dan berulang setiap tahun, dengan puncak penjualan di sekitar Maret, September, November, dan Desember, serta penurunan di bulan-bulan lain
- **Sisa**: Fluktuasi yang relatif acak, menunjukkan sebagian besar pola data telah dijelaskan oleh tren dan musiman

### 5. Rekayasa Fitur Berbasis Waktu (Medium)
Kolom Order Date yang sudah bertipe datetime digunakan untuk membuat fitur-fitur baru: month, day, weekday, dan is_weekend.

**Tujuan**: Fitur-fitur ini memberikan informasi kontekstual berbasis waktu yang lebih spesifik untuk membantu model menangkap pola penjualan yang lebih detail (misalnya, penjualan di akhir pekan atau bulan tertentu).

### 6. Prediksi dengan SARIMAX (Hard)
Model SARIMAX (Seasonal AutoRegressive Integrated Moving Average with eXogenous regressors) dibangun untuk memprediksi penjualan bulanan.

- **Pembagian Data**: Data monthly_sales dibagi menjadi 36 bulan data latih (Januari 2014 - Desember 2016) dan 12 bulan data uji (Januari 2017 - Desember 2017)
- **Parameter Model**: Digunakan order=(1,1,1) untuk komponen non-musiman dan seasonal_order=(1,1,1,12) untuk komponen musiman
- **Hasil Prediksi**: Model berhasil menangkap tren dan sebagian pola musiman, namun ada deviasi signifikan pada puncak tertentu

**Evaluasi Akurasi**:
- RMSE: 16260.70 (Rata-rata meleset sekitar Rp 16.260 per bulan)
- MAPE: 26.84% (Rata-rata meleset sekitar 26.84%)

**Peringatan**: Muncul UserWarning tentang data latih yang mungkin terlalu pendek (36 bulan) untuk estimasi parameter musiman yang optimal.

### 7. Prediksi dengan Prophet (Hard)
Model Facebook Prophet dibangun untuk memprediksi penjualan harian.

- **Persiapan Data**: Data transaksi asli diagregasikan ke frekuensi harian (ds untuk tanggal, y untuk penjualan)
- **Pembagian Data**: Data harian dibagi menjadi 1094 hari data latih (Januari 2014 - Desember 2016) dan 364 hari data uji (seluruh tahun 2017)
- **Parameter Model**: Digunakan seasonality_mode='multiplicative', weekly_seasonality=True, dan yearly_seasonality=True
- **Hasil Prediksi**: Model berhasil menangkap tren pertumbuhan, pola musiman mingguan, dan pola musiman tahunan. Prediksi juga dilakukan untuk 30 hari ke depan setelah data historis terakhir

**Evaluasi Akurasi**:
- RMSE: 1704.62 (Rata-rata meleset sekitar Rp 1.704 per hari)
- MAPE: nan% (Karena adanya hari-hari dengan penjualan nol, RMSE lebih relevan)

### 8. Perbandingan Model SARIMAX dan Prophet

#### Metrik Akurasi

| Model | Data | RMSE | MAPE |
|-------|------|------|------|
| SARIMAX | Data Bulanan | 16260.70 | 26.84% |
| Prophet | Data Harian | 1704.62 | nan% |

#### Analisis Perbandingan:

- **Skala Data**: Penting untuk diingat bahwa SARIMAX dievaluasi pada data bulanan, sementara Prophet pada data harian. Oleh karena itu, perbandingan RMSE secara langsung tidak sepenuhnya adil karena skala nilai penjualan harian jauh lebih kecil.

- **Akurasi RMSE**: Prophet menunjukkan RMSE yang jauh lebih rendah, mengindikasikan akurasi yang lebih baik dalam menangkap fluktuasi dan pola pada data harian yang lebih granular.

- **Penanganan Data Nol**: Prophet lebih tangguh dalam menangani data harian dengan banyak nilai nol dibandingkan MAPE SARIMAX.

- **Kemudahan Implementasi & Interpretasi**: Prophet umumnya lebih mudah diimplementasikan dan diinterpretasikan komponen-komponennya, serta lebih robust terhadap missing values dan outlier pada data harian.

### Kesimpulan Akhir

Meskipun SARIMAX berhasil menangkap tren dan musiman pada data bulanan, model Prophet menunjukkan kinerja yang lebih baik dan lebih praktis untuk prediksi penjualan harian pada dataset ini. Kemampuannya untuk menangani data harian yang tidak teratur, mengidentifikasi pola musiman mingguan, dan memberikan visualisasi komponen yang jelas menjadikannya pilihan yang lebih unggul untuk kebutuhan prediksi ini.

## 🚀 Cara Menjalankan Proyek

1. **Clone repositori ini:**
   ```bash
   git clone https://github.com/KMoex-HZ/TimeSeriesForecasting-BDC
   ```

2. **Masuk ke direktori proyek:**
   ```bash
   cd TimeSeriesForecasting-BDC
   ```

3. **Buat dan aktifkan virtual environment (disarankan):**
   ```bash
   python -m venv venv
   
   # Di Windows:
   .\venv\Scripts\activate
   
   # Di macOS/Linux:
   source venv/bin/activate
   ```

4. **Instal semua library yang dibutuhkan:**
   ```bash
   pip install pandas matplotlib seaborn statsmodels prophet numpy scikit-learn
   ```
   *(Catatan: scikit-learn adalah nama library untuk sklearn.metrics)*

5. **Buka notebook Jupyter di VS Code:**
   ```bash
   code notebooks/BigDataChallenge.ipynb
   ```

6. **Jalankan setiap sel di notebook secara berurutan** untuk melihat seluruh proses analisis dan prediksi.
