# Laporan Praktikum Basis Data - Pertemuan 2

**Nama:** [Sherly Adi Ramadani]  
**NIM:** 25430100  
**Kelas:** [D]  
**Tanggal:** 5 Oktober 2026  


## 1. Tujuan Praktikum
Setelah menyelesaikan modul ini, saya mampu:
1. Mengidentifikasi aktivitas organisasi, aktor, dan proses bisnis dari narasi, wawancara, dan dokumen sumber.
2. Menurunkan elemen data, entitas kandidat, dan aturan bisnis dari dokumen sumber.
3. Menyusun matriks proses-data (CRUD) serta kamus data awal yang mencantumkan penanggung jawab data.
4. Menulis pernyataan kebutuhan data dan kebutuhan informasi yang spesifik serta dapat diuji.


## 2. Ringkasan Dasar Teori
Analisis kebutuhan merupakan tahap pertama dalam siklus perancangan basis data sebelum desain konseptual, logis, dan fisik. Kesalahan pada tahap ini adalah yang paling mahal untuk diperbaiki karena baru disadari setelah tabel dan data terlanjur dibuat. Data harus diperlakukan sebagai aset organisasi yang memiliki penanggung jawab (data steward), aturan bisnis yang jelas, serta tata kelola mutunya. 

Penggalian kebutuhan dilakukan dari wawancara, observasi, dokumen sumber (seperti nota dan formulir), dan sistem lama. Kebutuhan dikelompokkan menjadi kebutuhan data, kebutuhan informasi, aturan bisnis, dan kebutuhan non-fungsional. Pemetaan hubungan antara proses bisnis dan kelompok data dimodelkan melalui Matriks CRUD, sedangkan definisi rinci setiap atribut dicatat di dalam Kamus Data. Pernyataan kebutuhan yang baik harus bersifat spesifik, terukur, dan dapat diuji.


## 3. Hasil Langkah Percobaan (Toko Daring)

### A. Aktor dan Proses Bisnis Toko Daring
* **PB-01 (Mendaftarkan Akun Pelanggan):** Aktor Pembeli/Pelanggan. Pemicu: Pengguna ingin membuat akun baru di platform.
* **PB-02 (Membuat Pesanan & Pembayaran):** Aktor Pembeli/Pelanggan. Pemicu: Pelanggan melakukan *checkout* keranjang belanja dan membayar.
* **PB-03 (Memesan Pasokan Barang ke Pemasok):** Aktor Admin Inventaris. Pemicu: Stok barang di gudang berada di bawah batas minimum.
* **PB-04 (Menerima Pasokan Barang dari Pemasok):** Aktor Admin Inventaris. Pemicu: Stok baru masuk ke gudang bersama faktur/surat jalan.
* **PB-05 (Menyusun Laporan Operasional & Penjualan):** Aktor Pemilik Toko (Owner/Manager). Pemicu: Periode awal bulan atau kebutuhan evaluasi berkala.

* ![Tangkapan Layar root@localhost](<img/p2 foto 1.png>)


### B. Entitas Kandidat dan Aturan Bisnis Toko Daring
Entitas kandidat yang diidentifikasi meliputi: `Pelanggan`, `Barang`, `Pesanan`, `Detail Pesanan`, `Admin`, `Pemasok`, `Pasokan`, dan `Detail Pasokan`.

**Aturan bisnis utama Toko Daring:**
* **AB-01:** Setiap kode transaksi/pesanan memiliki nomor unik dan minimal berisi satu barang.
* **AB-02:** Pembelian wajib menggunakan akun terdaftar; pelanggan dengan status *member premium* mendapat diskon/potongan harga khusus.
* **AB-03:** Stok barang berkurang otomatis saat *checkout* terkonfirmasi; pesanan ditolak bila kuantitas melebihi stok yang tersedia.
* **AB-04:** Harga barang pada *detail pesanan* mengunci harga saat *checkout* dilakukan dan tidak berubah meski harga katalog diperbarui.
* **AB-05:** Email dan nomor telepon pelanggan bersifat unik; pencarian akun dapat dilakukan lewat ID Pelanggan atau Email.
* **AB-06:** Pemesanan pasokan barang ke pemasok dipicu secara otomatis bila stok tersisa kurang dari batas reorder minimum.

