---
title: 🔴 Enable Delete Button in SE16N
layout: layout
---

# 🔴 Mengaktifkan Tombol Delete di SE16N

> [!WARNING] Debug Mode
> Metode ini menggunakan debug untuk memodifikasi behavior SE16N secara sementara.

## Langkah-Langkah:

### 1️⃣ Masuk Mode Debug
- Buka SE16N dan jalankan query
- Tekan `Ctrl + Shift + F1` atau masuk ke **Debug Mode**

### 2️⃣ Tambah/Modifikasi Variable

Tambahkan atau ubah variable berikut:

| Variable | Nilai | Fungsi |
|----------|-------|--------|
| `GD-EDIT` | `X` | Enable edit mode |
| `GD-EDITSAP` | `X` | Enable SAP native edit |

### 3️⃣ Lanjutkan Eksekusi
- Tekan `F8` untuk melanjutkan
- Tombol Delete seharusnya sudah aktif

> [!CAUTION] Catatan
> Ini adalah metode development/testing. Untuk production, gunakan SE11/SM30 atau custom Z-program.
