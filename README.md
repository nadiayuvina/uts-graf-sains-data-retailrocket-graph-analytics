# Ujian Tengah Semester Prodi S1 Sains Data Universitas Negeri Surabaya

Nama: Nadia Zahra Yuvina

Kelas: 2024 A

Nim: 24031554192

# RetailRocket Graph Analytics

## Deskripsi Proyek

Proyek ini dibuat untuk melakukan analisis hubungan antara User dan Item menggunakan dataset RetailRocket. Analisis dilakukan dengan memanfaatkan database graf Neo4j dan bahasa pemrograman Python. Melalui proyek ini, data interaksi tersebut digunakan untuk membentuk graf yang menggambarkan hubungan antara User dan Item. Selain itu, dilakukan perhitungan kemiripan menggunakan Cosine Similarity serta pembuatan sistem rekomendasi menggunakan algoritma K-Nearest Neighbors (KNN). Hasil rekomendasi kemudian dievaluasi menggunakan metrik Hit@5 dan NDCG@5 untuk mengetahui kesesuaiannya dengan aktivitas pengguna pada periode testing.

## Tujuan Proyek

Tujuan dari pengerjaan proyek ini adalah:

1. Membentuk hubungan antara User dan Item menggunakan database graf Neo4j.
2. Membuat representasi data interaksi dalam bentuk Vector Space Model (VSM).
3. Menganalisis perbandingan proyeksi graf User–User dan Item–Item.
4. Menghitung kemiripan antar-Item menggunakan metode Cosine Similarity.
5. Membuat sistem rekomendasi menggunakan algoritma KNN.
6. Menentukan tiga Item yang dapat direkomendasikan kepada User terpilih.
7. Mengevaluasi hasil rekomendasi menggunakan Hit@5 dan NDCG@5.

## Dataset

Dataset yang digunakan dalam proyek ini adalah RetailRocket Dataset. Dataset tersebut berisi riwayat interaksi pengguna dengan produk pada platform e-commerce.
unduh datasetnya sebagai berikut:
curl-L-o ecommerce-dataset.zip\
https://www.kaggle.com/api/v1/datasets/download/retailrocket/ecommerce-datase

Lalu untuk proyek, saya membagi menjadi dua periode yaitu:
| Periode | Tanggal | Kegunaan |
|---|---|---|
| Training (P1) | 1–20 Juni 2015 | Membentuk model rekomendasi |
| Testing (P2) | 21–25 Juni 2015 | Mengevaluasi hasil rekomendasi |

Pembagian ini dilakukan agar rekomendasi dibuat berdasarkan interaksi pada periode training, sedangkan data testing digunakan untuk memeriksa hasil rekomendasi.

## Tahapan Analisis

### 1. Pembentukan Graf User–Item

Tahap awal dilakukan dengan membentuk graf berdasarkan data interaksi RetailRocket menggunakan Neo4j.

Graf terdiri dari dua jenis node utama, yaitu:

- `Visitor`, yang merepresentasikan pengguna.
- `Item`, yang merepresentasikan produk.

Hubungan antara User dan Item direpresentasikan melalui beberapa relationship, yaitu:

- `VIEWED`
- `ADDED_TO_CART`
- `BOUGHT`

Melalui graf tersebut, hubungan antara pengguna dan produk dapat dianalisis berdasarkan aktivitas yang tercatat pada dataset.

### 2. Pembentukan Vector Space Model (VSM)

Setelah graf terbentuk, data interaksi digunakan untuk membuat Vector Space Model (VSM). VSM dibentuk dalam bentuk matriks biner dengan ketentuan:

- Nilai 1 menunjukkan bahwa terdapat interaksi antara User dan Item.
- Nilai 0 menunjukkan bahwa tidak terdapat interaksi yang tercatat.

Pada tahap ini dibuat dua bentuk representasi:

**VSM User–Item**

Baris merepresentasikan User, sedangkan kolom merepresentasikan Item.

**VSM Item–User**

Baris merepresentasikan Item, sedangkan kolom merepresentasikan User.

Berdasarkan hasil analisis, diperoleh ukuran matriks sebagai berikut:

| Representasi | Ukuran Matriks |
|---|---|
| VSM User–Item | 212.988 × 94.576 |
| VSM Item–User | 94.576 × 212.988 |

Matriks tersebut digunakan untuk menganalisis hubungan dan kemiripan berdasarkan pola interaksi yang tersedia.

### 3. Analisis Proyeksi Graf

Pada tahap ini dilakukan perbandingan antara proyeksi graf User–User dan Item–Item. Perbandingan dilakukan berdasarkan jumlah kemungkinan pasangan node yang perlu dihitung kemiripannya. Berdasarkan hasil perhitungan, proyeksi Item–Item memiliki jumlah kemungkinan pasangan yang lebih sedikit dibandingkan proyeksi User–User. Tapi hasil tersebut tidak secara langsung menunjukkan bahwa kualitas rekomendasi Item–Item lebih baik, karena kualitas rekomendasi tetap memerlukan evaluasi.