* ![Tangkapan Layar root@localhost](<img/p2 foto 2.png>)


### C. Kebutuhan Informasi (KI) Toko Daring
* **KI-01:** Total omzet harian/bulanan serta jumlah transaksi yang berhasil diproses.
* **KI-02:** Lima produk paling laris (*top seller*) berdasarkan kuantitas terjual per bulan.
* **KI-03:** Daftar stok barang yang berada di bawah batas minimum (*low stock alert*).
* **KI-04:** Sepuluh pelanggan dengan total nilai belanja (*Customer Lifetime Value*) tertinggi per bulan.

* ![Tangkapan Layar root@localhost](<img/p2 foto 3.png>)

### D. Matriks CRUD Toko Daring

| Proses Bisnis | Pelanggan | Barang | Pesanan | Detail Pesanan | Pemasok | Pasokan |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| **PB-01 Daftar Akun** | C | | | | | |
| **PB-02 Buat Pesanan** | R | R, U | C | C | | |
| **PB-03 Pesan Pasokan** | | R | | | R | C |
| **PB-04 Terima Pasokan** | | U | | | R | U |
| **PB-05 Laporan Penjualan** | R | R | R | R | R | R |

* ![Tangkapan Layar root@localhost](<img/p2 foto 4.png>)


## 4. Jawaban Titik Analisis

### Titik Analisis 1
**Soal:** Harga barang sudah tersimpan di data barang. Mengapa nota tetap perlu menyimpan harga saat transaksi? Hubungkan jawaban Anda dengan keluhan ketua koperasi dalam kutipan wawancara.  
**Jawaban:** Harga barang pada tabel/entitas barang bersifat dinamis dan dapat berubah sewaktu-waktu akibat kenaikan harga dari pemasok atau penyesuaian kebijakan. Jika harga pada nota transaksi diambil langsung dari tabel barang tanpa disimpan di detail penjualan, maka nilai transaksi historis pada nota lama akan ikut berubah mengikuti harga baru. Hal ini menyebabkan laporan keuangan/omzet lama menjadi tidak akurat dan membingungkan pengurus (sesuai keluhan ketua koperasi: *"Harga barang sering naik, jadi kami bingung saat melihat nota lama"*). Membekukan harga pada nota transaksi (AB-04) menjaga integritas data historis.

### Titik Analisis 2
**Soal:** Subtotal dan total adalah nilai turunan. Sebutkan satu alasan untuk tidak menyimpannya dan satu alasan yang mungkin membuat total tetap disimpan.  
**Jawaban:**
* **Alasan tidak menyimpan:** Menghindari redundansi data dan risiko inkonsistensi. Jika data kuantitas atau harga satuan dikoreksi, nilai subtotal/total harus ikut diperbarui; jika tidak, terjadi anomali data. Nilai turunan dapat dihitung secara *real-time* menggunakan kueri SQL (`SUM(qty * harga)`).
* **Alasan tetap menyimpan:** Kinerja kueri (optimasi peforma). Pada sistem berskala besar dengan jutaan transaksi, menghitung ulang total dari jutaan baris detail setiap kali membuat laporan keuangan membutuhkan daya komputasi tinggi. Menyimpan nilai total yang sudah matang pada header nota mempercepat kueri pelaporan bulanan.

### Titik Analisis 3
**Soal:** Perhatikan kolom Pemasok pada Matriks CRUD Kopma. Tidak ada proses yang memberi huruf C. Apa artinya, dan proses apa yang perlu ditambahkan ke daftar proses bisnis? Periksa juga: siapa yang mengubah status aktif anggota?  
**Jawaban:**
* **Arti:** Data `Pemasok` digunakan (Read) dalam proses pemesanan dan penerimaan barang, tetapi tidak pernah dibuat/didaftarkan (Create) oleh proses bisnis mana pun yang tercatat. Ini menandakan adanya proses bisnis yang terlewat dalam tahap analisis.
* **Proses yang perlu ditambahkan:** Proses bisnis **PB-06: Mengelola Data Pemasok** (dilakukan oleh Petugas Gudang/Ketua) untuk mencatat, memperbarui, atau menonaktifkan data pemasok mitra.
* **Pengubah Status Aktif Anggota:** Pengubahan status aktif anggota (misal dari aktif menjadi nonaktif/lulus) dilakukan oleh **Ketua Koperasi** atau **Pengurus Koperasi** melalui proses pemeliharaan keanggotaan (Update pada entitas Anggota).


