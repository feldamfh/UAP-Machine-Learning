# UAP-Machine-Learning
# Dashboard Evaluasi & Prediksi Acne Grading (CNN, MobileNet, ResNet50)

## 1. Deskripsi Proyek
Proyek ini bertujuan membangun sistem **klasifikasi tingkat keparahan jerawat (Acne Grading)** ke dalam 3 kelas:
- **Level_0 = Ringan**
- **Level_1 = Sedang**
- **Level_2 = Berat**

Sistem dikembangkan dalam bentuk **website interaktif menggunakan Streamlit**, dengan dua fitur utama:
1. **Evaluasi Model**: menampilkan ringkasan performa dan perbandingan ketiga model (CNN, MobileNet, ResNet50).
2. **Prediksi Gambar**: pengguna mengunggah gambar, memilih model, lalu sistem menampilkan prediksi kelas beserta probabilitasnya.

---

## 2. Dataset dan Preprocessing

### 2.1 Struktur Dataset
Dataset disusun dalam bentuk folder per kelas. Contoh struktur:

