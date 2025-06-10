# Laporan Proyek Machine Learning – Ria Kristi

## Domain Proyek

Pemilihan pupuk yang tepat sangat penting dalam praktik pertanian modern. Faktor seperti jenis tanaman, kondisi cuaca, dan karakteristik tanah memainkan peran besar dalam menentukan efektivitas suatu jenis pupuk. Penggunaan pupuk yang tidak sesuai tidak hanya dapat menurunkan hasil panen, tetapi juga menyebabkan pencemaran lingkungan dan pemborosan biaya.

Penggunaan pupuk yang berlebihan telah diketahui menjadi penyebab utama polusi tanah yang menyebar secara luas. Dampaknya meliputi kontaminasi lingkungan yang berkontribusi pada risiko serius bagi ekosistem dan kesehatan manusia (Nuruzzaman, Bahar, & Naidu, 2025). Oleh karena itu, pemilihan pupuk yang optimal perlu didukung dengan pendekatan yang lebih terukur dan efisien.

Dengan berkembangnya teknologi, pendekatan berbasis data dapat digunakan untuk memprediksi jenis pupuk yang paling sesuai dengan kondisi agrikultur tertentu. Model machine learning dapat membantu petani dan ahli agronomi membuat keputusan yang lebih akurat dan berbasis data, serta meningkatkan produktivitas dan efisiensi dalam praktik pertanian.

---

## Business Understanding

### Problem Statements

1. Bagaimana memprediksi jenis pupuk terbaik berdasarkan karakteristik lingkungan dan tanaman?
2. Bagaimana memberikan hingga tiga rekomendasi pupuk terbaik yang sesuai untuk setiap kombinasi kondisi agrikultur?

### Goals

1. Membangun model klasifikasi yang dapat memprediksi label `Fertilizer Name` berdasarkan kombinasi fitur lingkungan dan tanaman.
2. Menghasilkan hingga tiga rekomendasi pupuk untuk setiap baris data dalam `test.csv`, disusun berdasarkan probabilitas atau keyakinan prediksi.

### Solution Statements

- Melatih dan mengevaluasi beberapa algoritma klasifikasi, termasuk XGBoost, Random Forest, dan LightGBM, untuk memprediksi jenis pupuk berdasarkan fitur-fitur seperti suhu, kelembapan, kadar nitrogen, dan jenis tanaman.
- Menggunakan metrik Mean Average Precision at 3 (mAP@3) sebagai dasar evaluasi model, dengan tujuan mengukur seberapa baik model memberikan tiga rekomendasi pupuk teratas yang paling relevan.
- Memilih model dengan performa terbaik berdasarkan skor mAP@3 tertinggi pada data validasi, dan menggunakan model tersebut untuk menghasilkan prediksi akhir pada data test.

---

## Data Understanding

