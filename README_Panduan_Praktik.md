# Panduan Praktik ERPNext - Manufaktur Furniture Kayu (PT Kezia Woodcraft)

Selamat datang di dataset praktik ERPNext! Dokumen ini dirancang khusus untuk pemula yang ingin memahami dan mempraktikkan alur kerja sistem ERP tipe industri manufaktur dari awal (pengadaan bahan baku) hingga akhir (penjualan produk jadi).

---

## 🏢 Profil Perusahaan Praktik
- **Nama Perusahaan**: PT Kezia Woodcraft Indonesia
- **Sektor Industri**: Manufaktur Furniture & Kerajinan Kayu
- **Produk Unggulan**: Meja Belajar Kayu Minimalis (`FG-MEJA-01`)
- **Mata Uang**: IDR (Rupiah)

---

## 📁 Struktur File Dataset di Folder `ERP_Kezia`

| Nama File | Deskripsi |
| :--- | :--- |
| `01_Warehouses.csv` | Data master gudang (Bahan Baku, WIP Pabrik, Barang Jadi) |
| `02_Item_Groups.csv` | Kategori barang (Bahan Baku, Aksesoris/Hardware, Barang Jadi) |
| `03_Items.csv` | Master data barang (Spesifikasi, Satuan/UOM, Harga Standar) |
| `04_Suppliers.csv` | Daftar pemasok kayu, besi, baut, dan cat |
| `05_Customers.csv` | Daftar pelanggan (Sekolah/Kantor, Toko Mebel, Perorangan) |
| `06_Workstations_Operations.csv` | Stasiun kerja pabrik & tahapan pengerjaan |
| `07_BOM_Formula.csv` | Resep Bill of Materials untuk membuat 1 unit meja |
| `08_Simulasi_Transaksi.csv` | Skenario urutan transaksi simulasi (13 langkah) |

---

## 🔄 Alur Siklus Manufaktur di ERPNext

```
[1. Pemasok/Vendor]
       │ (Purchase Order & Receipt)
       ▼
[Gudang Bahan Baku]
       │ (Material Transfer for Manufacture)
       ▼
[Gudang WIP (Lantai Produksi)] ── Work Order & Job Card (Potong -> Rakit -> Cat -> QC)
       │ (Manufacture Stock Entry)
       ▼
[Gudang Barang Jadi]
       │ (Delivery Note & Sales Invoice)
       ▼
[2. Pelanggan/Customer]
```

---

## 🛠️ Langkah-Langkah Praktik di ERPNext (Web UI: http://localhost:8080)

### TAHAP 1: Menyiapkan Master Data (Setup Master)

1. **Membuat Gudang (Warehouses)**:
   - Buka menu: **Stock > Warehouse > New**
   - Buat 3 gudang:
     - `Gudang Bahan Baku` (Type: Stores)
     - `Gudang WIP Produksi` (Type: Work In Progress)
     - `Gudang Barang Jadi` (Type: Finished Goods)

2. **Membuat Kategori Barang (Item Groups)**:
   - Buka menu: **Stock > Item Group > New**
   - Buat: `Bahan Baku (Raw Material)`, `Aksesoris & Hardware`, `Barang Jadi (Finished Goods)`.

3. **Membuat Master Barang (Items)**:
   - Buka menu: **Stock > Item > New**
   - Masukkan barang-barang dari file `03_Items.csv`:
     - `RM-KAYU-01` (Papan Kayu Mahoni 120x60) - *Maintain Stock: Yes, Is Purchase Item: Yes*
     - `RM-BESI-01` (Kaki Meja Besi Hollow) - *Maintain Stock: Yes, Is Purchase Item: Yes*
     - `RM-BAUT-01` (Baut & Sekrup M6) - *Maintain Stock: Yes, Is Purchase Item: Yes*
     - `RM-CAT-01` (Cat Pernis Clear Gloss) - *Maintain Stock: Yes, Is Purchase Item: Yes*
     - `FG-MEJA-01` (Meja Belajar Kayu Minimalis) - *Maintain Stock: Yes, Is Sales Item: Yes*

4. **Membuat Workstation & Operation (Opsional tapi Direkomendasikan)**:
   - Buka menu: **Manufacturing > Workstation > New** (buat sesuai `06_Workstations_Operations.csv`).
   - Buka menu: **Manufacturing > Operation > New** (buat operasi Potong, Rakit, Cat, QC).

