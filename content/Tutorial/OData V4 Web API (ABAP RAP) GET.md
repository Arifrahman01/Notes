# Outline Manual Book: OData V4 Web API (ABAP RAP)

Berikut adalah garis besar urutan pembuatan API OData V4 untuk kebutuhan integrasi sistem-ke-sistem menggunakan ABAP RAP di SAP S/4HANA.

---

## Langkah 1: Pembuatan CDS View Entity (Data Model)
* **Object di Eclipse:** Data Definition
* **Tindakan:** Buat CDS View baru untuk mengambil data dari tabel. Pastikan menggunakan format modern yaitu `define root view entity`.
* **Status:** Simpan dan **Activate** (Ctrl + F3).

## Langkah 2: Pembuatan Service Definition
* **Object di Eclipse:** Service Definition
* **Tindakan:** Buat Service Definition baru. Daftarkan CDS View yang sudah dibuat di Langkah 1 menggunakan perintah `expose` beserta nama aliasnya (contoh: `expose ZCFIAS001 as MovementAsset;`).
* **Status:** Simpan dan **Activate** (Ctrl + F3).

## Langkah 3: Pembuatan Service Binding
* **Object di Eclipse:** Service Binding
* **Tindakan:** Buat Service Binding dengan me-referensi Service Definition dari Langkah 2.
* **Pengaturan Wajib:** Pada kolom Binding Type, pilih **OData V4 - Web API**.
* **Status:** Simpan dan **Activate** (Ctrl + F3) di dalam layar Service Binding. 
> *(Abaikan tombol Publish di sini jika sistem SAP Anda memblokir publikasi lokal).*

## Langkah 4: Publikasi API via SAP GUI (Backend Publish)
* **Lokasi:** SAP GUI -> T-Code **`/IWFND/V4_ADMIN`**
* **Tindakan:** 
  1. Klik tombol **Publish Service Groups** (Ikon +).
  2. Cari dengan parameter **System Alias:** `LOCAL` dan **Service Group ID:** `[Nama Service Binding Anda]`.
  3. Centang hanya pada baris service Anda di tabel hasil pencarian (jangan centang default support).
  4. Klik tombol **Publish Service Groups** di bagian bawah.
* **Status Akhir:** Kembali ke Eclipse, tekan **Refresh (F5)** pada Service Binding. Status akan berubah menjadi **Published**.

## Langkah 5: Testing & Format Data (JSON)
* **Lokasi:** SAP GUI -> T-Code **`/IWFND/GW_CLIENT`** (Gateway Client)
* **Tindakan:**
  1. Salin **Service URL** dari Eclipse dan *paste* ke kolom Request URI.
  2. Hapus parameter `$metadata` di ujung URL.
  3. Ganti dengan nama alias tabel Anda (contoh: `MovementAsset`).
  4. Tambahkan `?$format=json` di akhir URL.
  5. Klik **Execute**. 
* **Hasil:** Sistem harus memberikan respons **200 OK** dan menampilkan data dalam format JSON.