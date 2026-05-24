# 🔧 FIX: Income Tercampur dengan Expense

## 📋 Masalah
```
Saat input Pemasukan (Income) Rp 50.000:
❌ Masuk ke "Transactions" sheet (seharusnya ke "Incomes")
❌ Terhitung sebagai Pengeluaran
❌ Dashboard tidak akurat
```

**Penyebab Root:**
1. Google Apps Script tidak membedakan Income vs Expense dengan benar
2. Data Income dikirim tapi tidak tersimpan di sheet yang benar
3. Type field tidak di-force/override di Google Apps Script

---

## ✅ Solusi

### STEP 1: Update Google Apps Script (PENTING!)

1. **Buka Google Apps Script Anda**
   - Ke: https://script.google.com/macros/s/AKfycbx7nh8w2tETE5NPihwCxcf-I80jk_VkOKhwx3Uqju7UzPrs6jM51TO_m6Q7lZxVu3ti6w/exec
   
2. **Copy seluruh code dari file `GOOGLE_APPS_SCRIPT_FIXED.gs`**
   - Replace semua code di Google Apps Script Anda dengan code baru

3. **Deploy ulang**
   - Click "Deploy" → New deployment
   - Choose "Web app"
   - Execute as: Your Account
   - Who has access: Anyone
   - Click "Deploy"

4. **Test di Google Apps Script Editor**
   - Click "Run" → Select `testSendIncome()`
   - Check logs untuk verify
   - Run juga `testSendExpense()`

---

### STEP 2: Update index.html

✅ Sudah otomatis di-update!

**Yang berubah:**
```javascript
// Sebelum:
type: transaction.type || 'expense'  // Bisa jadi undefined

// Sesudah:
type: 'expense'  // FORCE selalu 'expense' untuk transactions
type: 'income'   // FORCE selalu 'income' untuk incomes
```

---

### STEP 3: Bersihkan Data Lama di Google Sheets

**Langkah-langkah:**
1. Buka Google Sheets Anda
2. Pergi ke sheet "Transactions"
3. **Cari baris dengan kategori `inc-1`, `inc-2`, `inc-3`, `inc-4`**
   - Ini adalah data Income yang salah tempat
4. **Hapus baris-baris tersebut** (gunakan Ctrl+X)
5. Pergi ke sheet "Incomes"
6. Paste data yang dihapus tadi (atau manual input ulang)
7. Pastikan kolom "type" = `income` untuk semua baris

**Atau lebih mudah:**
- Di Transactions sheet, select seluruh data
- Delete semua data (kecuali header)
- Fresh start dengan data yang bersih

---

### STEP 4: Test Ulang

**Test 1: Input Pemasukan**
1. Login ke aplikasi
2. Klik menu 💵 Pemasukan
3. Input data:
   - Tanggal: Hari ini
   - Kategori: Bonus
   - Deskripsi: "Test Pemasukan"
   - Nominal: 100000
4. Klik ✅ Simpan Pemasukan
5. **Check Console (F12)** untuk log:
   ```
   📤 Sending INCOME to Google Sheets: {type: 'income', ...}
   ✅ INCOME berhasil dikirim ke Google Sheets
   ```

**Test 2: Upload Pengeluaran**
1. Klik menu ⬆️ Upload Transaksi
2. Input data:
   - Tanggal: Hari ini
   - Merchant: "Test Merchant"
   - Nominal: 50000
3. Klik Simpan
4. **Check Console** untuk log:
   ```
   📤 Sending EXPENSE to Google Sheets: {type: 'expense', ...}
   ✅ EXPENSE berhasil dikirim ke Google Sheets
   ```

**Test 3: Verifikasi di Google Sheets**
1. Buka Google Sheets Anda
2. **Cek sheet "Incomes":**
   - Ada 1 baris data dengan amount 100000
   - type = 'income'
3. **Cek sheet "Transactions":**
   - Ada 1 baris data dengan amount 50000
   - type = 'expense'
4. Jangan ada data yang tercampur!

