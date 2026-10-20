# Dokumentasi Normalisasi dan Skema Relasional — Toko Daring (`toko_100`)

**Nama:** Sherly Adi Ramadani  
**NIM:** 25430100  
**Kelas:** D  
**Tema Proyek:** Toko Daring (`toko_100`)  
**Parameter P:** 1 *(Perhitungan: $(00 \bmod 9) + 1 = 1$)*

---

## 1. Dokumen Sumber (UNF / Unnormalized Form)

Titik awal analisis diambil dari faktur/nota pesanan (*Invoice*) pada sistem Toko Daring. Pada bentuk **UNF**, data masih berisi kelompok berulang (*repeating group*) untuk barang/produk yang dibeli dalam satu nomor pesanan.

### Notasi Relasi UNF
```
PESANAN_UNF (
    id_pesanan, tgl_pesanan, status_pesanan, total_bayar,
    id_pelanggan, nama_lengkap, email, no_telepon, alamat,
    { kode_produk, nama_produk, harga_satuan, stok, jumlah, harga_transaksi }
)
```

### Gambaran Tabel Data UNF (Unnormalized)
| id_pesanan | tgl_pesanan | status_pesanan | total_bayar | id_pelanggan | nama_lengkap | email | no_telepon | alamat | kode_produk | nama_produk | harga_satuan | stok | jumlah | harga_transaksi |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ORD-001** | 2026-10-15 | Selesai | 350000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 | PRD-01<br>PRD-02 | Mouse Wireless<br>Keyboard Mech | 100000<br>250000 | 15<br>8 | 1<br>1 | 100000<br>250000 |
| **ORD-002** | 2026-10-16 | Diproses | 200000 | CUST-102 | Siti Rahma | siti@yahoo.com | 082198765432 | Jl. Mawar No. 12 | PRD-01 | Mouse Wireless | 100000 | 15 | 2 | 100000 |
| **ORD-003** | 2026-10-16 | Selesai | 250000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 | PRD-02 | Keyboard Mech | 250000 | 8 | 1 | 250000 |

---

## 2. Aturan Bisnis & Dependensi Fungsional (Functional Dependency)

### Aturan Bisnis (AB)
1. **AB-01:** Satu pesanan diidentifikasi secara unik oleh `id_pesanan`, dibuat oleh tepat satu pelanggan, dan dapat mencakup satu atau banyak produk.
2. **AB-02:** Satu pelanggan diidentifikasi secara unik oleh `id_pelanggan` dan dapat membuat banyak pesanan dari waktu ke waktu.
3. **AB-03:** `status_pesanan` mencatat alur transaksi (*Pending*, *Diproses*, *Dikirim*, *Selesai*, *Batal*).
4. **AB-04:** Setiap produk diidentifikasi oleh `kode_produk` dan memiliki harga master (`harga_satuan`) serta jumlah stok.
5. **AB-05:** `harga_transaksi` pada rincian pesanan mencatat harga aktual produk pada saat transaksi dilakukan. Nilai ini bersifat historis agar tidak terpengaruh oleh perubahan harga master produk di masa mendatang.

### Pemetaan Functional Dependency (FD)
- **FD1:** `id_pesanan` $\rightarrow$ `tgl_pesanan`, `status_pesanan`, `total_bayar`, `id_pelanggan`
- **FD2:** `id_pelanggan` $\rightarrow$ `nama_lengkap`, `email`, `no_telepon`, `alamat`
- **FD3:** `kode_produk` $\rightarrow$ `nama_produk`, `harga_satuan`, `stok`
- **FD4:** (`id_pesanan`, `kode_produk`) $\rightarrow$ `jumlah`, `harga_transaksi`

---

## 3. Tahapan Normalisasi

### A. Bentuk Normal Pertama (1NF)
**Syarat 1NF:** Menghilangkan *repeating group* (kelompok berulang). Setiap atribut bernilai atomik (tunggal), dan dibuat Primary Key komposit `(id_pesanan, kode_produk)` untuk mengidentifikasi setiap baris detail transaksi secara unik.

#### Notasi Skema 1NF
`PESANAN_1NF` (**id_pesanan**, **kode_produk**, tgl_pesanan, status_pesanan, total_bayar, id_pelanggan, nama_lengkap, email, no_telepon, alamat, nama_produk, harga_satuan, stok, jumlah, harga_transaksi)

