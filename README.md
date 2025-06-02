# Laporan Proyek Machine Learning – Ria Kristi

## Domain Proyek

Pemilihan pupuk yang tepat sangat penting dalam praktik pertanian modern. Faktor seperti jenis tanaman, kondisi cuaca, dan karakteristik tanah memainkan peran besar dalam menentukan efektivitas suatu jenis pupuk. Penggunaan pupuk yang tidak tepat dapat menyebabkan penurunan hasil panen, pencemaran lingkungan, dan pemborosan biaya.

Dengan berkembangnya teknologi, pendekatan berbasis data dapat digunakan untuk memprediksi jenis pupuk yang optimal sesuai dengan kondisi agrikultur tertentu. Model machine learning dapat membantu para petani dan ahli agronomi untuk membuat keputusan berbasis data yang lebih akurat dan cepat.

Menurut [FAO](https://www.fao.org), efisiensi pemupukan merupakan salah satu strategi utama untuk mendukung ketahanan pangan global. Oleh karena itu, proyek ini memiliki relevansi langsung dalam meningkatkan produktivitas dan efisiensi sektor pertanian.

---

## Business Understanding

### Problem Statements

1. Bagaimana memprediksi jenis pupuk terbaik berdasarkan karakteristik lingkungan seperti suhu, kelembapan, kadar nitrogen, dan jenis tanaman?
2. Bagaimana memberikan hingga tiga rekomendasi pupuk terbaik yang sesuai untuk setiap kombinasi kondisi agrikultur?

### Goals

1. Membangun model klasifikasi yang dapat memprediksi label `Fertilizer Name` berdasarkan kombinasi fitur lingkungan dan tanaman.
2. Menghasilkan hingga tiga rekomendasi pupuk untuk setiap baris data dalam `test.csv`, disusun berdasarkan probabilitas atau keyakinan prediksi.

### Solution Statements

- Membangun baseline model klasifikasi menggunakan algoritma LightGBM dan Random Forest.
- Melakukan tuning hyperparameter dan feature engineering untuk meningkatkan kinerja model.
- Mengevaluasi model berdasarkan metrik **Mean Average Precision at 3 (MAP@3)**, sesuai dengan ketentuan kompetisi Kaggle.

---

## Data Understanding

Dataset yang digunakan berasal dari kompetisi **Kaggle Playground Series - Season 5, Episode 6**, yang dapat diakses melalui tautan berikut: [https://www.kaggle.com/competitions/playground-series-s5e6](https://www.kaggle.com/competitions/playground-series-s5e6)

### File Dataset
- `train.csv`: Dataset pelatihan dengan 750.000 entri dan 10 kolom fitur dan label target (`Fertilizer Name`).
- `test.csv`: Dataset pengujian dengan 250000 baris dan 9 kolom yang tidak menyertakan label `Fertilizer Name`.
- `sample_submission.csv` – Contoh format file pengiriman prediksi (maksimal 3 label, dipisahkan spasi).

### Struktur Kolom pada `train.csv`

| Kolom            | Tipe Data | Deskripsi                                                                 |
|------------------|-----------|---------------------------------------------------------------------------|
| id               | int64     | ID unik untuk setiap entri data                                           |
| Temparature      | int64     | Suhu lingkungan tempat tanaman tumbuh                                    |
| Humidity         | int64     | Tingkat kelembapan udara                                                  |
| Moisture         | int64     | Kadar kelembapan dalam tanah                                              |
| Soil Type        | object    | Jenis tanah tempat tanaman ditanam                                        |
| Crop Type        | object    | Jenis tanaman yang dibudidayakan                                          |
| Nitrogen         | int64     | Kandungan nitrogen dalam tanah                                            |
| Potassium        | int64     | Kandungan kalium dalam tanah                                              |
| Phosphorous      | int64     | Kandungan fosfor dalam tanah                                              |
| Fertilizer Name  | object    | Label target; jenis pupuk yang digunakan (target klasifikasi)             |

### Informasi Tambahan

<!-- - **Ukuran Data:** 750.000 baris dan 10 kolom
- **Tidak ada missing values** di seluruh kolom
- **Tipe prediksi:** Multi-class classification (beberapa label, top-3 prediction)
- Target kolom `Fertilizer Name` terdiri dari berbagai nama pupuk seperti `Urea`, `DAP`, `14-35-14`, dll.

### Analisis Awal yang Direncanakan

- Visualisasi distribusi nilai setiap fitur numerik (histogram, boxplot)
- Analisis frekuensi dari label `Fertilizer Name`
- Analisis distribusi jenis tanaman dan jenis tanah
- Uji korelasi antar fitur numerik
- Encoding untuk fitur kategorikal (`Soil Type`, `Crop Type`, dan `Fertilizer Name`)
- Pemetaan apakah beberapa jenis tanaman selalu dikaitkan dengan pupuk tertentu
-->
---
