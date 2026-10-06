# Dokumen Kebutuhan Data - Kopma 100

## 1. Latar Belakang dan Aktivitas Organisasi
Kopma 100 (Koperasi Mahasiswa 100) merupakan unit kegiatan usaha mahasiswa yang bergerak di bidang pelayanan kebutuhan civitas akademika, pengelolaan unit toko unit/kredit, simpan-pinjam anggota, serta pembagian Sisa Hasil Usaha (SHU). Aktivitas operasional utamanya meliputi pendaftaran anggota baru, pengelolaan katalog produk toko dan pasokan stok, pemrosesan transaksi penjualan/kredit anggota, pemrosesan angsuran serta denda keterlambatan, hingga penyusunan laporan keuangan harian dan bulanan.

## 2. Aktor dan Proses Bisnis

**Daftar Aktor**
* **Anggota Kopma**: Mahasiswa terdaftar yang memiliki hak simpanan, diskon khusus anggota, serta fasilitas kredit transaksi.
* **Kasir / Staf Operasional**: Staf yang memproses transaksi penjualan, pembayaran angsuran, denda, dan pencetakan kuitansi.
* **Staf Gudang / Inventory**: Staf yang mengelola pendataan produk, kategori barang, dan pasokan stok toko Kopma.
* **Pengurus / Manajer Kopma**: Penanggung jawab tata kelola operasional dan penerima laporan transaksi/keuangan.

**Tabel Process Business (PB)**

| Kode | Proses Bisnis | Aktor Utama | Pemicu (*Trigger*) |
|---|---|---|---|
| PB-01 | Mendaftarkan Anggota Baru | Kasir / Staf Operasional | Mahasiswa menyerahkan formulir pendaftaran anggota Kopma |
| PB-02 | Memproses Transaksi Belanja / Kredit | Kasir / Staf Operasional | Anggota/pelanggan membawa barang ke kasir toko |
| PB-03 | Memproses Pelunasan & Denda | Kasir / Staf Operasional | Anggota melakukan pembayaran angsuran atau denda keterlambatan |
| PB-04 | Mengelola Katalog & Stok Barang | Staf Gudang / Inventory | Penerimaan pasokan barang baru dari pemasok (*supplier*) |
| PB-05 | Menyusun Laporan Keuangan & SHU | Pengurus / Manajer Kopma | Pembukuan rutin pada akhir bulan atau akhir periode |

## 3. Dokumen Sumber yang Dianalisis
* **Formulir Pendaftaran Anggota**: Mengandung rincian identitas pemohon seperti NIM, nama lengkap, program studi, nomor HP, dan surel.
* **Nota Transaksi Penjualan (*Receipt*)**: Bukti transaksi belanja yang mencantumkan nomor transaksi, tanggal transaksi, status keanggotaan, identitas kasir, daftar produk yang dibeli, dan total bayar.
* **Kuitansi Angsuran & Denda Keterlambatan**: Bukti pembayaran tagihan yang mencatat nomor kuitansi, jumlah hari keterlambatan, nominal denda harian, total pembayaran, dan nama staf penerima.

## 4. Entitas Kandidat dan Elemen Data
* **Anggota**: `id_anggota`, `nim_anggota`, `nama_anggota`, `prodi_anggota`, `no_hp_anggota`, `email_anggota`, `status_anggota`.
* **Staf**: `id_staf`, `nama_staf`, `peran_staf`, `status_staf`.
* **Kategori**: `id_kategori`, `nama_kategori`, `deskripsi_kategori`.
* **Produk**: `id_produk`, `id_kategori`, `sku_produk`, `nama_produk`, `harga_umum`, `harga_anggota`, `stok_produk`.
* **Transaksi**: `id_transaksi`, `no_transaksi`, `tgl_transaksi`, `tgl_tenggat_bayar`, `id_anggota`, `id_staf`, `status_transaksi`.
* **Detail Transaksi**: `id_detail_transaksi`, `id_transaksi`, `id_produk`, `jumlah_barang`, `subtotal_harga`, `denda_keterlambatan`.
* **Pembayaran**: `id_pembayaran`, `no_kuitansi_pembayaran`, `id_detail_transaksi`, `tgl_bayar`, `jumlah_bayar`, `id_staf`.