#### Gambaran Tabel Data 1NF
| id_pesanan | kode_produk | tgl_pesanan | status_pesanan | total_bayar | id_pelanggan | nama_lengkap | email | no_telepon | alamat | nama_produk | harga_satuan | stok | jumlah | harga_transaksi |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ORD-001** | **PRD-01** | 2026-10-15 | Selesai | 350000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 | Mouse Wireless | 100000 | 15 | 1 | 100000 |
| **ORD-001** | **PRD-02** | 2026-10-15 | Selesai | 350000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 | Keyboard Mech | 250000 | 8 | 1 | 250000 |
| **ORD-002** | **PRD-01** | 2026-10-16 | Diproses | 200000 | CUST-102 | Siti Rahma | siti@yahoo.com | 082198765432 | Jl. Mawar No. 12 | Mouse Wireless | 100000 | 15 | 2 | 100000 |
| **ORD-003** | **PRD-02** | 2026-10-16 | Selesai | 250000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 | Keyboard Mech | 250000 | 8 | 1 | 250000 |

*Masalah pada 1NF:* Terdapat redundansi data (misal: data pelanggan & pesanan terulang di setiap baris item produk) serta ketergantungan parsial (*Partial Dependency*).

---

### B. Bentuk Normal Kedua (2NF)
**Syarat 2NF:** Memenuhi 1NF dan **menghilangkan Ketergantungan Parsial (*Partial Dependency*)**. Atribut bukan kunci harus bergantung penuh pada seluruh Primary Key komposit, bukan hanya sebagian dari Primary Key.

#### Analisis Pemisahan 2NF:
1. `(id_pesanan, kode_produk) -> jumlah, harga_transaksi` $\rightarrow$ Bergantung Penuh (**DETAIL_PESANAN**)
2. `id_pesanan -> tgl_pesanan, status_pesanan, total_bayar, id_pelanggan, nama_lengkap, email, no_telepon, alamat` $\rightarrow$ Bergantung Parsial pada `id_pesanan` (**PESANAN_2NF**)
3. `kode_produk -> nama_produk, harga_satuan, stok` $\rightarrow$ Bergantung Parsial pada `kode_produk` (**PRODUK**)

#### Notasi Skema 2NF
1. `PESANAN_2NF` (**id_pesanan**, tgl_pesanan, status_pesanan, total_bayar, id_pelanggan, nama_lengkap, email, no_telepon, alamat)
2. `PRODUK` (**kode_produk**, nama_produk, harga_satuan, stok)
3. `DETAIL_PESANAN` (**id_pesanan***, **kode_produk***, jumlah, harga_transaksi)

#### Gambaran Tabel Data 2NF

**Tabel `PESANAN_2NF`**
| id_pesanan | tgl_pesanan | status_pesanan | total_bayar | id_pelanggan | nama_lengkap | email | no_telepon | alamat |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ORD-001** | 2026-10-15 | Selesai | 350000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 |
| **ORD-002** | 2026-10-16 | Diproses | 200000 | CUST-102 | Siti Rahma | siti@yahoo.com | 082198765432 | Jl. Mawar No. 12 |
| **ORD-003** | 2026-10-16 | Selesai | 250000 | CUST-101 | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 |

**Tabel `PRODUK`**
| kode_produk | nama_produk | harga_satuan | stok |
| :--- | :--- | :--- | :--- |
| **PRD-01** | Mouse Wireless | 100000 | 15 |
| **PRD-02** | Keyboard Mech | 250000 | 8 |

**Tabel `DETAIL_PESANAN`**
| id_pesanan* | kode_produk* | jumlah | harga_transaksi |
| :--- | :--- | :--- | :--- |
| **ORD-001** | **PRD-01** | 1 | 100000 |
| **ORD-001** | **PRD-02** | 1 | 250000 |
| **ORD-002** | **PRD-01** | 2 | 100000 |
| **ORD-003** | **PRD-02** | 1 | 250000 |

*Masalah pada 2NF:* Pada tabel `PESANAN_2NF`, terdapat ketergantungan transitif (*Transitive Dependency*), yaitu: `id_pesanan` $\rightarrow$ `id_pelanggan` $\rightarrow$ (`nama_lengkap`, `email`, `no_telepon`, `alamat`).

---

### C. Bentuk Normal Ketiga (3NF)
**Syarat 3NF:** Memenuhi 2NF dan **menghilangkan Ketergantungan Transitif (*Transitive Dependency*)**. Atribut sanak/non-kunci tidak boleh bergantung pada atribut non-kunci lainnya.

#### Pemisahan 3NF:
Memisahkan entitas **PELANGGAN** dari tabel **PESANAN**, sehingga data pelanggan disimpan mandiri dan dirujuk oleh `id_pelanggan` sebagai Foreign Key (FK).

