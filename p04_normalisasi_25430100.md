# Laporan Praktikum Sistem Basis Data
## Milestone Proyek 4: Normalisasi dan Skema Relasional - Toko Daring (`toko_100`)

### Identitas Praktikum
* **NIM:** 25430100  
* **Nama:** Sherly Adi Ramadani  
* **Kelas:** D  
* **Tema Proyek:** Toko Daring (`toko_100`)  
* **Parameter P:** 1 *(Perhitungan: (00 mod 9) + 1)*

---

## 1. Aturan Bisnis (*Business Rules*) Proyek `toko_100`

Sebelum masuk ke tahap normalisasi, berikut adalah aturan bisnis dasar yang mendasari pembentukan Dependensi Fungsional (FD) pada sistem Toko Daring:

| Kode Aturan | Deskripsi Aturan Bisnis |
| :--- | :--- |
| **AB-01** | Setiap pesanan dilakukan oleh tepat satu pelanggan, dan satu pelanggan dapat melakukan banyak pesanan. |
| **AB-02** | Setiap pesanan memiliki total pembayaran (`total_bayar`) yang dihitung dari jumlah produk dan harga transaksi setelah dikurangi diskon 1% (sesuai parameter P). |
| **AB-03** | Status pesanan meliputi: `Menunggu Pembayaran`, `Diproses`, `Dikirim`, `Selesai`, dan `Dibatalkan`. |
| **AB-04** | Katalog produk mencatat informasi detail produk termasuk nama, harga satuan terkini, dan stok gudang. |
| **AB-05** | Setiap pesanan dapat memuat banyak item produk (melalui tabel relasi detail pesanan) dengan mencatat jumlah beli dan harga transaksi historis (*historical price*). |

---

## 2. Dokumen Sumber (UNF - *Unnormalized Form*)

Dokumen sumber diturunkan dari rancangan formulir/nota *checkout* transaksi pada platform *e-commerce* `toko_100` yang masih mengandung kelompok berulang (*repeating groups*).

```text
PESANAN_UNF (
    id_pesanan, tgl_pesanan, status_pesanan, total_bayar,
    id_pelanggan, nama_lengkap, email, no_telepon, alamat,
    { kode_produk, nama_produk, harga_satuan, jumlah, harga_transaksi }
)
```

---

## 3. Tahapan Normalisasi

### 3.1 Bentuk Normal Pertama (1NF)
* **Tujuan:** Menghilangkan kelompok berulang (*repeating groups*) `{ ... }` sehingga seluruh atribut bersifat atomik (tunggal) dan membentuk kunci primer komposit.
* **Skema 1NF:** `PESANAN_1NF` (`id_pesanan`, `kode_produk`, `tgl_pesanan`, `status_pesanan`, `total_bayar`, `id_pelanggan`, `nama_lengkap`, `email`, `no_telepon`, `alamat`, `nama_produk`, `harga_satuan`, `jumlah`, `harga_transaksi`)
* **Primary Key:** (`id_pesanan`, `kode_produk`)

### 3.2 Bentuk Normal Kedua (2NF)
* **Tujuan:** Menghilangkan ketergantungan parsial (*partial dependency*), di mana atribut bukan kunci hanya bergantung pada sebagian dari kunci primer komposit.
* **Dekomposisi:**
  1. `PESANAN` ($\underline{\text{id\_pesanan}}$, `tgl_pesanan`, `status_pesanan`, `total_bayar`, `id_pelanggan`, `nama_lengkap`, `email`, `no_telepon`, `alamat`) — bergantung penuh pada `id_pesanan`.
  2. `PRODUK` ($\underline{\text{kode\_produk}}$, `nama_produk`, `harga_satuan`, `stok`) — bergantung penuh pada `kode_produk`.
  3. `DETAIL_PESANAN` ($\underline{\text{id\_pesanan}}, \underline{\text{kode\_produk}}$, `jumlah`, `harga_transaksi`) — bergantung pada gabungan *primary key* komposit.

### 3.3 Bentuk Normal Ketiga (3NF)
* **Tujuan:** Menghilangkan ketergantungan transitif (*transitive dependency*), di mana atribut bukan kunci bergantung pada atribut bukan kunci lainnya (`id_pelanggan` $\rightarrow$ atribut profil pelanggan).
* **Dekomposisi Akhir (3NF):**
  1. **`PELANGGAN`**: (`id_pelanggan` [PK], `nama_lengkap`, `email`, `no_telepon`, `alamat`)
  2. **`PESANAN`**: (`id_pesanan` [PK], `tgl_pesanan`, `status_pesanan`, `total_bayar`, `id_pelanggan` [FK])
  3. **`PRODUK`**: (`kode_produk` [PK], `nama_produk`, `harga_satuan`, `stok`)
  4. **`DETAIL_PESANAN`**: (`id_pesanan` [PK, FK], `kode_produk` [PK, FK], `jumlah`, `harga_transaksi`)

