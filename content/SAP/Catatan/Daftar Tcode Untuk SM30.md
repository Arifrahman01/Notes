### Langkah 1: Buka Transaksi SE93

1. Masukkan T-Code **SE93** di command field.
    
2. Isi nama T-Code baru yang ingin Anda buat (contoh: `ZMAINT_TABLE` atau `ZHM_01`).
    
3. Klik tombol **Create**.
    

### Langkah 2: Pilih Transaction Type

1. Masukkan deskripsi singkat (Short Text), misal: _Maintenance Tabel HM_.
    
2. Pilih opsi paling bawah: **Transaction with parameters (parameter transaction)**.
    
3. Klik centang hijau atau tekan Enter.
    

### Langkah 3: Konfigurasi Parameter

Di layar berikutnya, lakukan pengaturan berikut:

1. **Transaction:** Isi dengan **`SM30`**.
    
2. **Skip Initial Screen:** Centang (agar user tidak perlu melihat layar depan SM30).
    
3. **Classification:** Centang **Inherit GUI attributes**.
    

### Langkah 4: Isi Default Values (Bagian Bawah)

Scroll ke tabel paling bawah di bagian **Default Values**, lalu tambahkan dua baris ini:

| **Name of screen field** | **Value**                         |
| ------------------------ | --------------------------------- |
| **`VIEWNAME`**           | _Isi dengan nama tabel/view Anda_ |
| **`UPDATE`**             | **`X`**                           |

> **Note:** Jika Anda ingin user hanya bisa melihat (Display Only), ganti value `UPDATE` menjadi kosong atau gunakan parameter `SHOW = X`.

### Langkah 5: Simpan dan Tes

1. Klik **Save** (Simpan ke Package dan Assign ke Transport Request).
    
2. Tes T-Code baru Anda dengan mengetikkannya di command field.
    

---

### Kenapa cara ini lebih baik?

- **Security:** Anda bisa memberikan izin akses (Authorization) ke T-Code spesifik tersebut kepada user tanpa memberikan akses penuh ke transaksi SM30 yang berbahaya jika user bisa mengetik nama tabel lain.
    
- **User Friendly:** User tidak perlu menghafal nama tabel teknis (seperti `ZT_HM_DATA`), mereka cukup menghafal T-Code yang sudah Anda buat.
    

Apakah T-Code ini rencananya akan digunakan oleh tim operasional di Jhonlin atau hanya untuk kebutuhan _testing_ internal saja?