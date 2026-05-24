# 📊 Google Sheets Integration Guide

## Setup Google Sheets untuk OpEx Tracker

Aplikasi OpEx Tracker sekarang sudah terintegrasi dengan Google Sheets untuk menyimpan semua data (Pengeluaran dan Pemasukan).

### URL Google Apps Script
```
https://script.google.com/macros/s/AKfycbx7nh8w2tETE5NPihwCxcf-I80jk_VkOKhwx3Uqju7UzPrs6jM51TO_m6Q7lZxVu3ti6w/exec
```

---

## 📝 Format Data yang Dikirim ke Google Sheets

### 1. Transaksi Pengeluaran (Expense)
Ketika admin upload transaksi pengeluaran:

```json
{
  "id": "txn-1234567890-abcdefg",
  "transaction_date": "2024-05-15",
  "merchant_name": "Parkir Mall",
  "amount": 8000,
  "category": "Parkir",
  "payment_method": "Tunai",
  "notes": "2 jam parkir",
  "receipt_image_url": "base64_image_or_null",
  "status": "pending",
  "created_by": "admin-001",
  "created_at": "2024-05-15T10:30:00Z",
  "type": "expense"
}
```

### 2. Transaksi Pemasukan (Income)
Ketika admin input pemasukan:

```json
{
  "id": "txn-1234567890-xyz123",
  "income_date": "2024-05-15",
  "description": "Bonus Kinerja",
  "amount": 2500000,
  "category": "inc-1",
  "notes": "Bonus tahunan",
  "type": "income",
  "status": "approved",
  "created_at": "2024-05-15T14:20:00Z"
}
```

---

## 🔄 Flow Penyimpanan Data

### Untuk Pengeluaran (Upload Transaksi)
```
User Upload Form
    ↓
Validation (form check)
    ↓
Create Transaction Object
    ↓
↙              ↘
localStorage    Google Sheets
(opex_transactions)   (via POST request)
    ↓              ↓
LocalStorage   Google Sheets
Updated        Updated
    ↑              ↑
    └──────┬───────┘
      Toast Success:
      "✅ Transaksi berhasil disimpan & sync Google Sheets!"
```

### Untuk Pemasukan (Input Pemasukan)
```
Admin Input Form
    ↓
Validation (check description & amount)
    ↓
Create Income Object
    ↓
↙              ↘
localStorage    Google Sheets
(opex_incomes)   (via POST request)
    ↓              ↓
LocalStorage   Google Sheets
Updated        Updated
    ↑              ↑
    └──────┬───────┘
      Toast Success:
      "✅ Pemasukan berhasil disimpan & sync Google Sheets!"
```

---

## 💾 localStorage Keys yang Digunakan

| Key | Deskripsi | Tipe Data |
|-----|-----------|----------|
| `opex_transactions` | Semua transaksi pengeluaran | Array of Objects |
| `opex_incomes` | Semua transaksi pemasukan | Array of Objects |
| `opex_categories` | Kategori pengeluaran | Array of Objects |
| `opex_session` | Session user yang login | Object |
| `opex_v2` | Flag version database | Boolean |

---

## 🔐 Data yang Disimpan Dual-Layer

### Layer 1: localStorage (Browser)
- **Kecepatan:** ⚡ Instant
- **Persistance:** 💾 Persisten
- **Sinkronisasi:** 🔄 Automatic to Google Sheets

### Layer 2: Google Sheets
- **Backup:** 🛡️ Cloud backup
- **Sharing:** 👥 Dapat dibagikan
- **Analytics:** 📊 Bisa dianalisa di Sheets

---

## 📤 Fungsi yang Mengirim Data ke Google Sheets

### 1. `saveTransactionToGoogleSheet(transaction)`
**Digunakan untuk:** Menyimpan transaksi pengeluaran ke Google Sheets

```javascript
const saveTransactionToGoogleSheet = async (transaction) => {
  try {
    await fetch(GOOGLE_SCRIPT_URL, {
      method: 'POST',
      mode: 'no-cors',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        ...transaction,
        type: transaction.type || 'expense'
      })
    });
    console.log('Data berhasil dikirim ke Google Sheets');
  } catch (err) {
    console.error('Gagal sync ke Google Sheets', err);
  }
};
```

**Dipanggil dari:** UploadPage component (saat admin upload transaksi)

### 2. `saveIncomeToGoogleSheet(income)`
**Digunakan untuk:** Menyimpan transaksi pemasukan ke Google Sheets

```javascript
const saveIncomeToGoogleSheet = async (income) => {
  try {
    await fetch(GOOGLE_SCRIPT_URL, {
      method: 'POST',
      mode: 'no-cors',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        ...income,
        type: 'income'
      })
    });
    console.log('Pemasukan berhasil dikirim ke Google Sheets');
  } catch (err) {
    console.error('Gagal sync pemasukan ke Google Sheets', err);
  }
};
```

**Dipanggil dari:** IncomeUploadPage component (saat admin input pemasukan)

---

## 🎯 Skenario Penggunaan