#### Notasi Skema 3NF
1. `PELANGGAN` (**id_pelanggan**, nama_lengkap, email, no_telepon, alamat)
2. `PESANAN` (**id_pesanan**, tgl_pesanan, status_pesanan, total_bayar, *id_pelanggan*)
3. `PRODUK` (**kode_produk**, nama_produk, harga_satuan, stok)
4. `DETAIL_PESANAN` (**id_pesanan***, **kode_produk***, jumlah, harga_transaksi)

#### Gambaran Tabel Data 3NF (Final Design)

**1. Tabel `PELANGGAN`**
| id_pelanggan | nama_lengkap | email | no_telepon | alamat |
| :--- | :--- | :--- | :--- | :--- |
| **CUST-101** | Budi Santoso | budi@gmail.com | 081234567890 | Jl. Melati No. 5 |
| **CUST-102** | Siti Rahma | siti@yahoo.com | 082198765432 | Jl. Mawar No. 12 |

**2. Tabel `PESANAN`**
| id_pesanan | tgl_pesanan | status_pesanan | total_bayar | id_pelanggan* |
| :--- | :--- | :--- | :--- | :--- |
| **ORD-001** | 2026-10-15 | Selesai | 350000 | CUST-101 |
| **ORD-002** | 2026-10-16 | Diproses | 200000 | CUST-102 |
| **ORD-003** | 2026-10-16 | Selesai | 250000 | CUST-101 |

**3. Tabel `PRODUK`**
| kode_produk | nama_produk | harga_satuan | stok |
| :--- | :--- | :--- | :--- |
| **PRD-01** | Mouse Wireless | 100000 | 15 |
| **PRD-02** | Keyboard Mech | 250000 | 8 |

**4. Tabel `DETAIL_PESANAN`**
| id_pesanan* | kode_produk* | jumlah | harga_transaksi |
| :--- | :--- | :--- | :--- |
| **ORD-001** | **PRD-01** | 1 | 100000 |
| **ORD-001** | **PRD-02** | 1 | 250000 |
| **ORD-002** | **PRD-01** | 2 | 100000 |
| **ORD-003** | **PRD-02** | 1 | 250000 |

---

## 4. Analisis BCNF & Keputusan Nilai Turunan

### Analisis Boyce-Codd Normal Form (BCNF)
Skema dikatakan memenuhi **BCNF** jika dan hanya jika untuk setiap ketergantungan fungsional $X \rightarrow Y$, maka $X$ adalah **Super Key** / **Candidate Key**.

- Pada `PELANGGAN`: Determinan `id_pelanggan` adalah Primary Key.
- Pada `PESANAN`: Determinan `id_pesanan` adalah Primary Key.
- Pada `PRODUK`: Determinan `kode_produk` adalah Primary Key.
- Pada `DETAIL_PESANAN`: Determinan `(id_pesanan, kode_produk)` adalah Composite Primary Key.

**Kesimpulan:** Seluruh determinan dalam skema ini merupakan kunci kandidat utama. Oleh karena itu, skema database **telah memenuhi standar BCNF**.

### Keputusan Atribut Turunan (*Derived Attribute*)
1. **Atribut `harga_transaksi` (Disimpan Permanen):**
   - Merupakan harga per unit pada saat transaksi dicatat (historis).
   - *Keputusan:* Harus disimpan secara fisik di tabel `DETAIL_PESANAN` untuk menghindari inkonsistensi data transaksi masa lalu apabila harga master di tabel `PRODUK` dinaikkan atau diturunkan di kemudian hari (AB-05).
2. **Atribut `total_bayar` & Subtotal (Dihitung Dinamis / Virtual):**
   - Subtotal per item dihitung secara langsung: $\text{jumlah} \times \text{harga\_transaksi}$.
   - Total Keseluruhan Pesanan dihitung melalui agregasi `SUM(jumlah * harga_transaksi)`.
   - *Keputusan:* Di dalam DDL database, `total_bayar` direkomendasikan dihitung menggunakan **SQL View** atau fungsi agregat guna mencegah anomali pembaruan (*Update Anomaly*).

---

## 5. Kamus Data (Data Dictionary)

### 1. Tabel `pelanggan`
| Nama Kolom | Tipe Data | Kunci | Keterangan |
| :--- | :--- | :--- | :--- |
| `id_pelanggan` | `VARCHAR(10)` | **PK** | Kode unik pelanggan (Contoh: 'CUST-101') |
| `nama_lengkap` | `VARCHAR(100)` | - | Nama lengkap pelanggan |
| `email` | `VARCHAR(100)` | `UNIQUE` | Email aktif pelanggan |
| `no_telepon` | `VARCHAR(15)` | - | Nomor kontak WhatsApp/Telepon |
| `alamat` | `TEXT` | - | Alamat pengiriman lengkap |