---

## 4. Evaluasi BCNF dan Keputusan Nilai Turunan

### 4.1 Evaluasi BCNF (*Boyce-Codd Normal Form*)
Pemeriksaan determinan pada seluruh tabel hasil normalisasi 3NF:
* Pada tabel `PELANGGAN`: Determinan tunggal adalah `id_pelanggan` (*Candidate Key / Primary Key*).
* Pada tabel `PESANAN`: Determinan tunggal adalah `id_pesanan` (*Candidate Key / Primary Key*).
* Pada tabel `PRODUK`: Determinan tunggal adalah `kode_produk` (*Candidate Key / Primary Key*).
* Pada tabel `DETAIL_PESANAN`: Determinan adalah komposit (`id_pesanan`, `kode_produk`) (*Candidate Key / Primary Key*).

> **Kesimpulan:** Seluruh penentu (*determinant*) pada skema di atas merupakan *candidate key*. Oleh karena itu, skema relasional proyek `toko_100` telah **memenuhi kriteria BCNF**.

### 4.2 Analisis Atribut Turunan dan Nilai Historis
1. **`total_bayar` pada tabel `PESANAN`:**
   * **Keputusan:** Diimplementasikan sebagai *derived/calculated attribute* (atau dihitung dinamis via *view* / *stored procedure* / *query aggregate*) berdasarkan penjumlahan `jumlah * harga_transaksi` dikurangi diskon loyalitas 1% sesuai parameter P, guna menghindari anomali pembaruan data.
2. **`harga_transaksi` pada tabel `DETAIL_PESANAN`:**
   * **Keputusan:** **Wajib disimpan permanen**. Atribut ini berfungsi sebagai *historical price* (harga pada saat transaksi *checkout* dilakukan) agar laporan keuangan dan histori pesanan pelanggan tidak berubah meskipun harga master produk pada tabel `PRODUK` mengalami kenaikan atau penurunan di kemudian hari.

---

## 5. Ringkasan Skema Relasional Akhir (`toko_100`)

Berikut adalah ringkasan lengkap dari struktur tabel akhir yang telah dinormalisasi hingga bentuk BCNF untuk proyek Toko Daring (`toko_100`):

| No | Nama Tabel | Kolom & Atribut | Primary Key (PK) | Foreign Key (FK) |
| :---: | :--- | :--- | :--- | :--- |
| 1 | `PELANGGAN` | `id_pelanggan`, `nama_lengkap`, `email`, `no_telepon`, `alamat` | `id_pelanggan` | - |
| 2 | `PESANAN` | `id_pesanan`, `tgl_pesanan`, `status_pesanan`, `total_bayar`, `id_pelanggan` | `id_pesanan` | `id_pelanggan` $\rightarrow$ `PELANGGAN(id_pelanggan)` |
| 3 | `PRODUK` | `kode_produk`, `nama_produk`, `harga_satuan`, `stok` | `kode_produk` | - |
| 4 | `DETAIL_PESANAN` | `id_pesanan`, `kode_produk`, `jumlah`, `harga_transaksi` | (`id_pesanan`, `kode_produk`) | `id_pesanan` $\rightarrow$ `PESANAN(id_pesanan)`, <br>`kode_produk` $\rightarrow$ `PRODUK(kode_produk)` |

---

## 6. Pembahasan dan Catatan Implementasi Sistem

1. **Integritas Relasional:** Seluruh hubungan antar tabel diikat menggunakan *Foreign Key* yang merujuk secara akurat ke *Primary Key* tabel induknya, sehingga menjaga konsistensi data (*referential integrity*).
2. **Keterkaitan Transaksi:** Tabel `DETAIL_PESANAN` bertindak sebagai tabel asosiatif (*junction table*) untuk memecah relasi *many-to-many* antara `PESANAN` dan `PRODUK`.
3. **Keamanan Data Historis:** Penggunaan atribut `harga_transaksi` memastikan bahwa catatan harga pembelian produk tidak akan berubah meskipun harga katalog pada tabel `PRODUK` mengalami pembaruan di masa mendatang.

---

## 7. Panduan Pengerjaan dan Pengunggahan Repositori Git

Simpan berkas laporan dan dokumentasi ini ke dalam struktur direktori repositori lokal Anda menggunakan VS Code dengan struktur folder berikut:

```text
toko_100/
├── laporan/
│   └── p04_laporan_25430100.md
└── p04_normalisasi_toko_100.md
```

Gunakan perintah terminal Git berikut untuk melakukan *commit* dan *push* ke repositori GitHub Anda:

```bash
# 1. Cek status berkas
git status

# 2. Tambahkan seluruh berkas baru ke staging area
git add .

# 3. Lakukan commit dengan pesan standar modul 4
git commit -m "p04: menyelesaikan laporan praktikum dan normalisasi toko_100"

# 4. Unggah perubahan ke repositori remote (GitHub)
git push origin main
```