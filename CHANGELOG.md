# OpEx Tracker - Update Pemasukan (Income)

## 🎯 Fitur Baru yang Ditambahkan

### 1. **Modul Pemasukan (Income Module)**
   - Menambahkan kategori pemasukan baru:
     - 🎁 Bonus
     - 💰 Komisi
     - ↩️ Reimbursement
     - 📦 Lainnya

### 2. **Halaman Input Pemasukan (Income Upload Page)**
   - Hanya admin yang dapat mengakses
   - Menu di sidebar: **💵 Pemasukan**
   - Field yang tersedia:
     - **Tanggal Pemasukan** - date picker
     - **Kategori** - pilih dari kategori pemasukan
     - **Deskripsi/Sumber** - teks input (wajib diisi)
     - **Nominal** - angka dengan preview rupiah
     - **Catatan** - text area (opsional)

### 3. **Dashboard Update**
   Dashboard sekarang menampilkan 3 kartu statistik utama:
   
   | Kartu | Deskripsi | Formula |
   |-------|-----------|---------|
   | 💵 Total Pemasukan | Total semua pemasukan | SUM(incomes.amount) |
   | 💰 Total Pengeluaran | Total semua pengeluaran | SUM(expenses.amount) |
   | 📊 Net Balance | Sisa pemasukan setelah pengeluaran | Total Pemasukan - Total Pengeluaran |

### 4. **Penyimpanan Data**
   - Pemasukan disimpan di localStorage dengan key: `opex_incomes`
   - Setiap transaksi pemasukan memiliki struktur:
     ```javascript
     {
       id: "string (unique)",
       income_date: "YYYY-MM-DD",
       description: "string",
       amount: number,
       category: "inc-1|inc-2|inc-3|inc-4",
       notes: "string (optional)",
       type: "income",
       status: "approved",
       created_at: "ISO timestamp"
     }
   ```

## 🔐 Kontrol Akses

| Fitur | Admin | Staff |
|-------|-------|-------|
| Upload Transaksi Pengeluaran | ✅ | ✅ |
| Lihat Daftar Transaksi | ✅ | ❌ |
| Input Pemasukan | ✅ | ❌ |
| Kategori Management | ✅ | ❌ |
| Dashboard Lengkap | ✅ | ✅ |

## 📊 Cara Menggunakan

### Input Pemasukan (Admin Only)
1. Login sebagai Admin
2. Klik menu **💵 Pemasukan** di sidebar
3. Isi form:
   - Pilih tanggal pemasukan
   - Pilih kategori (Bonus, Komisi, Reimbursement, atau Lainnya)
   - Isi deskripsi/sumber pemasukan
   - Masukkan nominal dalam Rp
   - (Opsional) Tambahkan catatan
4. Klik **✅ Simpan Pemasukan**

### Lihat di Dashboard
- Dashboard secara otomatis menampilkan:
  - Total Pemasukan (dari semua income transactions)
  - Total Pengeluaran (dari semua expense transactions)
  - Net Balance = Total Pemasukan - Total Pengeluaran
  
Warna indicator untuk Net Balance:
- **Hijau (Emerald)** = Positif (lebih banyak pemasukan)
- **Merah (Rose)** = Negatif (lebih banyak pengeluaran)

## 💾 Data Persistence
- Semua pemasukan disimpan di localStorage
- Data persisten di browser (tidak hilang saat refresh)
- Setiap pemasukan langsung ter-update di dashboard

## 🎨 UI/UX Changes
- Sidebar ditambah 1 menu: **💵 Pemasukan**
- Dashboard stat cards berubah dari 3 menjadi yang menampilkan:
  1. Total Pemasukan (emerald)
  2. Total Pengeluaran (rose)
  3. Net Balance (dinamis: emerald/rose)

## 📝 Login Credentials (Demo)
- **Admin**
  - Email: `admin`
  - Password: `admin123`
  
- **Staff** (Tanpa akses pemasukan)
  - Email: `Rezky123`
  - Password: `Rezky123`

---

**Version:** 1.0.1 with Income Module
**Date:** 2024