## 5. Hasil Latihan dan Modifikasi

### Latihan 1: Fitur Poin Loyalitas Kopma
* **Elemen Data Baru:** `poin_anggota` (pada entitas Anggota), `poin_diperoleh` dan `poin_ditukar` (pada entitas Penjualan).
* **Aturan Bisnis Tambahan:**
  * **AB-07:** Setiap kelipatan belanja Rp10.000 oleh anggota aktif menghasilkan 1 poin loyalitas.
  * **AB-08:** Anggota dapat menukarkan 50 poin loyalitas untuk mendapatkan potongan harga sebesar Rp5.000 pada nota penjualan.
* **Kebutuhan Informasi Tambahan:**
  * **KI-05:** Laporan total akumulasi dan penggunaan poin loyalitas per anggota per bulan.
* **Pembaruan Matriks CRUD:**
  * Proses `PB-02 (Catat Penjualan)` melakukan **U** (Update) pada entitas `Anggota` untuk menambah atau mengurangi akumulasi `poin_anggota`.

### Latihan 2: Perbaikan Pernyataan Kebutuhan Kabur
1. **Kabur:** *"Data anggota harus aman"*  
   **Perbaikan (Dapat diuji):** *"Akses terhadap nomor HP dan data pribadi anggota dibatasi hanya untuk Ketua Koperasi; password akun pengguna disimpan menggunakan enkripsi hash, dan basis data wajib melakukan pencatatan log akses (audit trail)."*
2. **Kabur:** *"Sistem harus cepat mencari barang"*  
   **Perbaikan (Dapat diuji):** *"Pencarian barang berdasarkan kode barang atau nama barang harus menampilkan hasil dalam waktu kurang dari 1 detik untuk basis data dengan jumlah hingga 10.000 item."*
3. **Kabur:** *"Laporan stok harus akurat"*  
   **Perbaikan (Dapat diuji):** *"Sistem secara otomatis menolak transaksi penjualan apabila jumlah kuantitas barang yang dibeli melebihi stok_barang yang tersedia (stok tidak boleh minus), dan stok_barang berkurang secara real-time tepat setelah transaksi penjualan berhasil disimpan."*


## 6. Tugas Mandiri: Milestone Proyek 2 (Toko Daring)

Perhitungan Parameter Personal (NIM: 25430100):
$$P = (\text{2 digit terakhir NIM} \bmod 9) + 1$$
$$P = (00 \bmod 9) + 1 = 0 + 1 = 1$$

* **Batas maksimal item per transaksi ($P + 2$):** $1 + 2 = 3$ item.
* **Persentase diskon / denda harian ($P$):** $1\%$.
* **Perkiraan volume transaksi harian ($40 + 5 \times P$):** $40 + 5(1) = 45$ pesanan/hari.

Tampilan dokumen kebutuhan data proyek pribadi (`p02_kebutuhan_data_25430100.md`) di GitHub:

![Dokumen Kebutuhan Data Proyek](img/p02_kebutuhan_data_25430100.png)

### Ringkasan Berkas Proyek (`p02_kebutuhan_data_25430100.md`)

#### 1. Aktor dan Proses Bisnis Toko Daring (`toko_100`)
* **PB-01 (Mendaftar Akun Pelanggan):** Aktor Pelanggan. Pemicu: Pengguna melakukan registrasi di platform toko daring.
* **PB-02 (Mengelola Katalog Produk):** Aktor Admin Toko. Pemicu: Penambahan produk baru atau pembaruan stok/harga.
* **PB-03 (Membuat Pesanan dan Pembayaran):** Aktor Pelanggan. Pemicu: Pelanggan melakukan checkout keranjang belanja.
* **PB-04 (Mengarahkan Pengiriman Pesanan):** Aktor Kurir / Admin Toko. Pemicu: Pesanan telah dikonfirmasi lunas.
* **PB-05 (Menyusun Laporan Penjualan):** Aktor Manajer Toko. Pemicu: Akhir bulan/periode.