**Test 4: Dashboard Check**
1. Refresh aplikasi (Ctrl+Shift+R)
2. Lihat Dashboard:
   ```
   💵 Total Pemasukan: Rp 100.000 ✅
   💸 Total Pengeluaran: Rp 50.000 ✅
   📊 Net Balance: Rp 50.000 ✅
   ```

---

## 📊 Google Sheets Structure (HARUS BENAR)

### Sheet: Transactions (Pengeluaran)
| Kolom | Type | Keterangan |
|-------|------|-----------|
| A | id | Transaction ID |
| B | transaction_date | Tanggal transaksi |
| C | merchant_name | Nama merchant |
| D | amount | Nominal (angka saja) |
| E | category | Kategori pengeluaran |
| F | payment_method | Metode pembayaran |
| G | notes | Catatan |
| H | receipt_image_url | URL gambar struk |
| I | status | Status (pending/approved/rejected) |
| J | created_by | User yang input |
| K | created_at | Waktu input |
| L | type | **HARUS 'expense'** |

### Sheet: Incomes (Pemasukan)
| Kolom | Type | Keterangan |
|-------|------|-----------|
| A | id | Income ID |
| B | income_date | Tanggal pemasukan |
| C | description | Deskripsi pemasukan |
| D | amount | Nominal (angka saja) |
| E | category | Kategori pemasukan (inc-1,inc-2,inc-3,inc-4) |
| F | notes | Catatan |
| G | type | **HARUS 'income'** |
| H | status | Status (approved/pending) |
| I | created_at | Waktu input |

---

## 🔍 Debugging Tips

### Jika masih tidak bekerja:

1. **Check Browser Console (F12)**
   ```
   Buka: Console tab
   Filter: "Sending INCOME" atau "Sending EXPENSE"
   Lihat: type field dalam log
   ```

2. **Check Google Apps Script Logs**
   ```
   Buka: Google Apps Script Editor
   Click: Execution → View logs
   Lihat: Apa yang terlog
   ```

3. **Test Google Apps Script langsung**
   ```
   Di Google Apps Script:
   - Click Run → testSendIncome()
   - Check logs
   - Check Google Sheets apakah data muncul di Incomes sheet
   ```

4. **Verify Sheet Names**
   ```
   Google Sheets Anda harus memiliki:
   - Sheet bernama "Transactions" (case-sensitive)
   - Sheet bernama "Incomes" (case-sensitive)
   
   Jika berbeda nama, update di Google Apps Script:
   const txnSheet = ss.getSheetByName('Transactions');
   const incomeSheet = ss.getSheetByName('Incomes');
   ```

---

## ✨ Improvement dari Fix

| Aspek | Sebelum | Sesudah |
|-------|---------|--------|
| **Income Saving** | ❌ Ke Transactions | ✅ Ke Incomes |
| **Expense Saving** | ✅ Ke Transactions | ✅ Ke Transactions |
| **Type Field** | ⚠️ Bisa undefined | ✅ ALWAYS explicit |
| **Dashboard** | ❌ Tidak akurat | ✅ Akurat |
| **Data Integrity** | ❌ Tercampur | ✅ Terpisah rapi |

---

## 🎯 Checklist After Fix

- [ ] Update Google Apps Script
- [ ] Deploy ulang Google Apps Script
- [ ] Update index.html (atau download yang baru)
- [ ] Clear data lama di Google Sheets
- [ ] Refresh browser (Ctrl+Shift+R)
- [ ] Test input Income → masuk ke Incomes sheet
- [ ] Test upload Expense → masuk ke Transactions sheet
- [ ] Verify Dashboard numbers akurat
- [ ] Check Console log ada type field
- [ ] Commit/backup code yang working

---

## 🚀 After Everything Works

1. **Production**: Siap untuk digunakan
2. **Monitoring**: Kirim data, monitor Google Sheets update
3. **Scaling**: Bisa add fitur lainnya dengan confident

---

**Status:** 🔧 Ready to Fix  
**Time to Fix:** ~10-15 menit  
**Difficulty:** Easy  
