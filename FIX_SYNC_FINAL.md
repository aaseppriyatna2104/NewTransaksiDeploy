# 🔧 FIX SINKRONISASI - Google Sheet & Apps Script Baru

## 📋 Masalah
```
File index.html menggunakan URL Google Apps Script LAMA
  ↓
URL tidak match dengan Google Sheet & Script baru
  ↓
Data tidak tersimpan atau tidak ter-load dari Google Sheets
  ↓
❌ Aplikasi tidak berfungsi dengan baik
```

---

## ✅ SOLUSI LENGKAP (3 STEP)

### STEP 1: Update Google Apps Script di Google Console

**PENTING!** Spreadsheet ID baru: `1QUBYoAsyH75KYRtnXLmBpNWtiwZN47Rfrf6rFHQxTOQ`

1. **Buka Google Apps Script:**
   - Link: https://script.google.com/macros/s/AKfycbwXbOlO-AULsjvkz1cRB-Vbv_7_YjN1I1GYbFE5rGnXes9vFztD7ybM5TuTPxmIOSgA/exec

2. **Copy semua code dari file `GOOGLE_APPS_SCRIPT_FINAL.gs`**

3. **Replace semua code di Google Apps Script editor:**
   - Select All (Ctrl+A)
   - Delete
   - Paste code baru

4. **Save & Deploy:**
   - Click "Save" (Ctrl+S)
   - Click "Deploy" → "New deployment"
   - Type: "Web app"
   - Execute as: Your Account
   - Who has access: "Anyone"
   - Click "Deploy"

5. **Test Google Apps Script:**
   - Click "Run" menu
   - Select `testConnection()` → Run
   - Check Logs (Ctrl+K) untuk verify:
     ```
     ✅ transactions_sheet: ✅ Found
     ✅ incomes_sheet: ✅ Found
     ```

---

### STEP 2: Update index.html dengan URL Baru

File `index.html` yang sudah di-update **sudah tersedia** di `/mnt/user-data/outputs/index.html`

**Yang berubah:**
```javascript
// LAMA:
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbx7nh8...';

// BARU:
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbwXbOlO-AULsjvkz1cRB-Vbv_7_YjN1I1GYbFE5rGnXes9vFztD7ybM5TuTPxmIOSgA/exec';
```

**Cara update:**
1. Download file `index.html` dari outputs
2. Replace file lama Anda
3. Atau copy-paste URL baru ke file lama Anda

---

### STEP 3: Verifikasi Google Sheet Structure

Pastikan Google Sheet Anda memiliki struktur yang benar:

**Buka:** https://docs.google.com/spreadsheets/d/1QUBYoAsyH75KYRtnXLmBpNWtiwZN47Rfrf6rFHQxTOQ/

#### Sheet "Transactions" (Pengeluaran)
```
Kolom: A       B                   C              D       E        F                G      H                  I         J          K              L
Header: id  transaction_date  merchant_name   amount  category payment_method notes receipt_image_url status created_by created_at     type
```

**HARUS ADA: kolom L untuk "type" = 'expense'**

#### Sheet "Incomes" (Pemasukan)
```
Kolom: A      B          C           D       E        F     G       H       I
Header: id income_date description amount category notes  type status created_at
```

**HARUS ADA: kolom G untuk "type" = 'income'**

---

## 🧪 TEST SETELAH PERBAIKAN

### Test 1: Load Data
```
Buka aplikasi → Dashboard
Refresh halaman (Ctrl+Shift+R)
Check: Apakah data ter-load dari Google Sheets?
Expected: ✅ Data muncul di Dashboard
```

### Test 2: Input Pemasukan
```
1. Login sebagai Admin
2. Klik menu 💵 Pemasukan
3. Input:
   - Tanggal: hari ini
   - Kategori: Bonus
   - Deskripsi: "Test Income"
   - Nominal: 100000
4. Klik Simpan
5. Check Browser Console (F12):
   📤 Sending INCOME to Google Sheets: {type: 'income', ...}
   ✅ INCOME berhasil dikirim ke Google Sheets
6. Check Google Sheets → Sheet "Incomes":
   ✅ Data muncul di baris terakhir
   ✅ Kolom type = 'income'
```

### Test 3: Upload Pengeluaran
```
1. Klik menu ⬆️ Upload Transaksi
2. Input:
   - Tanggal: hari ini
   - Merchant: "Test Merchant"
   - Nominal: 50000
3. Klik Simpan
4. Check Browser Console:
   📤 Sending EXPENSE to Google Sheets: {type: 'expense', ...}
   ✅ EXPENSE berhasil dikirim ke Google Sheets
5. Check Google Sheets → Sheet "Transactions":
   ✅ Data muncul di baris terakhir
   ✅ Kolom type = 'expense'
```

