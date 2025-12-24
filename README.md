# Dashboard Evaluasi & Prediksi Acne Grading (CNN, MobileNet, ResNet50)

## 1) Deskripsi Proyek
Proyek ini membangun sistem **klasifikasi tingkat keparahan jerawat (Acne Grading)** menjadi 3 kelas:
- **Level_0 = Ringan**
- **Level_1 = Sedang**
- **Level_2 = Berat**

Sistem disajikan dalam bentuk **website interaktif menggunakan Streamlit**, dengan dua fitur utama:
1. **Tab Evaluasi Model**: menampilkan ringkasan performa dan perbandingan ketiga model.
2. **Tab Prediksi**: pengguna mengunggah gambar, memilih model, lalu sistem menampilkan prediksi kelas dan probabilitasnya.

---

## 2) Dataset dan Preprocessing

### 2.1 Struktur Dataset
Dataset disusun dalam bentuk folder per kelas.

**Contoh struktur dataset (tanpa split):**
```
acne_dataset/
├─ Level_0/
├─ Level_1/
└─ Level_2/
```

**Contoh struktur dataset (train/val split):**
```
acne_dataset_split/
├─ train/
│  ├─ Level_0/
│  ├─ Level_1/
│  └─ Level_2/
└─ val/
   ├─ Level_0/
   ├─ Level_1/
   └─ Level_2/
```

### 2.2 Preprocessing
Tahapan preprocessing yang digunakan pada proses training dan pada dashboard prediksi:
- Konversi gambar ke **RGB**
- Resize ke **224 × 224**
- Normalisasi nilai piksel dengan **rescale 1/255**

### 2.3 Data Augmentation
Untuk meningkatkan variasi data latih dan mengurangi overfitting, dilakukan augmentasi seperti:
- Rotation
- Width/height shift
- Shear
- Zoom
- Horizontal flip

---

## 3) Model yang Digunakan

### 3.1 CNN (Custom CNN from Scratch)
Model CNN dibangun dari nol menggunakan beberapa blok **Conv2D + MaxPooling**, lalu head klasifikasi (**Dense + Dropout**) untuk output 3 kelas.

**Karakteristik:**
- Arsitektur sederhana dan mudah dipahami
- Sangat bergantung pada variasi dan jumlah dataset
- Cenderung lebih rentan overfitting dan bias kelas bila dataset tidak seimbang

**Output model:**
- `acne_grading_cnn_model.h5`

---

### 3.2 MobileNet (Transfer Learning)
MobileNet menggunakan bobot pre-trained (ImageNet) dengan `include_top=False` dan `pooling='avg'`. Base model dibekukan pada awal, lalu ditambahkan head classifier (Dense, BatchNorm, Dropout).

**Kelebihan:**
- Ringan dan cepat
- Biasanya kuat untuk dataset terbatas
- Baik untuk deployment karena lebih efisien

**Output model:**
- `mobilenet_acne_final.keras`

---

### 3.3 ResNet50 (Fine-tuning)
ResNet50 juga menggunakan bobot pre-trained (ImageNet) dengan `include_top=False`. Dilakukan dua tahap:
1. **Warm-up**: melatih head classifier (base model di-freeze)
2. **Fine-tuning**: membuka sebagian layer terakhir untuk meningkatkan performa

**Kelebihan:**
- Kuat untuk ekstraksi fitur
- Fine-tuning dapat meningkatkan akurasi secara signifikan

**Output model:**
- `resnet50_acne_finetuned_final.keras`
- `best_resnet_model.keras` (checkpoint terbaik)

---

## 4) Hasil Evaluasi dan Analisis Perbandingan

### 4.1 Ringkasan Hasil Evaluasi (WAJIB dalam bentuk tabel)
Evaluasi dilakukan menggunakan metrik: **Accuracy, Precision, Recall, F1-Score**.

| Nama Model | Akurasi | Hasil Analisis |
|---|---:|---|
| CNN (Custom) | **0.47** | Model cenderung memprediksi hanya **Level_1 (Sedang)**. Performa buruk pada **Level_0** dan **Level_2** (precision/recall = 0). Ini menunjukkan indikasi kuat **bias kelas / ketidakseimbangan data** dan keterbatasan CNN dari nol dalam belajar fitur yang kuat. |
| ResNet50 (Fine-tuned) | **0.54** | Performa meningkat dibanding CNN, namun masih gagal mengenali **Level_2** (precision/recall = 0). Model lebih baik membedakan Level_0 dan Level_1, tetapi belum stabil untuk kelas minoritas. Fine-tuning membantu, namun masih terdampak **imbalance**. |
| MobileNet (Transfer Learning) | **0.74** | Performa terbaik dan paling seimbang pada semua kelas (Level_0/1/2 memiliki precision & recall yang baik). Transfer learning membantu ekstraksi fitur yang lebih kuat sehingga akurasi dan generalisasi meningkat. Cocok digunakan sebagai model utama untuk prediksi. |

### 4.2 Kesimpulan Perbandingan
Berdasarkan hasil evaluasi:
- **MobileNet** memberikan performa terbaik (**akurasi 0.74**) dan paling konsisten pada semua kelas.
- **ResNet50** lebih baik dari CNN, namun masih kesulitan pada kelas **Level_2** pada hasil evaluasi yang diperoleh.
- **CNN dari nol** kurang efektif pada dataset ini karena cenderung bias terhadap kelas tertentu.

---

## 5) Struktur Repository
Contoh struktur repository:

```
UAP/
├─ app.py
├─ pyproject.toml
├─ pdm.lock
├─ README.md
└─ models/
   ├─ acne_grading_cnn_model.h5
   ├─ mobilenet_acne_final.keras
   ├─ resnet50_acne_finetuned_final.keras
   └─ best_resnet_model.keras
```

---

## 6) Panduan Menjalankan Sistem Website Secara Lokal

### 6.1 Persiapan
Pastikan sudah terinstall:
- Python (disarankan **3.10+**)
- PDM

Install PDM:
```bash
pip install pdm
```

### 6.2 Install Dependencies
Masuk ke folder project:
```bash
cd UAP
pdm install
```

### 6.3 Pastikan File Model Ada
Letakkan file model pada folder:
```
./models/
```

### 6.4 Menjalankan Streamlit
Jalankan website:
```bash
pdm run streamlit run app.py
```

Jika berhasil, Streamlit akan memberikan URL lokal (biasanya):
```
http://localhost:8501
```

---

## 7) Cara Menggunakan Website
1. Buka tab **Evaluasi Model** untuk melihat ringkasan dan perbandingan performa.
2. Buka tab **Prediksi** → upload gambar → pilih model → klik **Prediksi**.
3. Sistem menampilkan:
   - Prediksi Level (Level_0/Level_1/Level_2)
   - Interpretasi Kategori (Ringan/Sedang/Berat)
   - Confidence
   - Grafik probabilitas per kelas