Dataset yang digunakan berasal dari kompetisi **Kaggle Playground Series - Season 5, Episode 6**, yang dapat diakses melalui tautan berikut: [https://www.kaggle.com/competitions/playground-series-s5e6](https://www.kaggle.com/competitions/playground-series-s5e6)

### File Dataset
- `train.csv`: Dataset pelatihan dengan 750.000 entri dan 10 kolom fitur dan label target `Fertilizer Name`.
- `test.csv`: Dataset pengujian dengan 250.000 baris dan 9 kolom yang tidak menyertakan label `Fertilizer Name`.
- `sample_submission.csv` – Contoh format file pengiriman prediksi (maksimal 3 label, dipisahkan spasi).

### Variabel-variabel 
![image](https://github.com/user-attachments/assets/d4aac1e5-7d5a-49b0-a5f6-6ad2346db5db)

- Terdapat **3 kolom dengan tipe data `object`**, yaitu:
  - `Soil Type`, `Crop Type`, dan `Fertilizer Name`.  
    Kolom-kolom ini merupakan **fitur kategorikal** (non-numerik).
- Terdapat **7 kolom dengan tipe data numerik `int64`**, yaitu:
  - `id`, `Temparature`, `Humidity`, `Moisture`, `Nitrogen`, `Potassium`, dan `Phosphorous`.  
    Kolom-kolom ini merupakan **fitur numerik** yang mengandung nilai kuantitatif dari karakteristik tanah, cuaca, dan nutrisi.
- Kolom `Fertilizer Name` adalah **target variabel** yang ingin diprediksi dalam proyek ini.
- Tidak ada kolom missing value dalam dataset
  
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

### Memeriksa Outliers
![image](https://github.com/user-attachments/assets/2bd2cdcd-c88d-424c-8821-5d2efa41177a)
Tidak ditemukan outlier pada keenam fitur numerik yang dianalisis.

### Visualisasi fitur kategorikal
Untuk memahami hubungan antara fitur kategorikal dengan target klasifikasi (Fertilizer Name), dilakukan visualisasi distribusi jumlah data berdasarkan fitur Soil Type, Crop Type, dan Fertilizer Name.

![image](https://github.com/user-attachments/assets/cf47e12f-bb92-4702-8601-62320e153a7c)
Gambar di atas menunjukkan jumlah masing-masing Soil Type (Clayey, Sandy, Red, Loamy, dan Black) untuk setiap jenis pupuk. Setiap batang mewakili jumlah kemunculan kombinasi jenis tanah dan pupuk tertentu dalam dataset.
Dari visualisasi ini, dapat disimpulkan bahwa:
- Distribusi jumlah data relatif seimbang di antara kombinasi Soil Type dan Fertilizer Name.
- Tidak terdapat dominasi yang ekstrem dari suatu jenis pupuk terhadap jenis tanah tertentu, yang mengindikasikan tidak adanya bias distribusi yang kuat dalam dataset.
- Hal ini menunjukkan bahwa model klasifikasi memiliki variasi data yang cukup untuk belajar dari setiap kombinasi Soil Type dan Fertilizer Name.

![image](https://github.com/user-attachments/assets/8e45790b-a5d1-40aa-9d2d-6112162e7091)
Grafik batang di atas menunjukkan distribusi jumlah penggunaan masing-masing jenis pupuk (Fertilizer Name) terhadap berbagai jenis tanaman (Crop Type). Visualisasi ini memberikan gambaran bagaimana frekuensi pemakaian setiap jenis pupuk berbeda-beda tergantung pada jenis tanaman yang dibudidayakan.
Dari grafik tersebut, dapat diamati beberapa pola:
- Pupuk jenis 28-28 dan 14-35-14 cenderung banyak digunakan secara merata pada hampir semua jenis tanaman.
- Tanaman Paddy (padi) menunjukkan frekuensi penggunaan pupuk yang lebih tinggi dibandingkan tanaman lainnya, terutama untuk jenis 28-28, 17-17-17, dan 14-35-14. Hal ini bisa mencerminkan kebutuhan nutrisi yang lebih tinggi atau preferensi agronomis untuk tanaman padi.
- Tanaman seperti Millets, Barley, dan Tobacco memiliki variasi penggunaan pupuk yang lebih rendah secara keseluruhan.
- Pupuk Urea menunjukkan tingkat penggunaan yang lebih rendah untuk sebagian besar tanaman, khususnya pada Sugarcane dan Pulses.

![image](https://github.com/user-attachments/assets/6f103c81-549a-42dc-bf90-163334c8dbea)
Visualisasi di atas menunjukkan jumlah data untuk masing-masing kategori pada fitur target Fertilizer Name. Grafik ini penting dalam tahap Univariate Exploratory Data Analysis (EDA) untuk mengevaluasi distribusi kelas target sebelum melakukan pemodelan klasifikasi.
Temuan dari visualisasi:
- Seluruh jenis pupuk memiliki distribusi yang relatif seimbang, dengan perbedaan jumlah yang tidak terlalu signifikan.
- Pupuk 14-35-14 dan 10-26-26 menjadi dua kategori dengan jumlah data terbanyak, masing-masing sedikit melebihi 114.000 entri.
- Pupuk DAP dan Urea memiliki jumlah data terendah.
- Distribusi yang cukup merata ini menunjukkan bahwa tidak ada ketimpangan kelas (class imbalance) yang parah, sehingga algoritma klasifikasi tidak perlu terlalu difokuskan pada penanganan ketidakseimbangan data.
Visualisasi ini membantu dalam menjamin bahwa model machine learning nantinya tidak bias terhadap salah satu jenis pupuk karena ketidakseimbangan data. Distribusi yang cukup seimbang seperti ini ideal untuk klasifikasi multikelas.

### Hubungan Antar Fitur Numerik
![image](https://github.com/user-attachments/assets/efd00a19-8c2c-4645-a9fa-1c3a0e1a5bd6)
Visualisasi ini menunjukkan hubungan antar seluruh fitur numerik dalam dataset melalui pairplot. Tujuan utamanya adalah untuk mengamati apakah terdapat korelasi atau pola hubungan antara masing-masing fitur numerik.

Insight dari visualisasi:
- Tidak tampak hubungan linear yang jelas antar fitur numerik, terlihat dari pola titik yang cenderung tersebar merata atau membentuk garis horizontal/vertikal.
- Distribusi nilai dari masing-masing fitur terlihat seragam atau terdistribusi normal, terutama untuk Temperature, Humidity, dan Potassium.
- Hal ini mengindikasikan bahwa fitur-fitur numerik bersifat relatif independen satu sama lain, dan kemungkinan tidak terdapat multikolinearitas yang signifikan dalam dataset.
- Meski tidak saling berkorelasi kuat, masing-masing fitur tetap bisa berkontribusi terhadap model klasifikasi secara individual.

Visualisasi pairplot membantu memastikan bahwa tidak ada fitur numerik yang redundant karena korelasi tinggi, serta memberi gambaran awal tentang struktur data sebelum dilakukan pemodelan.

---

## Data Preparation

Pada tahap ini, dilakukan beberapa proses transformasi data agar dataset siap digunakan untuk pemodelan machine learning. Berikut adalah tahapan-tahapan data preparation yang dilakukan:

### 1. Encoding Fitur Kategorikal
Terdapat tiga fitur kategorikal dalam dataset: `Soil Type`, `Crop Type`, dan `Fertilizer Name`.
- **`Soil Type` dan `Crop Type`** merupakan fitur input, sehingga dilakukan transformasi menggunakan **One-Hot Encoding**. Teknik ini dipilih agar model tidak menganggap ada hubungan ordinal antar kategori dan untuk memastikan fitur bisa digunakan dalam model yang hanya menerima input numerik.
- **`Fertilizer Name`** merupakan target variabel (label), sehingga digunakan **Label Encoding** untuk mengubah nama pupuk menjadi nilai numerik. Ini diperlukan agar target label dapat diproses oleh algoritma klasifikasi.
Langkah encoding ini juga diterapkan pada data **test**, dan dilakukan penyesuaian kolom (alignment) antara data training dan testing menggunakan fungsi `align`, agar struktur kolomnya sama. Jika ada kolom yang tidak tersedia di test set, maka akan diisi dengan nilai 0.

### 2. Standardisasi Fitur Numerik
Seluruh fitur numerik (setelah encoding) kemudian distandarisasi menggunakan **StandardScaler**, yaitu transformasi yang mengubah setiap fitur agar memiliki rata-rata 0 dan standar deviasi 1.
Standardisasi ini penting agar semua fitur berada dalam skala yang sama, sehingga algoritma machine learning seperti SVM, KNN, atau neural network tidak bias terhadap fitur dengan skala yang lebih besar.

### 3. Split Data untuk Validasi
Karena label dari data test tidak tersedia, maka data training dibagi kembali menjadi training dan validation set menggunakan `train_test_split` dengan rasio 80:20.
Tujuan dari langkah ini adalah untuk melakukan evaluasi lokal menggunakan metrik **mAP@3** pada validation set, sebelum membuat prediksi akhir dengan data test.

---

## Modeling

Pada tahap ini, dilakukan pelatihan dan evaluasi beberapa model machine learning untuk menyelesaikan masalah klasifikasi multikelas, yaitu memprediksi jenis pupuk (`Fertilizer Name`) berdasarkan fitur-fitur cuaca, jenis tanah, dan tanaman.

### Tujuan Modeling

- Membangun model klasifikasi untuk memprediksi label pupuk.
- Membandingkan performa tiga algoritma: **XGBoost**, **Random Forest**, dan **LightGBM**.
- Memilih model terbaik berdasarkan metrik evaluasi **mAP@3**.

### Fungsi mAP@3

Untuk mengevaluasi performa model, digunakan metrik **Mean Average Precision at 3 (mAP@3)**. Berikut fungsi implementasinya:

```python
# Fungsi mAP@3
def mAPk(actual, predicted, k=3):
    def apk(a, p, k):
        # Convert the numpy array slice to a list before using the index method
        p_list = list(p[:k])
        if a in p_list:
            return 1.0 / (p_list.index(a) + 1)
        return 0.0
    return np.mean([apk(a, p, k) for a, p in zip(actual, predicted)])
```

### Fungsi Evaluasi Model
Untuk memudahkan proses evaluasi setiap model, digunakan fungsi berikut:
```
def evaluate_model(model, name):
    model.fit(X_train, y_train)
    probs = model.predict_proba(X_val)
    top_3 = np.argsort(probs, axis=1)[:, -3:][:, ::-1]
    score = mAPk(y_val, top_3, k=3)
    print(f"{name:<20}: mAP@3 = {score:.4f}")
    return score
```
### Evaluasi Semua Model
Tiga model diuji untuk dibandingkan performanya. Setelah setiap model selesai dilatih dan dievaluasi, objek model dihapus dan memori dibersihkan untuk menghindari kehabisan memori pada Google Colab.
```
# Evaluasi semua model
scores = {}

# XGBoost Classifier
xgb_model = XGBClassifier(use_label_encoder=False, eval_metric='mlogloss', random_state=42)
scores['XGBoost'] = evaluate_model(xgb_model, "XGBoost")

# Bersihkan memori
del xgb_model
gc.collect()

# RandomForest Classifier
rf_model = RandomForestClassifier(n_estimators=100, random_state=42)
scores['Random Forest'] = evaluate_model(rf_model, "Random Forest")

# Bersihkan memori
del rf_model
gc.collect()

# LightGBM Classifier
lgbm_model = LGBMClassifier(n_estimators=100, random_state=42)
scores['LightGBM'] = evaluate_model(lgbm_model, "LightGBM")

# Bersihkan memori
del lgbm_model
gc.collect()
```

### Hasil Evaluasi Perbandingan
Setelah seluruh model dievaluasi, dicetak skor mAP@3 dari masing-masing model:
| Model         | mAP\@3 Score |
| ------------- | ------------ |
| XGBoost       | **0.3307**   |
| Random Forest | 0.2911       |
| LightGBM      | 0.3230       |

Berdasarkan hasil evaluasi di atas, XGBoost menunjukkan performa terbaik dengan nilai mAP@3 sebesar 0.3307.

### Model Final dan Prediksi
Karena memiliki skor tertinggi, model XGBoost dipilih sebagai model final. Model ini kemudian dilatih ulang menggunakan seluruh data train (X_scaled, y_encoded) dan digunakan untuk memprediksi genre pada data uji.
```
final_model = XGBClassifier(use_label_encoder=False, eval_metric='mlogloss', random_state=42)
final_model.fit(X_scaled, y_encoded)
```
Setelah pelatihan ulang selesai, dilakukan prediksi terhadap data test dalam bentuk probabilitas, kemudian diambil tiga label dengan probabilitas tertinggi:
```
# Prediksi probabilitas
test_probs = final_model.predict_proba(test_scaled)
# Ambil top-3 prediksi
top_3_preds_idx = np.argsort(test_probs, axis=1)[:, -3:][:, ::-1]
# Konversi ke label asli
top_3_preds_label = le.inverse_transform(top_3_preds_idx.ravel()).reshape(top_3_preds_idx.shape)
```
---
## Evaluation

### Metrik Evaluasi: mAP@3 (Mean Average Precision at 3)

Metrik yang digunakan untuk mengevaluasi performa model dalam proyek ini adalah **Mean Average Precision at 3 (mAP@3)**. mAP@3 merupakan metrik yang cocok digunakan pada tugas klasifikasi multikelas di mana model diminta memberikan lebih dari satu prediksi (top-N prediction), dalam hal ini **3 label teratas**.

mAP@3 mengukur seberapa sering label yang benar muncul dalam 3 prediksi teratas dan memperhitungkan **urutan kemunculannya**. Makin tinggi posisi label yang benar, makin tinggi nilai yang diberikan.

### Cara Kerja mAP@3

mAP@3 dihitung dengan langkah-langkah sebagai berikut:

- Untuk setiap data, cek apakah label sebenarnya ada di antara 3 prediksi teratas model.
- Jika ya, nilai presisi dihitung sebagai kebalikan dari posisinya:
  - Posisi ke-1: skor = 1.0
  - Posisi ke-2: skor = 0.5
  - Posisi ke-3: skor = 0.333
- Jika label tidak muncul di 3 teratas, skor = 0.0
- Skor semua data dijumlahkan dan dirata-rata untuk mendapatkan nilai akhir mAP@3.

#### Rumus mAP@3:

![image](https://github.com/user-attachments/assets/5d6399da-9c64-4da3-9ff2-9f6e6ec83895)

Formula evaluasi Mean Average Precision at K (mAP@K) (sumber: Kaggle, 2025)

Di dalam rumus mAP@k, terdapat beberapa komponen penting:
- U: jumlah total observasi/data (misalnya jumlah data validasi)
- P(k): nilai precision (presisi) pada posisi ke-k
- n: jumlah prediksi per observasi (misalnya 3 jika kita menggunakan mAP@3)
- rel(k): fungsi indikator yang bernilai 1 jika label pada posisi ke-k benar (relevan), dan 0 jika tidak
Nilai mAP dihitung dengan menjumlahkan nilai presisi dari posisi di mana label benar ditemukan, kemudian dirata-rata untuk seluruh data.
Dalam konteks ini menggunakan mAP@3, sehingga nilai k hanya sampai 3, bukan 5 seperti pada gambar di atas, jadi bisa menyesuaikan rumus menjadi mAP@3 sesuai kebutuhan.

### Hasil Evaluasi Akhir

Model telah dievaluasi menggunakan data validasi dengan hasil skor sebagai berikut:

| Model           | mAP@3 Score |
|----------------|-------------|
| **XGBoost**     | **0.3307**  |
| LightGBM       | 0.3215      |
| Random Forest  | 0.3102      |

Model **XGBoost** dipilih sebagai model final karena memberikan nilai **mAP@3 tertinggi**, yaitu **0.3307**. Ini menunjukkan bahwa rata-rata, label yang benar berhasil diprediksi dalam 3 besar oleh model, dan sering kali berada di posisi yang tinggi.

Model ini kemudian digunakan untuk melatih ulang dengan seluruh data pelatihan dan menghasilkan prediksi akhir pada data uji. Hasil prediksi disesuaikan dengan format submission kompetisi.

---
##  Hasil Prediksi (Submission.csv)
Setelah model XGBoost dilatih ulang dengan seluruh data latih dan menghasilkan prediksi terhadap data uji, langkah selanjutnya adalah menyiapkan file submission.csv berisi hasil prediksi.

### Format Output Submission
Hasil prediksi berupa 3 label teratas untuk setiap entri diproses menjadi format string yang dipisahkan dengan spasi (space-delimited). Format ini mengikuti aturan kompetisi kaggle, di mana setiap baris dalam file submission mencantumkan:
- id → ID dari data uji
- Fertilizer Name → Tiga prediksi label teratas dalam format string, dipisahkan spasi

Setelah file berhasil disimpan, berikut adalah cuplikan isi file submission.csv:

![image](https://github.com/user-attachments/assets/aaf3c521-049f-48b4-88e7-c71d5bfd44e2)


---
## Referensi
Kaggle. (2025). Evaluation metric: Mean Average Precision at K (mAP@K)[Screenshot]. Kaggle. [https://www.kaggle.com/competitions/playground-series-s5e6/overview](https://www.kaggle.com/competitions/playground-series-s5e6/overview)
Nuruzzaman, M., Bahar, M. M., & Naidu, R. (2025, January 25). Diffuse soil pollution from agriculture: Impacts and remediation. Science of the Total Environment. Elsevier B.V. [https://doi.org/10.1016/j.scitotenv.2025.178398](https://doi.org/10.1016/j.scitotenv.2025.178398)
