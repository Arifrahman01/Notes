---
title: 🏆 Catatan Premi Upah RKB
layout: layout
---

## 📋 TCODE & Referensi

**yfe010**

---

## 📊 Perhitungan Upah Insentif Pupuk

Perhitungan 'Upah Insentif Pupuk' dapat ditemukan di beberapa bagian utama sebagai berikut:

### 1️⃣ Metode `calc_totalupah_hk` - Class `lcl_alv_kwn`

Sistem menghitung insentif berdasarkan hasil kerja dan prestasi pupuk.

- **Pemanggilan Fungsi:** `ycl_premi_bkm=>calc_premi_rawat`
- **Input:** `im_hasil_kerja` dan `im_hasil_pupuk`
- **Output:** `ex_premi_insentif` → `lv_premi_insentif`
- **Penyimpanan:** `<fs_karyawan>-upah_insentif_pupuk`

### 2️⃣ Akumulasi ke Total Upah

```
Total = upah_harian + upah_premi + upah_insentif_pupuk
        + upah_kerjalibur + insentif_lembur - advance
```

### 3️⃣ Kondisi Khusus

Jika `lv_wc_others = 'M'` → nilai `upah_insentif_pupuk` akan menjadi **0**

### 4️⃣ Metode `simulate_calc` - Class `lcl_app`

Melakukan simulasi perhitungan identik sebelum ditampilkan/disimpan

---

## 🔍 LYFG_BKM_RAWATTOP - Root Cause Analysis

**Masalah:** Karyawan tidak mendapatkan insentif saat bekerja di dua blok

**Penyebab Teknis:**

### Isu 1: Filter Data Berdasarkan RKH (Blok)

- Program filter data `gi_karyawan` hanya untuk RKH terpilih
- Data karyawan di blok lain "tersembunyi" dari konteks perhitungan
- Jika karyawan di Blok A & B → data B tidak dihitung saat melihat Blok A

### Isu 2: Perhitungan Per Baris (Tidak Akumulatif)

- Looping hanya pada baris aktif saat itu
- Memanggil `calc_premi_rawat` dengan data dari baris tersebut saja
- **Tidak menjumlahkan** hasil kerja dari blok lain
- Jika hasil kerja 1 blok < minimum → insentif = 0

### Isu 3: Risiko Pengozongan Upah

- Jika bekerja di 2 blok & status = 'M' → **semua upah & insentif jadi 0**