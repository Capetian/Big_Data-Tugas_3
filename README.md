Kelas: Big Data

Nama: Raden Bimo Rizki Prayogo

NRP: 0511740000139

# Big Data - Tugas 3

## Business Understanding
Workflow ini digunakan untuk melakukan rekomendasi film dengan Collaborative Filtering menggunakan Alternating Least Squares.

Overview Workflow:

![picture](/img/overview.PNG)

## Data Understanding

Data yang digunakan adalah dataset MovieLens small yang berisi 100,000 rating film untuk 9000 film dari 600 user. Data yang digunakan pada workflow ini terdapat di file ratings.csv dan movies.csv.

![picture](/img/ratings_csv.PNG)
File ratings.csv berisi data tentang rating suatu movie dari suatu user. File terdiri dari 100836 baris dan 4 kolom. Kolom tersebut antara lain:

* userId: Id user
* movieId: Id film
* rating: rating suatu movie (dari 0 - 5, lebih besar lebih bagus, -1 jika film belum ditonton oleh user)
* timestamp : timestamp user saat melakukan rating

![picture](/img/movies_csv.PNG)
File movies.csv berisi detil tentang film yang dirating. File terdiri dari 9742 baris dan 3 kolom. Kolom tersebut antara lain:

* movieId : ID film
* title : nama film
* genres : genre film, bisa lebih dari satu

## Data Preparation
### Membuat Spark Context
Pertama-tama sebuah context spark perlu disiapkan terlebih dahulu. Spark Context dibuat secara lokal dan menggunakan 2 executor (thread).

![picture](/img/spark_setup.PNG)
![picture](/img/spark_con.PNG)

### Menyiapkan Data Training

#### Mengektraksi Data File CSV
File ratings.csv dibaca dengan node File Reader dan kemudian diubah menjadi sebuah Spark dataframe menggunakan node Table to Spark. Dataset kemudia bagi menjadi test set dan training set dengan rasio 80% : 20%.

![picture](/img/read_spark.PNG)

Membaca file csv:
![picture](/img/file_read.PNG)

Melakukan partisi:
![picture](/img/partition.PNG)


#### Menyiapkan Profil User
Dibuatkan suatu profil user untuk menanyakan rating 20 film random dari user dan melakukan rekomendasi untuk user sesuai preferensi user.

##### Menanyakan rating dari user:
![picture](/img/ask_user.PNG)
Membaca daftar film di movies.csv, membuat tabel dengan format dan kolom yang sama dengan data yang sudah dijadikan dataframe dan mengambil 20 film untuk dirating oleh user. Hasil rating akan diubah menjadi Spark dan dijadikan data training.

Komponen add fields:
![picture](/img/komp_add_field.PNG)
menambah kolom yang relevan untuk dijadikan data training dan mengacak movie yang akan dirating.

Komponen Ask User for Movie Rating:
![picture](/img/komp_ask_user.PNG)
menanyakan rating dari user. Akan muncul UI seperti berikut.

![picture](/img/ask_user_ui.PNG)

##### Menanyakan rating dari user:
![picture](/img/prep_recom.PNG)
Film selain 20 film yang dirating akan digunakan untuk mencari rekomendasi film unutk user.

Komponen no rating:
![picture](/img/komp_no_rating.PNG)
Film dirating sebagai -1 (belum ditonton).

## Modelling
![picture](/img/modelling.PNG)
Dataset dari ratings.csv digabung dengan penilaian user, kemudian lakukan collaborative filtering dengan ALS.

![picture](/img/ALS.PNG)


Hasil training:
![picture](/img/res_training.PNG)

## Evaluasi
Menggunakan test set dari hasil partisi, lakukan penilaian akurasi model. Nilai prediksi yang tidak ada digantikan nilai rata-rata.

![picture](/img/evaluation.PNG)

Konfigurasi missing value:
![picture](/img/miss_val.PNG)

Hasil Evaluasi:
![picture](/img/scorer.PNG)


## Deployment 
![picture](/img/Deployment.PNG)
Lakukan rekomendasi berdasarkan model yang sudah dibentuk, kemudian tampilkan 20 rekomendasi film berdasarkan prediksi yang paling tinggi. Simpan rekomendai sebgai file csv.

Komponen Top 20 recommended movie:
![picture](/img/komp_top_20.PNG)
Filter nilai yang kosong, urutkan secara descending, ambil 20 tertinggi dan join movieId dengan nama film.

Komponen Top 20 recommended movie:
![picture](/img/komp_display_recom.PNG)

Menampilkan UI berisi rekomendasi film seperti berikut:
![picture](/img/recom_ui.PNG)

Hasil CSV yang dihasilkan:
![picture](/img/res_csv.PNG)


# Benchmarking:
Untuk membandingkan kecepatan pembacaan File Reader dan CSV to Spark dilakukan dengan membaca file ratings.csv versi small dan 25M. Versi small memiliki 4 kolom dan 100,000 baris dan Versi 25M memiliki 4 kolom dan 25 juta baris. Hasil benchmarking yang didapatkan adalah sebagai berikut:
## Dataset Small:
![picture](/img/benchmark_small.PNG)
catatan: KNIME perlu berkomunikasi dengan spark terlebih dahulu sehingga waktu eksekusi lebih lama dari yang seharusnya. Waktu eksekusi seharusnya adalah sebagai berikut:
![picture](/img/benchmark_small_SparkUI.PNG)

## Dataset 25M:
![picture](/img/benchmark_big.PNG)
catatan: KNIME perlu berkomunikasi dengan spark terlebih dahulu sehingga waktu eksekusi lebih lama dari yang seharusnya. Waktu eksekusi seharusnya adalah sebagai berikut:
![picture](/img/benchmark_big_SparkUI.PNG)

## Kesimpulan:
Untuk dataset kecil, CSV to Spark lebih lambat karena perlu dilakukan job assigment terlebih dahulu ke tiap-tiap executor yang akan melakukan task. Untuk dataset besar, CSV lebih cepat karena tugas dibagikan ke berbagai executor agar bisa dikerjakan secara parallel. 