## 5. Aturan Bisnis

| Kode | Aturan Bisnis |
|---|---|
| AB-01 | Setiap transaksi pembelian/kredit memiliki nomor unik dan melayani maksimal 3 jenis item barang per transaksi. |
| AB-02 | Anggota yang memiliki tunggakan angsuran atau denda yang belum lunas dilarang melakukan transaksi kredit baru. |
| AB-03 | Batas waktu standar jatuh tempo pembayaran transaksi kredit anggota adalah 30 hari kalender. |
| AB-04 | Denda keterlambatan pembayaran kredit ditetapkan sebesar Rp1.000 per hari keterlambatan untuk tiap transaksi. |
| AB-05 | Satu kategori barang dapat mencakup banyak produk unik dengan Stock Keeping Unit (SKU) masing-masing. |
| AB-06 | Nomor Induk Mahasiswa (NIM) bersifat unik dan tidak boleh terdaftar ganda sebagai anggota Kopma. |
| AB-07 | Pencatatan transaksi belanja secara otomatis mengurangi jumlah stok produk di sistem. |
| AB-08 | Pelunasan denda atau angsuran wajib menerbitkan kuitansi resmi yang terhubung ke transaksi dan staf penerima. |

## 6. Kebutuhan Informasi

| Kode | Kebutuhan Informasi | Elemen Data yang Dibutuhkan |
|---|---|---|
| KI-01 | Rekapitulasi volume dan nominal transaksi harian serta bulanan | `Transaksi`, `Detail Transaksi` |
| KI-02 | Daftar 5 produk unit toko Kopma paling terlaris per bulan | `Detail Transaksi`, `Produk` |
| KI-03 | Daftar anggota yang terlewat dari tanggal jatuh tempo pembayaran kredit | `Transaksi`, `Detail Transaksi`, `Anggota` |
| KI-04 | Laporan penerimaan kas pembayaran angsuran/denda harian per staf kasir | `Pembayaran`, `Staf` |
| KI-05 | Daftar 10 anggota paling aktif bertransaksi untuk penentuan alokasi SHU | `Transaksi`, `Anggota` |

## 7. Matriks CRUD

| Proses Bisnis | Anggota | Staf | Produk | Transaksi | Detail Transaksi | Pembayaran |
|---|---|---|---|---|---|---|
| PB-01 Mendaftarkan Anggota | C | R | - | - | - | - |
| PB-02 Memproses Transaksi Belanja | R | R | R, U | C | C | - |
| PB-03 Memproses Pelunasan & Denda | - | R | - | U | U | C |
| PB-04 Mengelola Katalog & Stok | - | R | C, R, U | - | - | - |
| PB-05 Menyusun Laporan Keuangan | R | R | R | R | R | R |

*(Keterangan: C = Create, R = Read, U = Update, D = Delete)*

## 8. Kamus Data Awal

