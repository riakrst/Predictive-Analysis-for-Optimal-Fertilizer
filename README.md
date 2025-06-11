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
2. Menghasilkan hingga tiga rekomendasi pupuk untuk setiap baris data dalam `uji.csv`, disusun berdasarkan probabilitas atau keyakinan prediksi.

### Solution Statements

- Melatih dan mengevaluasi beberapa algoritma klasifikasi, termasuk XGBoost, Random Forest, dan LightGBM, untuk memprediksi jenis pupuk berdasarkan fitur-fitur seperti suhu, kelembapan, kadar nitrogen, dan jenis tanaman.
- Menggunakan metrik Mean Average Precision at 3 (MAP@3) sebagai dasar evaluasi model, dengan tujuan mengukur seberapa baik model memberikan tiga rekomendasi pupuk teratas yang paling relevan.
- Memilih model dengan performa terbaik berdasarkan skor MAP@3 tertinggi pada data validasi, dan menggunakan model tersebut untuk menghasilkan prediksi akhir pada data uji.

---

## Data Understanding

Dataset yang digunakan berasal dari kompetisi **Kaggle Playground Series - Season 5, Episode 6**, yang dapat diakses melalui tautan berikut: [https://www.kaggle.com/competitions/playground-series-s5e6](https://www.kaggle.com/competitions/playground-series-s5e6)

### File Dataset
- `train.csv`: Dataset pelatihan dengan 750.000 entri dan 10 kolom fitur dan label target `Fertilizer Name`.
- `uji.csv`: Dataset pengujian dengan 250.000 baris dan 9 kolom yang tidak menyertakan label `Fertilizer Name`.
- `sample_submission.csv` – Contoh format file pengiriman prediksi (maksimal 3 label, dipisahkan spasi).

### Variabel-variabel 
![Data info](https://github.com/user-attachments/assets/f7aa5916-0146-4bd5-b04c-ae9755ee0073)

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
![Boxplot outlier](https://github.com/user-attachments/assets/5f76da90-a1e4-4ed8-b7bf-0423f8094f93)

Tidak ditemukan outlier pada keenam fitur numerik yang dianalisis.

### Visualisasi fitur kategorikal
Untuk memahami hubungan antara fitur kategorikal dengan target klasifikasi (Fertilizer Name), dilakukan visualisasi distribusi jumlah data berdasarkan fitur Soil Type, Crop Type, dan Fertilizer Name.

![Visualisasi Soil Type](https://github.com/user-attachments/assets/e543f82e-c1c4-4b46-814d-3d4229c3673b)
Gambar di atas menunjukkan jumlah masing-masing Soil Type (Clayey, Sandy, Red, Loamy, dan Black) untuk setiap jenis pupuk. Setiap batang mewakili jumlah kemunculan kombinasi jenis tanah dan pupuk tertentu dalam dataset.
Dari visualisasi ini, dapat disimpulkan bahwa:
- Distribusi jumlah data relatif seimbang di antara kombinasi Soil Type dan Fertilizer Name.
- Tidak terdapat dominasi yang ekstrem dari suatu jenis pupuk terhadap jenis tanah tertentu, yang mengindikasikan tidak adanya bias distribusi yang kuat dalam dataset.
- Hal ini menunjukkan bahwa model klasifikasi memiliki variasi data yang cukup untuk belajar dari setiap kombinasi Soil Type dan Fertilizer Name.

![Visualisasi Crop Type](https://github.com/user-attachments/assets/06c1f422-3d02-4883-81fc-440bad74fe1b)
Grafik batang di atas menunjukkan distribusi jumlah penggunaan masing-masing jenis pupuk (Fertilizer Name) terhadap berbagai jenis tanaman (Crop Type). Visualisasi ini memberikan gambaran bagaimana frekuensi pemakaian setiap jenis pupuk berbeda-beda tergantung pada jenis tanaman yang dibudidayakan.
Dari grafik tersebut, dapat diamati beberapa pola:
- Pupuk jenis 28-28 dan 14-35-14 cenderung banyak digunakan secara merata pada hampir semua jenis tanaman.
- Tanaman Paddy (padi) menunjukkan frekuensi penggunaan pupuk yang lebih tinggi dibandingkan tanaman lainnya, terutama untuk jenis 28-28, 17-17-17, dan 14-35-14. Hal ini bisa mencerminkan kebutuhan nutrisi yang lebih tinggi atau preferensi agronomis untuk tanaman padi.
- Tanaman seperti Millets, Barley, dan Tobacco memiliki variasi penggunaan pupuk yang lebih rendah secara keseluruhan.
- Pupuk Urea menunjukkan tingkat penggunaan yang lebih rendah untuk sebagian besar tanaman, khususnya pada Sugarcane dan Pulses.

![Visualisasi Fertilizer Name](https://github.com/user-attachments/assets/d694e883-b621-4b49-8a5b-2cafb741d5be)
Visualisasi di atas menunjukkan jumlah data untuk masing-masing kategori pada fitur target Fertilizer Name. Grafik ini penting dalam tahap Univariate Exploratory Data Analysis (EDA) untuk mengevaluasi distribusi kelas target sebelum melakukan pemodelan klasifikasi.
Temuan dari visualisasi:
- Seluruh jenis pupuk memiliki distribusi yang relatif seimbang, dengan perbedaan jumlah yang tidak terlalu signifikan.
- Pupuk 14-35-14 dan 10-26-26 menjadi dua kategori dengan jumlah data terbanyak, masing-masing sedikit melebihi 114.000 entri.
- Pupuk DAP dan Urea memiliki jumlah data terendah.
- Distribusi yang cukup merata ini menunjukkan bahwa tidak ada ketimpangan kelas (class imbalance) yang parah, sehingga algoritma klasifikasi tidak perlu terlalu difokuskan pada penanganan ketidakseimbangan data.
Visualisasi ini membantu dalam menjamin bahwa model machine learning nantinya tidak bias terhadap salah satu jenis pupuk karena ketidakseimbangan data. Distribusi yang cukup seimbang seperti ini ideal untuk klasifikasi multikelas.

### Hubungan Antar Fitur Numerik
![Pairplot Fitur Numerik](https://github.com/user-attachments/assets/a5c8fe37-0ade-4681-bf79-c519f2827c2c)
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
Langkah encoding ini juga diterapkan pada data **uji**, dan dilakukan penyesuaian kolom (alignment) antara data training dan ujiing menggunakan fungsi `align`, agar struktur kolomnya sama. Jika ada kolom yang tidak tersedia di uji set, maka akan diisi dengan nilai 0.

### 2. Standardisasi Fitur Numerik
Seluruh fitur numerik (setelah encoding) kemudian distandarisasi menggunakan **StandardScaler**, yaitu transformasi yang mengubah setiap fitur agar memiliki rata-rata 0 dan standar deviasi 1.
Standardisasi ini penting agar semua fitur berada dalam skala yang sama, sehingga algoritma machine learning seperti SVM, KNN, atau neural network tidak bias terhadap fitur dengan skala yang lebih besar.

### 3. Split Data untuk Validasi
Karena label dari data uji tidak tersedia, maka data training dibagi kembali menjadi training dan validation set menggunakan `train_uji_split` dengan rasio 80:20.
Tujuan dari langkah ini adalah untuk melakukan evaluasi lokal menggunakan metrik **MAP@3** pada validation set, sebelum membuat prediksi akhir dengan data uji.

---
## Modeling

Pada tahap ini, dilakukan pembangunan dan evaluasi beberapa model machine learning untuk menyelesaikan tugas klasifikasi multikelas, yaitu memprediksi jenis pupuk (**`Fertilizer Name`**) berdasarkan fitur-fitur lingkungan seperti cuaca, jenis tanah, dan tanaman.

### Tujuan Modeling

- Mengembangkan model klasifikasi untuk memprediksi jenis pupuk berdasarkan fitur input.
- Mencoba dan membandingkan performa tiga algoritma: **Random Forest**, **LightGBM**, dan **XGBoost**.
- Menentukan model terbaik berdasarkan metrik **Mean Average Precision at 3 (MAP@3)**.
- Melatih ulang model terbaik untuk prediksi akhir terhadap data uji.

### Penjelasan Singkat Algoritma

#### 1. **Random Forest**
Random Forest adalah algoritma ensemble berbasis pohon keputusan (decision tree) yang membangun banyak pohon secara acak (random) dan menggabungkan hasilnya untuk meningkatkan akurasi dan mengurangi overfitting. Model ini bekerja dengan membuat banyak decision tree dari subset data dan fitur yang berbeda, lalu menggabungkan prediksi mereka (dalam klasifikasi: mayoritas voting).

**Kelebihan:**  
- Tidak mudah overfitting.  
- Baik untuk data tabular.  
- Bisa mengukur pentingnya fitur.  

**Kekurangan:**  
- Relatif lambat saat jumlah pohon banyak.  
- Interpretasi sulit dibanding model linear.  

**Parameter yang digunakan:**  
- `n_estimators=100`  
  Menentukan jumlah pohon keputusan dalam hutan. Semakin banyak pohon, semakin stabil hasilnya, namun juga memperlama waktu pelatihan. Nilai 100 dipilih sebagai titik awal yang umum dan seimbang antara performa dan waktu.
- `random_state=42`  
  Digunakan untuk mengatur seed sehingga hasil model dapat direproduksi (reproducible). Angka 42 dipilih sebagai standar umum untuk konsistensi hasil.


#### 2. **LightGBM (Light Gradient Boosting Machine)**
LightGBM adalah algoritma boosting berbasis pohon yang mengembangkan model secara iteratif untuk mengoreksi kesalahan dari model sebelumnya. Dibandingkan dengan XGBoost, LightGBM dirancang untuk efisiensi tinggi dalam pelatihan dan prediksi.

**Kelebihan:**  
- Pelatihan sangat cepat.  
- Efisien untuk dataset besar.  
- Mendukung pengurutan kategori secara otomatis.  

**Kekurangan:**  
- Bisa overfitting pada dataset kecil.  
- Kurang robust untuk data yang sangat tidak seimbang.  

**Parameter yang digunakan:**  
- `n_estimators=100`  
  Menentukan jumlah boosting iteration. Sama seperti pada Random Forest, nilai ini digunakan sebagai titik awal yang umum untuk eksperimen awal dan menghasilkan hasil cukup baik tanpa overfitting.
- `random_state=42`  
  Menetapkan seed acak agar hasil model tetap konsisten setiap kali dijalankan. Ini penting dalam proyek kolaboratif atau saat menggunakan metode validasi silang.


#### 3. **XGBoost (Extreme Gradient Boosting)**
XGBoost adalah algoritma boosting yang sangat populer dan terbukti unggul dalam berbagai kompetisi data science. Ia bekerja dengan membangun model secara bertahap, fokus memperbaiki kesalahan dari prediksi sebelumnya menggunakan teknik *gradient boosting*.

**Kelebihan:**  
- Kuat terhadap overfitting dengan regularisasi.  
- Dapat menangani data tabular dan kompleks.  
- Dukungan dokumentasi dan komunitas luas.  

**Kekurangan:**  
- Waktu pelatihan lebih lama dibanding LightGBM.  
- Lebih banyak parameter yang perlu diatur.

**Parameter yang digunakan:**  
- `use_label_encoder=False`  
  Menonaktifkan label encoder bawaan dari XGBoost yang digunakan pada versi sebelumnya. Hal ini dilakukan agar model tidak mengalami error saat menerima label bertipe numerik dari `LabelEncoder`.
- `eval_metric='mlogloss'`  
  Menentukan metrik evaluasi saat pelatihan. Multiclass log loss (`mlogloss`) mengukur seberapa baik probabilitas prediksi model mendekati distribusi sebenarnya. Cocok digunakan untuk klasifikasi multikelas.
- `random_state=42`  
  Sama seperti sebelumnya, digunakan untuk menjamin reproducibility hasil pelatihan.

### Hasil Evaluasi Perbandingan
Untuk menilai performa model klasifikasi multikelas dengan lebih akurat, digunakan metrik **Mean Average Precision at 3 (MAP@3)**, yang mempertimbangkan peringkat dari tiga label teratas dalam prediksi. Model dilatih menggunakan data latih dan divalidasi dengan data validasi.
Setiap model dievaluasi menggunakan metrik MAP@3, dan hasilnya dibandingkan:

| Model         | MAP@3 Score |
|---------------|-------------|
| **XGBoost**       | **0.3307**   |
| Random Forest | 0.2911      |
| LightGBM      | 0.3230      |

> Hasil menunjukkan bahwa **XGBoost** memberikan performa terbaik dalam memprediksi jenis pupuk dengan skor MAP@3 tertinggi.

### Model Terbaik dan Pelatihan Ulang

Berdasarkan evaluasi, **XGBoost** dipilih sebagai model final. Model ini kemudian dilatih ulang menggunakan seluruh data latih (`X_scaled`, `y_encoded`) untuk menghasilkan prediksi pada data uji secara maksimal. Prediksi dibuat dalam bentuk probabilitas, lalu diambil **3 label teratas** (top-3) untuk setiap data uji sebagai hasil akhir prediksi.

---
## Evaluation

### Metrik Evaluasi: MAP@3 (Mean Average Precision at 3)

Metrik utama yang digunakan dalam proyek ini adalah **Mean Average Precision at 3 (MAP@3)**. Metrik ini dipilih karena sesuai untuk tugas **multi-class classification dengan top-N prediction**, di mana sistem harus memberikan hingga tiga rekomendasi pupuk terbaik berdasarkan input lingkungan dan tanaman.

MAP@3 menilai seberapa sering label yang benar muncul dalam 3 prediksi teratas model, sekaligus mempertimbangkan **urutan prediksi**. Artinya, jika label yang benar muncul pada posisi pertama, skornya lebih tinggi dibanding jika berada di posisi kedua atau ketiga.

### Cara Kerja MAP@3

Langkah perhitungan MAP@3 secara umum:

1. Untuk setiap sampel, cek apakah label sebenarnya muncul dalam tiga prediksi teratas model.
2. Jika muncul:
   - Posisi 1 → skor = 1.0
   - Posisi 2 → skor = 0.5
   - Posisi 3 → skor = 0.333
3. Jika tidak muncul dalam top-3 → skor = 0.0
4. Skor dari seluruh data dirata-rata → menghasilkan MAP@3.

#### Rumus MAP@K:
Formula evaluasi Mean Average Precision at K (MAP@K) (sumber: Kaggle, 2025)

![rumus MAP](https://github.com/user-attachments/assets/3f7dd9b0-2539-433f-93c4-323404f428d4)

Di dalam rumus MAP@k, terdapat beberapa komponen penting:
- U: jumlah total observasi/data (misalnya jumlah data validasi)
- P(k): nilai precision (presisi) pada posisi ke-k
- n: jumlah prediksi per observasi (misalnya 3 jika kita menggunakan MAP@3)
- rel(k): fungsi indikator yang bernilai 1 jika label pada posisi ke-k benar (relevan), dan 0 jika tidak

Nilai MAP dihitung dengan menjumlahkan nilai presisi dari posisi di mana label benar ditemukan, kemudian dirata-rata untuk seluruh data.
Dalam konteks ini menggunakan MAP@3, sehingga nilai k hanya sampai 3, bukan 5 seperti pada gambar di atas, jadi bisa menyesuaikan rumus menjadi MAP@3 sesuai kebutuhan.


### Hasil Evaluasi Model

Model dievaluasi menggunakan data validasi, dengan hasil skor MAP@3 sebagai berikut:

| Model         | MAP@3 Score |
|---------------|-------------|
| **XGBoost**       | **0.3307**   |
| Random Forest | 0.2911      |
| LightGBM      | 0.3230      |

Model **XGBoost** memberikan nilai **MAP@3 tertinggi**, yaitu **0.3307**, sehingga dipilih sebagai model final.

### Kesimpulan

Model XGBoost terbukti paling optimal dalam menyelesaikan **dua problem utama proyek ini**, yaitu:

1. **Memprediksi jenis pupuk terbaik** berdasarkan kombinasi fitur lingkungan dan tanaman dengan akurasi ranking yang tinggi.
2. **Memberikan hingga tiga rekomendasi pupuk** yang relevan untuk setiap kondisi, dengan mempertimbangkan probabilitas prediksi.

Dengan nilai mAP@3 sebesar 0.3307, model ini menunjukkan bahwa dalam rata-rata, label pupuk yang benar berhasil diprediksi dalam tiga posisi teratas oleh sistem. Meskipun nilai ini belum tergolong tinggi secara absolut, hasil tersebut sudah mencerminkan performa yang cukup baik untuk digunakan sebagai sistem prediksi dalam pemilihan pupuk berbasis data.

---
##  Hasil Prediksi (Submission.csv)
Setelah model XGBoost dilatih ulang dengan seluruh data latih dan menghasilkan prediksi terhadap data uji, langkah selanjutnya adalah menyiapkan file submission.csv berisi hasil prediksi.

### Format Output Submission
Hasil prediksi berupa 3 label teratas untuk setiap entri diproses menjadi format string yang dipisahkan dengan spasi (space-delimited). Format ini mengikuti aturan kompetisi kaggle, di mana setiap baris dalam file submission mencantumkan:
- id → ID dari data uji
- Fertilizer Name → Tiga prediksi label teratas dalam format string, dipisahkan spasi

Setelah file berhasil disimpan, berikut adalah cuplikan isi file submission.csv:

![submission](https://github.com/user-attachments/assets/1fcb4d43-7918-47f8-bd29-f5d8364b0c9d)


---
## Referensi
1. Breiman, L. *Random Forests*. Machine Learning 45, 5–32 (2001). [https://doi.org/10.1023/A:1010933404324](https://doi.org/10.1023/A:1010933404324)
2. Chen, T., & Guestrin, C. (2016). *XGBoost: A scalable tree boosting system*. In Proceedings of the ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (Vol. 13-17-August-2016, pp. 785–794). Association for Computing Machinery. [https://doi.org/10.1145/2939672.2939785](https://doi.org/10.1145/2939672.2939785)
3.Evidently AI. (2025, January 9). Mean Average Precision (MAP) in ranking and recommendations. Retrieved from [https://www.evidentlyai.com/ranking-metrics/mean-average-precision-MAP](https://www.evidentlyai.com/ranking-metrics/mean-average-precision-MAP)
4. Kaggle. (2025). Evaluation metric: Mean Average Precision at K (MAP@K)[Screenshot]. Kaggle. [https://www.kaggle.com/competitions/playground-series-s5e6/overview](https://www.kaggle.com/competitions/playground-series-s5e6/overview)
5. Ke, G., Meng, Q., Finley, T., Wang, T., Chen, W., Ma, W., ... & Liu, T. Y. (2017). LightGBM: A highly efficient gradient boosting decision tree. Advances in Neural Information Processing Systems, 30. [https://papers.nips.cc/paper_files/paper/2017/hash/6449f44a102fde848669bdd9eb6b76fa-Abstract.html](https://papers.nips.cc/paper_files/paper/2017/hash/6449f44a102fde848669bdd9eb6b76fa-Abstract.html)
6. Nuruzzaman, M., Bahar, M. M., & Naidu, R. (2025, January 25). Diffuse soil pollution from agriculture: Impacts and remediation. Science of the Total Environment. Elsevier B.V. [https://doi.org/10.1016/j.scitotenv.2025.178398](https://doi.org/10.1016/j.scitotenv.2025.178398)