### 4. Perhitungan Cosine Similarity Antar-Item

Perhitungan similarity dilakukan menggunakan metode Cosine Similarity berdasarkan VSM Item–User. Pada tahap ini dipilih Item 5411 sebagai Item acuan karena memiliki jumlah User yang berinteraksi paling banyak pada data yang digunakan untuk membentuk VSM.

Berdasarkan hasil perhitungan, diperoleh lima Item dengan similarity tertinggi:

| Item ID | Cosine Similarity |
|---|---:|
| 33030 | 0,194950 |
| 33956 | 0,105435 |
| 358234 | 0,102665 |
| 375492 | 0,080496 |
| 89143 | 0,075612 |

Hasil similarity kemudian disimpan ke database Neo4j menggunakan relationship `SIMILAR_TO` dengan properti `score`. Setelah disimpan, hasil tersebut dapat ditampilkan kembali menggunakan query Cypher untuk melihat hubungan Item acuan dengan lima Item yang memiliki similarity tertinggi.

### 5. Sistem Rekomendasi Menggunakan KNN

Pada tahap berikutnya dibuat sistem rekomendasi menggunakan algoritma K-Nearest Neighbors (KNN) dengan pendekatan User-Based Collaborative Filtering. Sistem rekomendasi dibuat berdasarkan kemiripan pola interaksi antar-User pada periode training (P1). Pemilihan User dilakukan secara acak menggunakan random seed = 42 agar hasil pemilihan dapat direproduksi. User yang dipilih harus terdapat pada periode training dan testing serta memenuhi kriteria yang diperlukan untuk proses rekomendasi. Berdasarkan hasil pengacakan, diperoleh User 78440 sebagai User yang digunakan dalam analisis. Dengan menggunakan nilai k = 5, diperoleh lima User terdekat:

| User ID | Cosine Similarity |
|---|---:|
| 315682 | 0,408248 |
| 1274856 | 0,408248 |
| 388661 | 0,333333 |
| 561509 | 0,306186 |
| 1048683 | 0,306186 |

Kelima User tersebut digunakan sebagai dasar dalam menentukan Item yang akan direkomendasikan.

### 6. Hasil Rekomendasi Item

Setelah mendapatkan lima User terdekat, dilakukan pemeringkatan Item berdasarkan skor rekomendasi. Skor diperoleh dari penjumlahan nilai similarity tetangga yang pernah berinteraksi dengan masing-masing Item. Item yang sudah pernah diinteraksikan oleh User terpilih pada periode training tidak dimasukkan kembali ke dalam daftar rekomendasi. Berdasarkan hasil perhitungan, diperoleh lima rekomendasi teratas:

| Ranking | Item ID | Skor Rekomendasi |
|---|---|---:|
| 1 | 364950 | 0,408248 |
| 2 | 424186 | 0,408248 |
| 3 | 449822 | 0,408248 |
| 4 | 466436 | 0,408248 |
| 5 | 40830 | 0,333333 |

Dari lima rekomendasi tersebut, dipilih tiga Item dengan peringkat tertinggi untuk diiklankan kepada User 78440, yaitu:

1. Item 364950
2. Item 424186
3. Item 449822

Ketiga Item tersebut memiliki skor rekomendasi yang sama, yaitu sebesar 0,408248.

### 7. Evaluasi Menggunakan Hit@5 dan NDCG@5

Tahap terakhir adalah mengevaluasi hasil rekomendasi menggunakan metrik Hit@5 dan NDCG@5. Evaluasi dilakukan dengan membandingkan lima rekomendasi teratas terhadap Item yang benar-benar diinteraksikan oleh User 78440 pada periode testing (P2). Berdasarkan data testing, User 78440 tercatat berinteraksi dengan Item 122041. Namun, Item tersebut tidak terdapat dalam lima rekomendasi teratas yang dihasilkan oleh algoritma KNN. Hasil evaluasi yang diperoleh adalah:

| Metrik | Hasil |
|---|---:|
| Hit@5 | 0 |
| NDCG@5 | 0,000000 |

Nilai Hit@5 sebesar 0 menunjukkan bahwa tidak terdapat Item rekomendasi yang sesuai dengan Item aktual pada data testing. Sementara itu, nilai NDCG@5 sebesar 0 menunjukkan bahwa tidak ada Item relevan yang berhasil ditempatkan dalam lima peringkat teratas. Hasil tersebut tidak langsung menunjukkan bahwa dataset yang digunakan buruk atau algoritma KNN tidak bekerja dengan baik. Evaluasi ini hanya dilakukan terhadap satu User, sehingga hasilnya belum dapat digunakan untuk menilai performa algoritma secara keseluruhan.


## Catatan
Proyek ini dibuat sebagai bagian dari pengerjaan tugas akademik yang berkaitan dengan analisis graf dan sistem rekomendasi. Hasil analisis dan evaluasi yang ditampilkan mengikuti data, metode, serta periode pengamatan yang digunakan dalam pengerjaan proyek.
