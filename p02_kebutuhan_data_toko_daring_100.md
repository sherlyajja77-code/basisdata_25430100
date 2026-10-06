# Milestone Proyek 2: Analisis Kebutuhan Data Toko Daring
**NIM:** 25430100  
**Nama Berkas:** p02_kebutuhan_data_toko_daring_100.md  

Parameter Proyek Mandiri
* **Nilai P:** 1
* **Maksimal Rincian Item per Transaksi:** 3 item
* **Persentase Diskon Pelanggan:** 1%
* **Estimasi Volume Transaksi Harian:** 45 transaksi


## 1. Latar Belakang dan Aktivitas Organisasi

### 1.1 Latar Belakang 
Toko Daring **`toko_100`** merupakan unit usaha ritel yang mengalihkan operasionalnya ke platform *e-commerce* untuk memperluas jangkauan pasar dan meningkatkan efisiensi. Penerapan basis data terintegrasi ini dirancang untuk mengatasi risiko pencatatan manual—seperti ketidaksesuaian stok, keterlambatan verifikasi pembayaran, dan ketidakakuratan riwayat transaksi—serta siap menangani estimasi beban operasional hingga **45 transaksi per hari**.

### 1.2 Aktivitas Organisasi
Operasional harian `toko_100` mencakup lima aktivitas utama:
* **Manajemen Pelanggan:** Pendaftaran akun baru dan pemberian diskon **1%** bagi pelanggan aktif.
* **Pengelolaan Katalog & Stok:** Pembaruan data produk serta pembaruan stok otomatis dengan validasi pencegahan stok minus.
* **Pemrosesan Pesanan:** Transaksi *checkout* dengan pembatasan maksimal **3 jenis item** per transaksi dan penguncian harga historis pada detail pesanan.
* **Verifikasi Pembayaran & Pengiriman:** Konfirmasi pembayaran oleh admin serta pembaruan status pesanan hingga tahap pengiriman.
* **Pelaporan Penjualan:** Pembuatan laporan omzet (harian/bulanan), daftar *top seller*, rekap belanja pelanggan, dan peringatan stok menipis.


## 2. Tabel Proses Bisnis Toko Daring
| Kode | Proses Bisnis | Aktor | Pemicu |
| :--- | :--- | :--- | :--- |
| PB-01 | Mendaftar Akun Pelanggan | Pelanggan | Registrasi akun baru pada aplikasi |
| PB-02 | Mengelola Katalog Produk | Admin / Penjual | Penambahan atau pembaruan stok & harga produk |
| PB-03 | Membuat Pesanan Daring | Pelanggan | Pelanggan melakukan *checkout* keranjang belanja |
| PB-04 | Memproses Pembayaran & Pengiriman | Admin | Konfirmasi pembayaran dari pelanggan |
| PB-05 | Menyusun Laporan Penjualan | Pemilik Toko | Rekapitulasi periode bulanan / mingguan |


## 3. Aturan Bisnis Utama Toko Daring
| Kode | Aturan Bisnis |
| :--- | :--- |
| AB-01 | Dalam 1 kali transaksi *checkout*, pelanggan hanya dapat membeli maksimal **3 jenis produk/item** berbeda. |
| AB-02 | Pelanggan terdaftar yang aktif berhak mendapatkan diskon sebesar **1%** dari total harga belanjaan. |
| AB-03 | Infrastruktur basis data dirancang untuk menangani kapasitas rata-rata **45 transaksi** per hari. |
| AB-04 | Stok produk berkurang secara otomatis saat pesanan dikonfirmasi dan ditolak jika stok kurang dari jumlah beli. |
| AB-05 | Harga produk pada detail pesanan dicatat secara permanen saat transaksi dibuat untuk menjaga riwayat keuangan. |


## 4. Kebutuhan Informasi
| Kode | Kebutuhan Informasi | Data yang Dibutuhkan |
| :--- | :--- | :--- |
| KI-01 | Laporan omzet penjualan harian dan bulanan | Pesanan, Detail Pesanan |
| KI-02 | Daftar produk paling laris (*top seller*) | Detail Pesanan, Produk |
| KI-03 | Peringatan stok produk yang hampir habis | Produk |
| KI-04 | Rekapitulasi total belanja per pelanggan | Pesanan, Pelanggan |


## 5. Matriks CRUD
| Proses | Pelanggan | Produk | Pesanan | Detail Pesanan | Pembayaran |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **PB-01** | C | - | - | - | - |
| **PB-02** | - | C, U | - | - | - |
| **PB-03** | R | R, U | C | C | C |
| **PB-04** | - | - | U | - | U |
| **PB-05** | R | R | R | R | R |


## 6. Kamus Data & Penanggung Jawab Data

### Tabel 1: `pelanggan`
* **Deskripsi:** Menyimpan informasi data diri pelanggan toko daring.
* **Penanggung Jawab Data (*Data Owner*):** Admin Layanan Pelanggan (*Customer Service*)

| Nama Atribut | Tipe Data | Keterangan |
| :--- | :--- | :--- |
| `id_pelanggan` | VARCHAR(10) | Primary Key |
| `nama_lengkap` | VARCHAR(50) | Nama pelanggan |
| `email` | VARCHAR(50) | Alamat email unik |
| `no_telepon` | VARCHAR(13) | Nomor kontak |
| `alamat` | TEXT | Alamat pengiriman |

### Tabel 2: `produk`
* **Deskripsi:** Menyimpan katalog barang yang dijual di toko daring.
* **Penanggung Jawab Data (*Data Owner*):** Petugas Gudang / Inventaris

| Nama Atribut | Tipe Data | Keterangan |
| :--- | :--- | :--- |
| `kode_produk` | VARCHAR(10) | Primary Key |
| `nama_produk` | VARCHAR(30) | Nama barang |
| `harga_satuan` | INT | Harga normal produk |
| `stok` | INT | Jumlah stok tersedia |

### Tabel 3: `pesanan`
* **Deskripsi:** Menyimpan data transaksi utama pesanan pelanggan.
* **Penanggung Jawab Data (*Data Owner*):** Manajer Keuangan / Operasional

| Nama Atribut | Tipe Data | Keterangan |
| :--- | :--- | :--- |
| `id_pesanan` | VARCHAR(20) | Primary Key |
| `id_pelanggan` | VARCHAR(10) | Foreign Key ke tabel `pelanggan` |
| `tgl_pesanan` | DATETIME | Tanggal dan waktu pesanan dibuat |
| `total_bayar` | INT | Total nilai setelah diskon 1% |
| `status_pesanan` | VARCHAR(36) | Contoh: *Pending*, *Lunas*, *Dikirim* |

### Tabel 4: `detail_pesanan`
* **Deskripsi:** Rincian barang yang dibeli dalam satu nomor pesanan.
* **Penanggung Jawab Data (*Data Owner*):** Manajer Keuangan / Operasional

| Nama Atribut | Tipe Data | Keterangan |
| :--- | :--- | :--- |
| `id_pesanan` | VARCHAR(20) | Composite Primary Key / Foreign Key |
| `kode_produk` | VARCHAR(10) | Composite Primary Key / Foreign Key |
| `jumlah` | INT | Jumlah unit (maksimal 3 item) |
| `harga_transaksi` | INT | Harga produk saat transaksi berlangsung |