### Skenario 1: Upload Transaksi Pengeluaran
1. Admin login dengan email: `admin`, password: `admin123`
2. Klik menu "⬆️ Upload Transaksi"
3. Upload foto struk
4. Isi detail transaksi (tanggal, merchant, nominal, etc)
5. Klik "Simpan Transaksi"
6. **Hasil:**
   - Data disimpan di localStorage `opex_transactions`
   - Data dikirim ke Google Sheets via `saveTransactionToGoogleSheet`
   - Toast success: "✅ Transaksi berhasil disimpan & sync Google Sheets!"
   - Dashboard otomatis update Total Pengeluaran

### Skenario 2: Input Pemasukan
1. Admin login
2. Klik menu "💵 Pemasukan"
3. Isi form:
   - Tanggal Pemasukan
   - Kategori (Bonus/Komisi/Reimbursement/Lainnya)
   - Deskripsi (misal: "Bonus Kinerja Mei 2024")
   - Nominal Rp (misal: 2500000)
   - Catatan (opsional)
4. Klik "✅ Simpan Pemasukan"
5. **Hasil:**
   - Data disimpan di localStorage `opex_incomes`
   - Data dikirim ke Google Sheets via `saveIncomeToGoogleSheet`
   - Toast success: "✅ Pemasukan berhasil disimpan & sync Google Sheets!"
   - Dashboard otomatis update Total Pemasukan & Net Balance

---

## 🚨 Error Handling

### Jika Google Sheets Tidak Dapat Diakses
```
Status: ⚠️ Warning
Behavior: Data tetap disimpan di localStorage
Message di Console: "Gagal sync ke Google Sheets"
User Experience: Data tidak hilang, hanya tidak tersync ke Sheets
```

### Backup Strategy
```
LocalStorage (Primary)
    ↓
Google Sheets (Backup)

Jika Google Sheets gagal:
- Data tetap aman di localStorage
- User dapat retry sinkronisasi nanti
- Atau download data dari localStorage sebagai CSV
```

---

## 📊 Struktur Google Sheet yang Diharapkan

Sheet Anda harus memiliki kolom-kolom berikut:

### Tab 1: Transactions (Pengeluaran)
| id | transaction_date | merchant_name | amount | category | payment_method | notes | receipt_image_url | status | created_by | created_at | type |
|---|---|---|---|---|---|---|---|---|---|---|---|

### Tab 2: Incomes (Pemasukan)
| id | income_date | description | amount | category | notes | type | status | created_at |
|---|---|---|---|---|---|---|---|---|

---

## 🔗 Integrasi dengan Google Apps Script

Google Apps Script Anda harus:
1. ✅ Menerima POST request dengan JSON data
2. ✅ Membedakan antara transaksi pengeluaran (type='expense') dan pemasukan (type='income')
3. ✅ Menyimpan ke sheet yang sesuai
4. ✅ Return response 200 OK

**Contoh Google Apps Script:**
```javascript
function doPost(e) {
  const data = JSON.parse(e.postData.contents);
  
  const sheet = SpreadsheetApp.getActiveSpreadsheet();
  
  // Jika type = 'income', simpan ke sheet Incomes
  if (data.type === 'income') {
    const incomeSheet = sheet.getSheetByName('Incomes');
    incomeSheet.appendRow([
      data.id, data.income_date, data.description, 
      data.amount, data.category, data.notes, 
      data.type, data.status, data.created_at
    ]);
  } 
  // Jika type = 'expense', simpan ke sheet Transactions
  else {
    const txnSheet = sheet.getSheetByName('Transactions');
    txnSheet.appendRow([
      data.id, data.transaction_date, data.merchant_name,
      data.amount, data.category, data.payment_method,
      data.notes, data.receipt_image_url, data.status,
      data.created_by, data.created_at, data.type
    ]);
  }
  
  return ContentService.createTextOutput('OK');
}
```

---

## ✅ Checklist Setup

- [ ] Google Sheets sudah dibuat dengan kolom yang sesuai
- [ ] Google Apps Script sudah di-deploy sebagai web app
- [ ] URL Google Apps Script sudah benar di `index.html`
- [ ] Google Apps Script bisa menerima POST request
- [ ] Test: Upload 1 transaksi pengeluaran
- [ ] Cek: Data muncul di Google Sheets
- [ ] Test: Input 1 pemasukan
- [ ] Cek: Data muncul di Google Sheets dengan `type='income'`

---

## 📱 Monitoring di Google Sheets

Anda bisa membuat:
1. **Dashboard Sheets:** Menggunakan SUMIF untuk menghitung total by type
2. **Charts:** Visualisasi data pengeluaran vs pemasukan
3. **Filter:** Filter berdasarkan tanggal, kategori, type
4. **Pivot Tables:** Analisis data lebih mendalam

**Contoh Formula di Google Sheets:**
```
Total Pengeluaran: =SUMIF(L:L,"expense",D:D)
Total Pemasukan: =SUMIF(L:L,"income",D:D)
Net Balance: =SUMIF(L:L,"income",D:D) - SUMIF(L:L,"expense",D:D)
```

---

**Version:** 1.0.1 with Google Sheets Integration
**Status:** ✅ Ready to Use