### 2. Tabel `produk`
| Nama Kolom | Tipe Data | Kunci | Keterangan |
| :--- | :--- | :--- | :--- |
| `kode_produk` | `VARCHAR(10)` | **PK** | Kode unik produk (Contoh: 'PRD-01') |
| `nama_produk` | `VARCHAR(100)` | - | Nama barang/produk |
| `harga_satuan` | `DECIMAL(12,2)` | - | Harga master produk saat ini |
| `stok` | `INT` | - | Sisa jumlah stok barang |

### 3. Tabel `pesanan`
| Nama Kolom | Tipe Data | Kunci | Keterangan |
| :--- | :--- | :--- | :--- |
| `id_pesanan` | `VARCHAR(15)` | **PK** | Nomor nota/pesanan (Contoh: 'ORD-001') |
| `tgl_pesanan` | `DATE` | - | Tanggal pembuatan pesanan |
| `status_pesanan`| `ENUM(...)` | - | Status: 'Pending','Diproses','Dikirim','Selesai','Batal' |
| `id_pelanggan` | `VARCHAR(10)` | **FK** | Merujuk ke `pelanggan(id_pelanggan)` |

### 4. Tabel `detail_pesanan`
| Nama Kolom | Tipe Data | Kunci | Keterangan |
| :--- | :--- | :--- | :--- |
| `id_pesanan` | `VARCHAR(15)` | **PK, FK** | Merujuk ke `pesanan(id_pesanan)` |
| `kode_produk` | `VARCHAR(10)` | **PK, FK** | Merujuk ke `produk(kode_produk)` |
| `jumlah` | `INT` | - | Kuantitas barang yang dipesan |
| `harga_transaksi`| `DECIMAL(12,2)`| - | Harga acuan historis barang saat dipesan |

---

## 6. Implementasi DDL SQL (`toko_100`)

```sql
-- Create Database
CREATE DATABASE IF NOT EXISTS toko_100;
USE toko_100;

-- 1. Tabel Pelanggan
CREATE TABLE pelanggan (
    id_pelanggan VARCHAR(10) NOT NULL,
    nama_lengkap VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    no_telepon VARCHAR(15),
    alamat TEXT,
    PRIMARY KEY (id_pelanggan)
) ENGINE=InnoDB;

-- 2. Tabel Produk
CREATE TABLE produk (
    kode_produk VARCHAR(10) NOT NULL,
    nama_produk VARCHAR(100) NOT NULL,
    harga_satuan DECIMAL(12, 2) NOT NULL DEFAULT 0.00,
    stok INT NOT NULL DEFAULT 0,
    PRIMARY KEY (kode_produk)
) ENGINE=InnoDB;

-- 3. Tabel Pesanan
CREATE TABLE pesanan (
    id_pesanan VARCHAR(15) NOT NULL,
    tgl_pesanan DATE NOT NULL,
    status_pesanan ENUM('Pending', 'Diproses', 'Dikirim', 'Selesai', 'Batal') NOT NULL DEFAULT 'Pending',
    id_pelanggan VARCHAR(10) NOT NULL,
    PRIMARY KEY (id_pesanan),
    CONSTRAINT fk_pesanan_pelanggan 
        FOREIGN KEY (id_pelanggan) REFERENCES pelanggan(id_pelanggan)
        ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

-- 4. Tabel Detail Pesanan
CREATE TABLE detail_pesanan (
    id_pesanan VARCHAR(15) NOT NULL,
    kode_produk VARCHAR(10) NOT NULL,
    jumlah INT NOT NULL DEFAULT 1,
    harga_transaksi DECIMAL(12, 2) NOT NULL,
    PRIMARY KEY (id_pesanan, kode_produk),
    CONSTRAINT fk_detail_pesanan 
        FOREIGN KEY (id_pesanan) REFERENCES pesanan(id_pesanan)
        ON UPDATE CASCADE ON DELETE CASCADE,
    CONSTRAINT fk_detail_produk 
        FOREIGN KEY (kode_produk) REFERENCES produk(kode_produk)
        ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

-- 5. View Agregasi Perhitungan Total Bayar Dinamis
CREATE VIEW v_rekap_pesanan AS
SELECT 
    p.id_pesanan,
    p.tgl_pesanan,
    p.status_pesanan,
    c.id_pelanggan,
    c.nama_lengkap,
    SUM(dp.jumlah * dp.harga_transaksi) AS total_bayar
FROM pesanan p
JOIN pelanggan c ON p.id_pelanggan = c.id_pelanggan
JOIN detail_pesanan dp ON p.id_pesanan = dp.id_pesanan
GROUP BY p.id_pesanan, p.tgl_pesanan, p.status_pesanan, c.id_pelanggan, c.nama_lengkap;
```