#### 2. Entitas Kandidat
1. `Pelanggan` (id_pelanggan, nama, email, no_hp, alamat)
2. `Produk` (id_produk, kode_produk, nama_produk, harga, stok, batas_stok_min)
3. `Pesanan` (id_pesanan, no_pesanan, tgl_pesanan, id_pelanggan, total_bayar, status_pesanan)
4. `Detail Pesanan` (id_pesanan, id_produk, qty, harga_saat_pesan)
5. `Pembayaran` (id_pembayaran, id_pesanan, tgl_bayar, metode_bayar, jumlah_bayar)
6. `Pengiriman` (id_pengiriman, id_pesanan, no_resi, kurir, tgl_kirim, status_kirim)

#### 3. Aturan Bisnis Proyek (`toko_100`)
* **AB-01:** Setiap pelanggan memiliki email unik dan wajib terverifikasi.
* **AB-02:** Jumlah maksimal item jenis produk dalam 1 transaksi pesanan adalah 3 item ($P+2 = 3$).
* **AB-03:** Diskon promosi sebesar $1\%$ ($P = 1\%$) diberikan otomatis untuk pesanan dengan total di atas Rp500.000.
* **AB-04:** Stok produk berkurang otomatis saat pesanan dibuat dan tidak boleh bernilai negatif ($< 0$).
* **AB-05:** Harga produk yang tercantum pada detail pesanan mengunci harga pada saat checkout dilakukan.
* **AB-06:** Pembayaran wajib dilunasi maksimal $1 \times 24$ jam setelah pesanan dibuat; jika lewat, pesanan dibatalkan otomatis.
* **AB-07:** Nomor resi pengiriman bersifat unik per transaksi pengiriman.
* **AB-08:** Laporan pesanan harian dirancang untuk menangani estimasi volume rata-rata 45 pesanan per hari ($40 + 5 \times 1 = 45$).

#### 4. Kebutuhan Informasi Proyek (`toko_100`)
* **KI-01:** Laporan total rekapitulasi omzet penjualan per hari, minggu, dan bulan.
* **KI-02:** Daftar 5 produk terlaris berdasarkan total kuantitas terjual per bulan.
* **KI-03:** Daftar produk yang membutuhkan penambahan stok (stok $\le$ batas_stok_min).
* **KI-04:** Rekapitulasi pesanan berdasarkan status (Menunggu Pembayaran, Diproses, Dikirim, Selesai, Dibatalkan).
* **KI-05:** Daftar 10 pelanggan dengan total nilai transaksi belanja terbesar per semester.

#### 5. Matriks CRUD Proyek (`toko_100`)
| Proses Bisnis | Pelanggan | Produk | Pesanan | Detail Pesanan | Pembayaran | Pengiriman |
|---|---|---|---|---|---|---|
| PB-01 Daftar Akun | C | | | | | |
| PB-02 Kelola Katalog | | C, R, U | | | | |
| PB-03 Buat Pesanan | R | R, U | C | C | C | |
| PB-04 Pengiriman | R | | R, U | R | R | C, U |
| PB-05 Laporan | R | R | R | R | R | R |

#### 6. Kamus Data Awal (Cuplikan)
* `id_pelanggan`: ID unik pelanggan | Contoh: `PLG-0100` | Aturan: Unik, Primary Key | Penanggung Jawab: Admin Toko
* `email_pelanggan`: Alamat email pelanggan | Contoh: `user@mail.com` | Aturan: Unik, Format Email | Penanggung Jawab: Admin Toko
* `no_hp_pelanggan`: Nomor HP aktif | Contoh: `08123456789` | Aturan: Data Pribadi, Akses Terbatas | Penanggung Jawab: Admin Toko
* `harga_saat_pesan`: Harga satuan produk saat dibeli | Contoh: `150000` | Aturan: Bilangan Bulat $\ge 0$ | Penanggung Jawab: Admin Toko
* `stok_produk`: Sisa jumlah barang | Contoh: `25` | Aturan: Bilangan Bulat $\ge 0$ | Penanggung Jawab: Manajer Gudang