5. **Membuat Bill of Materials (BOM / Resep Produksi)**:
   - Buka menu: **Manufacturing > Bill of Materials (BOM) > New**
   - **Item**: `FG-MEJA-01` (Quantity: 1)
   - Masukkan bahan baku di tabel **Items**:
     - `RM-KAYU-01` (Qty: 1)
     - `RM-BESI-01` (Qty: 1)
     - `RM-BAUT-01` (Qty: 0.1)
     - `RM-CAT-01` (Qty: 0.25)
   - Simpan (**Save**) dan klik **Submit**.

---

### TAHAP 2: Pengadaan Bahan Baku (Procurement / Buying)

1. **Membuat Supplier**:
   - Buka menu: **Buying > Supplier > New**, tambahkan supplier dari `04_Suppliers.csv`.
2. **Membuat Purchase Order (PO)**:
   - Buka menu: **Buying > Purchase Order > New**
   - Pesan bahan baku (misal: 20 pcs Kayu, 20 set Kaki Besi, 5 box Baut, 5 kaleng Cat).
   - Klik **Save** lalu **Submit**.
3. **Menerima Barang ke Gudang (Purchase Receipt)**:
   - Dari Purchase Order yang sudah di-submit, klik **Create > Purchase Receipt**.
   - Pastikan **Accepted Warehouse** mengarah ke `Gudang Bahan Baku`.
   - Klik **Submit**. *Sekarang stok bahan baku Anda sudah bertambah!*
4. **Mencatat Tagihan Pembelian (Purchase Invoice)**:
   - Klik **Create > Purchase Invoice**, klik **Submit**, lalu klik **Create > Payment** untuk mencatat pelunasan kas.

---

### TAHAP 3: Proses Manufaktur / Produksi (Manufacturing)

1. **Membuat Perintah Kerja (Work Order)**:
   - Buka menu: **Manufacturing > Work Order > New**
   - **Item to Produce**: `FG-MEJA-01`
   - **Qty to Produce**: `10`
   - **Target Warehouse**: `Gudang Barang Jadi`
   - **Source Warehouse**: `Gudang Bahan Baku`
   - **WIP Warehouse**: `Gudang WIP Produksi`
   - Klik **Save** dan klik **Submit**.
2. **Transfer Bahan Baku ke Lantai Produksi**:
   - Di dalam Work Order, klik tombol **Start**.
   - ERPNext akan membuat **Stock Entry** dengan tipe `Material Transfer for Manufacture`.
   - Periksa bahan-bahan yang akan dipindahkan, lalu klik **Submit**.
3. **Menyelesaikan Produksi (Finish Manufacture)**:
   - Kembali ke Work Order, klik tombol **Finish**.
   - ERPNext akan membuat **Stock Entry** dengan tipe `Manufacture`.
   - Klik **Submit**.
   - *Selamat! Bahan baku di lantai produksi berkurang dan 10 unit `FG-MEJA-01` telah masuk ke `Gudang Barang Jadi`!*

---

### TAHAP 4: Penjualan Produk Jadi (Selling)

1. **Membuat Pelanggan (Customer)**:
   - Buka menu: **Selling > Customer > New**, masukkan data dari `05_Customers.csv`.
2. **Sales Order (Pesanan Penjualan)**:
   - Buka menu: **Selling > Sales Order > New**
   - Customer: `PT Edukasi Cipta Mandiri`
   - Item: `FG-MEJA-01` sejumlah `10 Unit` @ Rp 450.000.
   - Klik **Save** dan **Submit**.
3. **Pengiriman Barang (Delivery Note)**:
   - Dari Sales Order, klik **Create > Delivery**.
   - Pastikan pengeluaran barang berasal dari `Gudang Barang Jadi`.
   - Klik **Submit**.
4. **Penagihan & Pembayaran (Sales Invoice & Payment)**:
   - Dari Sales Order/Delivery Note, klik **Create > Sales Invoice**.
   - Klik **Submit**, lalu klik **Create > Payment** untuk menerima pembayaran Rp 4.500.000.

---

## 📊 Analisis & Laporan yang Bisa Anda Pelajari
Setelah menyelesaikan simulasi di atas, Anda bisa melihat laporan real-time di ERPNext:
- **Stock Balance**: Melihat pergerakan dan sisa stok di setiap gudang (**Stock > Stock Balance**).
- **Production Analytics**: Memantau biaya produksi dan varians (**Manufacturing > Production Analytics**).
- **Profit and Loss Statement**: Melihat pendapatan penjualan, HPP (Harga Pokok Penjualan), dan laba kotor (**Accounting > Profit and Loss Statement**).
