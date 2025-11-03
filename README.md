# Laporan Praktikum Big Data

## Ringkasan Tim
- **Nama Praktikan**: _Eky Fikri Yamansyah_
- **NIM**: _312310572_


## Daftar Isi
1. [Pendahuluan](#pendahuluan)
2. [Praktikum 1 – HDFS, MongoDB, Cassandra](#praktikum-1--hdfs-mongodb-cassandra)
3. [Praktikum 2 – Word Count (MapReduce, Spark RDD, DataFrame)](#praktikum-2--word-count-mapreduce-spark-rdd-dataframe)
4. [Praktikum 3 – _(sesuaikan topik)_](#praktikum-3--)
5. [Praktikum 4 – _(sesuaikan topik)_](#praktikum-4--)
6. [Refleksi Akhir](#refleksi-akhir)
7. [Lampiran](#lampiran)

---

> **Dokumentasi**  
> - [✅] Praktikum 1 selesai
> - [✅] Praktikum 2 selesai
> - [ ] Praktikum 3 selesai
> - [ ] Praktikum 4 selesai

---

## Praktikum 1 – HDFS, MongoDB, Cassandra

### Tujuan Pembelajaran
- Memahami manajemen berkas terdistribusi menggunakan HDFS.
- Menerapkan operasi dasar NoSQL pada MongoDB.
- Mengevaluasi penyimpanan kolumnar dan query pada Cassandra.
- Membandingkan karakteristik penyimpanan ketiga teknologi tersebut.

### Kebutuhan Awal
- File lokal `dataset.csv`.
- Hadoop service aktif (`namenode`, `datanode`, dsb.).
- Layanan MongoDB dan Cassandra berjalan.
- Direktori dokumentasi gambar: `doc/` (misal `hdfs1.png`, `mongo1.png`, `cassandra1.png`).

### Bagian A – HDFS
1. **Membuat direktori praktikum di HDFS**
   ```bash
   hdfs dfs -mkdir /praktikum
   ```
   Direkomendasikan memastikan direktori belum ada dengan `hdfs dfs -ls /`.

2. **Mengunggah dataset ke HDFS**
   ```bash
   hdfs dfs -put dataset.csv /praktikum/
   ```
   Pastikan file berhasil tersalin tanpa duplikat nama.

3. **Memverifikasi isi direktori**
   ```bash
   hdfs dfs -ls /praktikum/
   ```
   Catat ukuran file dan replika yang ditampilkan.

4. **Membaca isi dataset langsung dari HDFS**
   ```bash
   hdfs dfs -cat /praktikum/dataset.csv
   ```
   Dokumentasikan potongan output penting (misal 5 baris pertama) untuk referensi.

### Dokumentasi Visual
Tambahkan bukti visual dari setiap tahapan:

![Tampilan direktori HDFS](doc/hdfs1.png)
_Gambar 1. Tampilan hasil `hdfs dfs -cat /praktikum/dataset.csv` atau aktivitas relevan lainnya._

Berikan penjelasan singkat mengenai insight dari gambar, misalnya struktur data, metadata direktori, atau kesalahan yang ditemukan.

### Latihan Mandiri
Lakukan eksperimen tambahan untuk memperkuat pemahaman:

```bash
hdfs dfs -put ~/bigfile.txt /user/kyymain/bigdata/
hdfs dfs -put ~/bigfile.txt /praktikum/
hdfs fsck /user/kyymain/bigdata/bigfile.txt -files -blocks -locations
```

- Jelaskan tujuan menempatkan `bigfile.txt` pada dua lokasi berbeda.
- Analisis hasil dari `hdfs fsck`, terutama informasi jumlah blok, lokasi replika, dan potensi mismatch.

![Hasil fsck](doc/hdfs2.png)
_Gambar 2. Visualisasi hasil pemeriksaan integritas HDFS dengan `hdfs fsck`._

> **Catatan Analisis Gambar**  
> Terangkan apakah semua blok terdistribusi merata, apakah terdapat blok hilang/korup, dan bagaimana implikasinya terhadap ketersediaan data.

### Bagian B – MongoDB
1. **Memilih basis data kerja**
   ```javascript
   use praktikum
   ```
   Pastikan database otomatis dibuat saat perintah insert pertama dijalankan.

2. **Menambahkan data mahasiswa**
   ```javascript
   db.mahasiswa.insertOne({ nim: "321564", nama: "Budi", jurusan: "Informatika" })
   ```
   Catat acknowledgement (`acknowledged: true`) sebagai bukti keberhasilan operasi.

3. **Menambahkan beberapa data sekaligus**
   ```javascript
   db.mahasiswa.insertMany([
     { nim: "12346", na ma: "Budi", jurusan: "Sistem Informasi" },
     { nim: "12347", nama: "Citra", jurusan: "Teknik Komputer" }
   ])
   ```
   Evaluasi dampak `insertMany` terhadap throughput dan pastikan tidak ada duplikasi `nim`.

4. **Membuat indeks pada kolom NIM**
   ```javascript
   db.mahasiswa.createIndex({ nim: 1 })
   ```
   Simpan hasil pembuatan indeks (nama indeks) dan jelaskan tujuan indeks berurutan (`ascending`) pada atribut unik.

5. **Menampilkan seluruh data mahasiswa**
   ```javascript
   db.mahasiswa.find()
   ```
   Simpan output JSON beserta jumlah dokumen yang dihasilkan.

6. **Query berbasis jurusan**
   ```javascript
   db.mahasiswa.find({ jurusan: "Informatika" })
   ```
   Jelaskan bagaimana MongoDB memfilter koleksi dan potensi penggunaan indeks di masa depan.

7. **Menampilkan data terurut berdasarkan nama**
   ```javascript
   db.mahasiswa.find().sort({ nama: 1 })
   ```
   Catat perbedaan performa sebelum dan sesudah indeks dibuat, serta dampak urutan ascending terhadap hasil.

8. **Latihan: Menyimpan Dokumen Nested**
   ```javascript
   db.biodata.insertOne({
     nama: "Eky Fikri Yamansyah",
     umur: 22,
     alamat: {
       jalan: "Jl. Mawar No. 12",
       kecamatan: "Cikarang Selatan",
       kota: "Bekasi",
       provinsi: "Jawa Barat"
     },
     kontak: {
       email: "eky@example.com",
       hp: "0895-xxxx-xxxx"
     },
     hobi: ["coding", "cybersecurity", "music"]
   })
   
   db.biodata.find().pretty()
   ```
   Soroti struktur embedded document (`alamat`, `kontak`) dan array (`hobi`) serta kegunaannya untuk menyimpan data semi-terstruktur.

![Operasi MongoDB](doc/mongo1.png)
_Gambar 2. Tampilan shell MongoDB yang menampilkan hasil insert dan query._

![Indeks dan Sorting MongoDB](doc/mongo2.png)
_Gambar 3. Bukti pembuatan indeks dan hasil query terurut pada koleksi `mahasiswa`._

![Dokumen Nested MongoDB](doc/mongo3.png)
_Gambar 4. Contoh penyimpanan dokumen bersarang pada koleksi `biodata`._

> **Analisis MongoDB**  
> Jelaskan struktur dokumen yang digunakan, highlight fleksibilitas skema, serta bandingkan dengan tabel relasional/kolumnar.

### Bagian C – Cassandra
1. **Membuat keyspace praktikum**
   ```sql
   CREATE KEYSPACE praktikum
   WITH replication = { 'class': 'SimpleStrategy', 'replication_factor': 1 };
   ```
   Pastikan keyspace aktif dengan `USE praktikum;` sebelum melanjutkan ke langkah berikut.

2. **Membangun tabel mahasiswa**
   ```sql
   CREATE TABLE mahasiswa (
     nim text PRIMARY KEY,
     nama text,
     jurusan text
   );
   ```
   Gunakan `PRIMARY KEY` pada `nim` agar setiap baris memiliki identitas unik.

3. **Menambahkan data awal**
   ```sql
   INSERT INTO mahasiswa (nim, nama, jurusan)
   VALUES ('12345', 'Budi', 'Informatika');
   ```
   Verifikasi bahwa write berhasil dengan meninjau `timestamp` dan `applied` pada response.

4. **Membaca isi tabel**
   ```sql
   SELECT * FROM mahasiswa;
   ```
   Dokumentasikan hasil query beserta struktur kolom yang tampil.

5. **Eksperimen lanjutan**
   ```sql
   INSERT INTO mahasiswa (nim, nama, jurusan) VALUES ('12346', 'Citra', 'Sistem Informasi');
   INSERT INTO mahasiswa (nim, nama, jurusan) VALUES ('12347', 'Dewi', 'Teknik Komputer');
   SELECT * FROM mahasiswa WHERE jurusan = 'Informatika' ALLOW FILTERING;
   ALTER KEYSPACE praktikum WITH replication = { 'class': 'SimpleStrategy', 'replication_factor': 3 };
   ```
   Jelaskan konsekuensi `ALLOW FILTERING` terhadap performa dan kenapa sebaiknya menggunakan partisi yang dirancang sesuai pola query. Catat juga kebutuhan penyesuaian cluster sebelum menaikkan `replication_factor`.

6. **Validasi replikasi**
   - Gunakan `DESCRIBE KEYSPACE praktikum;` untuk memastikan konfigurasi baru terpasang.
   - Jika memungkinkan, rekam hasil `nodetool status` guna memantau distribusi node.

![Operasi Cassandra](doc/cassandra1.png)
_Gambar 5. Bukti eksekusi query Cassandra dan status keyspace._

> **Analisis Cassandra**  
> Uraikan pola akses yang cocok untuk Cassandra, trade-off konsistensi, serta perbedaan mendasar dibanding MongoDB dan HDFS.

### Latihan – Cassandra 2 Node dengan Docker Compose
1. **Cek status cluster**  
   ```bash
   sudo docker exec -it cassandra-node1 nodetool status
   ```
   Pastikan kedua node (`UN` status) aktif sebelum menjalankan query.

2. **Aktifkan keyspace dengan replika ganda**
   ```sql
   CREATE KEYSPACE praktikum_cluster
   WITH replication = { 'class': 'SimpleStrategy', 'replication_factor': 2 };
   
   USE praktikum_cluster;
   ```
   Konfirmasi bahwa `replication_factor = 2` agar data tersalin ke dua node.

3. **Buat tabel dan isi data**
   ```sql
   CREATE TABLE mahasiswa (
       nim text PRIMARY KEY,
       nama text,
       jurusan text,
       angkatan int
   );
   
   INSERT INTO mahasiswa (nim, nama, jurusan, angkatan) VALUES ('001', 'Budi Santoso', 'Informatika', 2021);
   INSERT INTO mahasiswa (nim, nama, jurusan, angkatan) VALUES ('002', 'Siti Aminah', 'Sistem Informasi', 2022);
   INSERT INTO mahasiswa (nim, nama, jurusan, angkatan) VALUES ('003', 'Ahmad Zaki', 'Teknik Komputer', 2021);
   INSERT INTO mahasiswa (nim, nama, jurusan, angkatan) VALUES ('004', 'Rina Wati', 'Informatika', 2023);
   INSERT INTO mahasiswa (nim, nama, jurusan, angkatan) VALUES ('005', 'Joko Widodo', 'Sistem Informasi', 2020);
   
   SELECT * FROM mahasiswa;
   ```

4. **Amati distribusi token antar node**
   ```bash
   sudo docker exec -it cassandra-node1 cqlsh -e "SELECT nim, nama, TOKEN(nim) FROM praktikum_cluster.mahasiswa;"
   ```
   Catat nilai token sebagai indikator node penyimpanan setiap baris.

5. **Verifikasi replika di masing-masing node**
   ```bash
   sudo docker exec -it cassandra-node1 cqlsh -e "SELECT * FROM praktikum_cluster.mahasiswa;"
   sudo docker exec -it cassandra-node2 cqlsh -e "SELECT * FROM praktikum_cluster.mahasiswa;"
   ```
   Bandingkan hasil untuk memastikan data tersedia di kedua node sesuai replika yang ditentukan.

![Distribusi Cassandra Cluster](doc/cassandra.png)
_Gambar 6. Distribusi token dan replika data pada cluster Cassandra dua node._

### Kesimpulan Praktikum 1
- Soroti temuan utama dari ketiga bagian (HDFS, MongoDB, Cassandra).
- Catat isu yang muncul di masing-masing layanan dan solusi penanganannya.
- Buat daftar rencana tindak lanjut seperti konfigurasi replikasi lanjutan atau optimasi query.

---

## Praktikum 2 – Word Count (MapReduce, Spark RDD, DataFrame)
### Ringkasan Tujuan
- Membandingkan tiga pendekatan pemrosesan data besar untuk kasus Word Count: Hadoop Streaming (MapReduce), Spark RDD, dan Spark DataFrame/SQL.
- Memahami perbedaan arsitektur, sintaks, dan performa masing‑masing teknologi.

### Requirements
- Hadoop (cluster/single‑node) + HDFS aktif.
- Apache Spark 3.x + PySpark.
- Python 3.x.
- File: `input.txt`, `mapper.py`, `reducer.py`.

### Setup Awal
1) Siapkan direktori dan unggah data ke HDFS
```bash
hdfs dfs -mkdir -p /praktikum/latihan_mr/input
hdfs dfs -put input.txt /praktikum/latihan_mr/input/
hdfs dfs -ls /praktikum/latihan_mr/input
```

2) Pastikan file mapper/reducer dapat dieksekusi
```bash
chmod +x mapper.py reducer.py
```

---

### Sesi 1 — MapReduce (Hadoop Streaming)
Gunakan skrip berikut:

`mapper.py`
```python
#!/usr/bin/env python
import sys

for line in sys.stdin:
    words = line.strip().split()
    for word in words:
        print(f"{word.lower()}\t1")
```

`reducer.py`
```python
#!/usr/bin/env python
import sys
from itertools import groupby

for key, group in groupby(sys.stdin, key=lambda x: x.split('\t', 1)[0]):
    try:
        total_count = sum(int(line.split('\t', 1)[1].strip()) for line in group)
        print(f"{key}\t{total_count}")
    except ValueError:
        pass
```

Jalankan Hadoop Streaming:
```bash
hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \ 
  -files mapper.py,reducer.py \ 
  -input /praktikum/latihan_mr/input \ 
  -output /praktikum/latihan_mr/output_mr \ 
  -mapper mapper.py \ 
  -reducer reducer.py
```

Tips MapReduce:
- Jika output sudah ada: `hdfs dfs -rm -r /praktikum/latihan_mr/output_mr`.
- Lihat hasil: `hdfs dfs -cat /praktikum/latihan_mr/output_mr/part-*`.
- Top 20 frekuensi (desc):
  ```bash
  hdfs dfs -cat /praktikum/latihan_mr/output_mr/part-* | sort -k2,2nr | head -n 20
  ```
- Ambil ke lokal (opsional):
  ```bash
  hdfs dfs -get /praktikum/latihan_mr/output_mr ./output_mr
  ```

---

### Sesi 2 — Spark RDD
Contoh via PySpark shell:
```bash
pyspark --master local[*]
```

Script RDD:
```python
# Pilih salah satu sumber data
lines = spark.sparkContext.textFile("input.txt")
# atau: lines = spark.sparkContext.textFile("hdfs:///praktikum/latihan_mr/input/input.txt")

words = lines.flatMap(lambda s: s.lower().split())
pairs = words.map(lambda w: (w, 1))
counts = pairs.reduceByKey(lambda a, b: a + b)

# Aksi
counts.take(5)
# Simpan (opsional)
# counts.saveAsTextFile("output_rdd")
```

Catatan RDD:
- Tanpa aksi seperti `collect()`, `take()`, atau `saveAsTextFile()`, transformasi tidak dieksekusi (lazy evaluation).
- Gunakan `time` atau Spark UI (`http://localhost:4040`) untuk mengamati waktu eksekusi dan stage.

---

### Sesi 3 — Spark DataFrame/SQL
```python
from pyspark.sql.functions import explode, split, col

df = spark.read.text("input.txt")
# atau: df = spark.read.text("hdfs:///praktikum/latihan_mr/input/input.txt")

words_df = df.select(explode(split(col("value"), " ")).alias("word")).filter(col("word") != "")
counts_df = words_df.groupBy("word").count()

counts_df.orderBy(col("count").desc()).show(10)

# Bonus SQL
counts_df.createOrReplaceTempView("word_counts")
spark.sql("SELECT word, count FROM word_counts ORDER BY count DESC LIMIT 10").show()

# Lihat rencana eksekusi
counts_df.explain()
```

---

### Hasil & Analisis
Gunakan tabel berikut untuk membandingkan hasil (isi sesuai eksperimen Anda):

| Teknologi | Waktu Eksekusi | Kompleksitas Kode | Kelebihan | Kekurangan |
|-----------|----------------|-------------------|-----------|------------|
| MapReduce | _(isi)_        | Tinggi            | Reliable, mature | Lambat, verbose |
| Spark RDD | _(isi)_        | Sedang            | Fleksibel, cepat | Kurang optimasi otomatis |
| DataFrame | _(isi)_        | Rendah            | Optimisasi Catalyst, paling cepat | Kurang fleksibel untuk operasi sangat kustom |

Pertanyaan panduan:
- Mengapa MapReduce memerlukan berkas `mapper` dan `reducer` terpisah?  
  Jawab singkat: pemisahan fase Map/Reduce eksplisit dan berbasis stream.
- Apa yang terjadi jika `collect()` dihapus pada RDD?  
  Jawab singkat: tidak ada eksekusi karena lazy evaluation.
- Mengapa DataFrame terasa lebih mudah?  
  Jawab singkat: API deklaratif mirip SQL; dioptimalkan oleh Catalyst.

---

### Troubleshooting
- Permission denied `mapper.py`: `chmod +x mapper.py reducer.py`.
- Output directory exists: `hdfs dfs -rm -r /praktikum/latihan_mr/output_mr`.
- PySpark tidak ditemukan: `export PATH=$SPARK_HOME/bin:$PATH`.
- `input.txt` tidak ditemukan: verifikasi path (lokal/HDFS) dan izin akses.
- Memory error Spark: jalankan dengan `pyspark --driver-memory 4g --executor-memory 4g`.

### Bukti Eksperimen (Gambar)
Sertakan tangkapan layar hasil eksekusi pada bagian ini:

![Hadoop Streaming Output](doc/pr1.png)
_Gambar 1. Hasil MapReduce (Hadoop Streaming) untuk Word Count._

![Spark RDD/DataFrame Output](doc/pr2.png)
_Gambar 2. Hasil Word Count menggunakan Spark (RDD/DataFrame/SQL)._ 

Letakkan file pada folder `doc/` dengan nama: `doc/pr1.png` dan `doc/pr2.png`.

### Checklist Praktikum 2
- [ ] MapReduce sukses, hasil diverifikasi dari HDFS.
- [ ] Spark RDD berjalan dan menyimpan/menampilkan hasil.
- [ ] Spark DataFrame/SQL menampilkan top 10 kata.
- [ ] Tabel perbandingan dan analisis terisi.
- [ ] Screenshot disimpan di folder `doc/`.

---

## Praktikum 3 – _(sesuaikan topik)_
Gunakan struktur yang sama dengan praktikum sebelumnya:
- **Tujuan**
- **Setup**
- **Langkah Praktikum**
- **Script / Query**
- **Hasil & Screenshot**
- **Analisis**

Tambahkan checklist kendala dan solusi agar mudah dievaluasi pada sesi review.

---

## Praktikum 4 – _(sesuaikan topik)_
Sediakan ruang untuk:
- Rangkaian percobaan lanjutan (misal Spark, Streaming, atau Machine Learning).
- Benchmarking kecil beserta tabel perbandingan.
- Evaluasi akhir terhadap performa cluster atau pipeline.

Gunakan tabel untuk merangkum hasil, contoh:

| Pengujian | Deskripsi | Waktu Eksekusi | Catatan |
|-----------|-----------|----------------|---------|
| Test 1    | _Isi_     | _00:00_        | _Insight_ |
| Test 2    | _Isi_     | _00:00_        | _Insight_ |

---

## Refleksi Akhir
- **Highlight Pembelajaran**: Tiga poin terbesar yang didapat dari keseluruhan praktikum.
- **Rencana Pengembangan**: Teknologi/konsep apa yang ingin dieksplorasi berikutnya.
- **Evaluasi Tim**: Apa yang berjalan baik dan apa yang perlu ditingkatkan (komunikasi, kolaborasi, manajemen waktu).

---

## Lampiran
- **Link Dataset Tambahan**: Cantumkan sumber data eksternal jika digunakan.
- **Konfigurasi Teknis**: Paste isi file konfigurasi penting (misal `core-site.xml`) bila relevan.
- **Referensi**: Buku, artikel, atau dokumentasi resmi yang dirujuk selama praktikum.

> _Catatan_: Simpan seluruh bukti visual di folder `doc/` dan gunakan penamaan konsisten (`prakX-stepY.png`) agar mudah dilacak.