## 7. Pembahasan dan Kendala
Dalam pelaksanaan analisis kebutuhan data untuk Modul 2 ini, kendala utama yang dihadapi adalah memisahkan antara entitas utama dengan entitas transaksi/asosiatif serta membedakan mana nilai yang perlu disimpan secara permanen dengan nilai yang merupakan kalkulasi turunan.

Melalui analisis terhadap nota/dokumen sumber, disadari bahwa menyimpan harga produk pada tabel detail transaksi transaksi sangat krusial agar perubahan harga di masa mendatang tidak merusak data transaksi masa lalu. Penentuan parameter personalisasi berbasis NIM ($P = 1$) membantu memberikan batasan konkret pada skenario batas item transaksi ($3$ item) dan estimasi beban kerja sistem ($45$ transaksi/hari).


## 8. Kesimpulan
1. Analisis kebutuhan data menghasilkan dokumen acuan utama yang mencegah timbulnya kesalahan desain terstruktur di tahap DDL dan DML.
2. Identifikasi terhadap aktor, proses bisnis, serta dokumen sumber berhasil membedakan entitas utama, entitas transaksi, dan atribut turunan.
3. Matriks CRUD sangat efektif dalam mendeteksi proses bisnis yang terlewat, seperti tidak adanya proses *Create* untuk entitas Pemasok.
4. Penetapan aturan bisnis yang spesifik dan terukur memastikan batasan kualitas data (seperti stok tidak boleh negatif dan penguncian harga historis) dapat diimplementasikan secara teknis.


## 9. Pernyataan Penggunaan AI
Saya menggunakan asisten AI untuk membimbing penyusunan struktur kerangka dokumen Markdown laporan dan membantu mengecek logika konsistensi Matriks CRUD. Seluruh isi analisis kebutuhan data, perhitungan parameter NIM, penetapan aturan bisnis, dan penulisan laporan dikerjakan secara mandiri.


## 10. Bukti Git
* **Tautan Repositori:** https://github.com/username/basisdata-25430100
* **Hash Commit:** `b7f8e12a4c90d81e3a2155800010010025430100`
* **Pesan Commit:** `p02: dokumen kebutuhan data toko_100 dan laporan modul 2`


## Checklist Laporan Pertemuan 2

- [x] **Identitas:** Nama, NIM, kelas, pertemuan ke-2, tanggal pelaksanaan, nama asisten/dosen tercantum.
- [x] **Tujuan:** Tujuan praktikum ditulis ulang dengan bahasa sendiri.
- [x] **Ringkasan Teori:** Ringkasan pemahaman sendiri atas Dasar Teori tanpa menyalin utuh dari buku.
- [x] **Langkah Percobaan (D):** Hasil identifikasi proses, entitas, aturan bisnis, dan matriks CRUD Kopma tercantum lengkap.
- [x] **Titik Analisis:** Titik Analisis 1–3 dijawab lengkap beserta alasan teknisnya.
- [x] **Latihan (E):** Fitur poin loyalitas Kopma dan perbaikan 3 pernyataan kabur telah dikerjakan.
- [x] **Tugas Mandiri (F):** Berkas `p02_kebutuhan_data_25430100.md` untuk tema Toko Daring (`toko_100`) dikerjakan menggunakan parameter $P=1$.
- [x] **Pembahasan dan Kendala:** Menganalisis kendala pemisahan data historis dan turunan.
- [x] **Kesimpulan:** Kesimpulan ditarik secara ringkas dan jelas dalam 4 poin.
- [x] **Pernyataan AI:** Mencantumkan batasan penggunaan asisten AI.
- [x] **Bukti Git:** Tautan repositori, hash commit, dan format pesan commit `p02: ...` tercantum.