| Elemen Data | Deskripsi / Arti | Contoh Nilai | Aturan / Batasan | Penanggung Jawab (*Data Steward*) |
|---|---|---|---|---|
| `id_anggota` | ID unik internal anggota Kopma | 1 | Kunci Utama, Penambahan Otomatis | Pengurus Kopma |
| `nim_anggota` | Nomor Induk Mahasiswa | 25430100 | Unik, 8-10 digit angka | Pengurus Kopma |
| `nama_anggota` | Nama lengkap anggota | Ahmad Dahlan | Wajib diisi, maks 100 karakter | Pengurus Kopma |
| `prodi_anggota` | Program studi anggota | Ilmu Komputer | Wajib diisi | Pengurus Kopma |
| `no_hp_anggota` | Nomor ponsel aktif | 081234567890 | Data pribadi, 10-13 digit | Pengurus Kopma |
| `email_anggota` | Alamat surel resmi | ahmad@ummetro.ac.id | Format surel valid, unik | Pengurus Kopma |
| `status_anggota` | Status keaktifan anggota | Aktif | Nilai: Aktif / Nonaktif | Pengurus Kopma |
| `id_staf` | ID unik staf/pengurus Kopma | 101 | Kunci Utama, Penambahan Otomatis | Pengurus Kopma |
| `nama_staf` | Nama lengkap staf | Siti Rahma | Wajib diisi | Pengurus Kopma |
| `id_produk` | ID unik entri barang toko | 501 | Kunci Utama, Penambahan Otomatis | Staf Gudang |
| `sku_produk` | Kode Unik Stok Barang | KPM-100-001 | Unik, 10-15 karakter | Staf Gudang |
| `nama_produk` | Nama produk toko Kopma | Buku Tulis A5 | Wajib diisi | Staf Gudang |
| `harga_anggota` | Harga khusus anggota Kopma | 4500 | Bilangan bulat $\ge0$ | Staf Gudang |
| `stok_produk` | Jumlah stok di toko/gudang | 100 | Bilangan bulat $\ge0$ | Staf Gudang |
| `no_transaksi` | Nomor nota transaksi | TR-2610-0001 | Unik, format TR-YYMM-XXXX | Kasir |
| `tgl_transaksi` | Tanggal transaksi | 2026-10-06 | Wajib diisi, format tanggal | Kasir |
| `denda_keterlambatan` | Denda keterlambatan bayar | 1000 | Bilangan bulat $\ge0$, Rp1.000/hari | Kasir |
| `no_kuitansi_pembayaran` | Nomor kuitansi pelunasan | KW-2610-0012 | Unik, format KW-YYMM-XXXX | Kasir |

## 9. Kebutuhan Non-Fungsional Data

**Perhitungan Parameter Personal berbasis NIM**
* NIM: `25430100` (dua digit terakhir = `00`)
* Parameter $P = (00 \bmod 9) + 1 = 0 + 1 = 1$
* Batas maksimal item jenis barang per transaksi $= P + 2 = 1 + 2 = 3$ item
* Tarif denda harian keterlambatan $= P \times \text{Rp1.000} = 1 \times \text{Rp1.000} = \text{Rp1.000}$ / hari
* Proyeksi volume transaksi harian $= 40 + (5 \times 1) = 45$ transaksi / hari

**Spesifikasi Kebutuhan Non-Fungsional**
* **Volume Data**: Estimasi pertumbuhan transaksi sebesar 45 transaksi per hari atau $\approx 13.500$ transaksi per tahun.
* **Masa Retensi Data**: Rekam transaksi penjualan, kredit, dan kuitansi pembayaran disimpan sekurang-kurangnya 5 tahun untuk kebutuhan audit keuangan internal dan pembagian SHU.
* **Privasi Data**: Informasi kontak pribadi (`no_hp_anggota` dan `email_anggota`) diklasifikasikan sebagai data tertutup, di mana akses pembacaan hanya diberikan kepada Kasir dan Pengurus Kopma.

## 10. Isu Kualitas Data yang Diantisipasi

| Dimensi Kualitas Data | Isu yang Berpotensi Terjadi | Tindakan Pencegahan / Antisipasi |
|---|---|---|
| Akurasi & Keunikan | Registrasi NIM ganda atau kesalahan ketik nomor identitas anggota | Penerapan batasan `UNIQUE` pada kolom `nim_anggota` dan validasi digit angka. |
| Konsistensi Data | Redundansi entri produk atau variasi harga merusak catatan lama | Harga jual disimpan secara eksplisit pada entitas detail transaksi saat peristiwa pembelian terjadi. |
| Kelengkapan Data | Lupa mencatat tanggal pelunasan angsuran pada sistem | Pembaruan otomatis status `status_transaksi` secara sinkron saat konfirmasi pelunasan diinput. |
| Akurasi Historis | Perubahan tarif denda dasar merusak perhitungan akumulasi laporan lama | Nominal denda disimpan secara eksplisit pada entitas detail transaksi/pembayaran saat peristiwa pelunasan terjadi. |