### Test 4: Dashboard Accuracy
```
Refresh aplikasi (Ctrl+Shift+R)
Check Dashboard:
  💵 Total Pemasukan: Harus sesuai dengan Incomes sheet
  💸 Total Pengeluaran: Harus sesuai dengan Transactions sheet
  📊 Net Balance: Pemasukan - Pengeluaran
```

---

## 📊 URL & Credentials yang Benar

### URL Google Sheets (Baru)
```
https://docs.google.com/spreadsheets/d/1QUBYoAsyH75KYRtnXLmBpNWtiwZN47Rfrf6rFHQxTOQ/
```

### Spreadsheet ID (Baru)
```
1QUBYoAsyH75KYRtnXLmBpNWtiwZN47Rfrf6rFHQxTOQ
```

### URL Google Apps Script (Baru)
```
https://script.google.com/macros/s/AKfycbwXbOlO-AULsjvkz1cRB-Vbv_7_YjN1I1GYbFE5rGnXes9vFztD7ybM5TuTPxmIOSgA/exec
```

### Di index.html (HARUS sesuai)
```javascript
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbwXbOlO-AULsjvkz1cRB-Vbv_7_YjN1I1GYbFE5rGnXes9vFztD7ybM5TuTPxmIOSgA/exec';
const SPREADSHEET_ID = '1QUBYoAsyH75KYRtnXLmBpNWtiwZN47Rfrf6rFHQxTOQ'; // Jika ada di kode
```

---

## 🔍 Debugging Jika Masih Ada Masalah

### Masalah: "Data tidak ter-load"
```
1. Buka Browser Console (F12)
2. Cek apakah ada fetch error
3. URL Google Apps Script benar?
4. Google Sheets accessible?
5. Sheet names benar? (case-sensitive: "Transactions", "Incomes")
```

### Masalah: "Data tersimpan tapi tidak muncul di Sheets"
```
1. Google Apps Script di-deploy dengan benar?
2. Execute as: Your Account? (bukan "Me")
3. Who has access: Anyone?
4. Check Google Apps Script Logs (Ctrl+K)
5. Cek Spreadsheet ID di Google Apps Script sama dengan yang baru?
```

### Masalah: "Income masuk ke Transactions sheet"
```
1. Data dikirim dengan type field?
2. Google Apps Script check: if (data.type === 'income')?
3. Incomes sheet ada di Google Sheets?
4. Test: Run testSaveIncome() di Google Apps Script
5. Check Logs apakah tersimpan di Incomes sheet
```

---

## ✨ Data Flow Setelah Fix

```
User Input/Upload
    ↓
index.html (dengan URL baru)
    ↓
Fetch ke Google Apps Script (URL baru)
    ↓
Google Apps Script (dengan Spreadsheet ID baru)
    ↓
├─ Jika type='income' → Simpan ke Incomes sheet
└─ Jika type='expense' → Simpan ke Transactions sheet
    ↓
doGet() → Load semua data
    ↓
Pisahkan income vs expense
    ↓
Return ke aplikasi
    ↓
Dashboard update dengan data akurat ✅
```

---

## 📝 File-File yang Sudah Diupdate

1. **index.html** ✅
   - URL Google Apps Script sudah diupdate
   - Ready to use

2. **GOOGLE_APPS_SCRIPT_FINAL.gs** ✅
   - Spreadsheet ID: `1QUBYoAsyH75KYRtnXLmBpNWtiwZN47Rfrf6rFHQxTOQ`
   - Logging lengkap untuk debugging
   - Test functions untuk verifikasi

---

## 🎯 Checklist After Fix

- [ ] Download/Copy GOOGLE_APPS_SCRIPT_FINAL.gs ke Google Apps Script
- [ ] Replace semua code di Google Apps Script
- [ ] Save & Deploy Google Apps Script
- [ ] Run testConnection() → verify sheets ada
- [ ] Run testSaveIncome() → verify income saved
- [ ] Run testSaveExpense() → verify expense saved
- [ ] Download index.html baru (atau update URL-nya)
- [ ] Replace file lama Anda
- [ ] Refresh browser (Ctrl+Shift+R)
- [ ] Test input Income → check Google Sheets
- [ ] Test upload Expense → check Google Sheets
- [ ] Verify Dashboard accuracy
- [ ] Cek Console untuk "Sending INCOME/EXPENSE" log
- [ ] Done! ✅

---

**Status:** ✅ READY TO FIX
**Difficulty:** ⭐ Easy
**Time:** ⏱️ 5-10 minutes
**Support:** Panduan lengkap tersedia di atas
