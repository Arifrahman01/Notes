---
title: 📐 Membuat Maintenance Table Dialog
layout: layout
---

# 📐 Membuat Maintenance Table Dialog (TMG)

## Langkah 1: Membuat Maintenance Dialog di SE11

1. Buka transaksi **SE11**, masukkan nama tabel (misal `ZTA_FI_GRGL`), klik **Change**

2. Di menu, pilih: **Utilities** > **Table Maintenance Generator**

3. Isi parameter:
   - **Authorization Group:** `&NC&` (siapa saja bisa akses)
   - **Function Group:** Nama sama dengan tabel: `ZTA_FI_GRGL`
   - **Maintenance Screens:** Pilih **One Step**
   - **Screen Number:** Klik **Find Screen Number(s)** > **Propose** (auto `0001`)

4. Klik tombol **Create** (ikon folder putih di pojok kiri)

5. Input **Transport Request** jika diminta

6. Tunggu pesan sukses: _"Request for ZTA_FI_GRGL completed successfully"_

---

## Langkah 2: Create Table dengan SE16N

### Gunakan Function Module `SE16N_INTERFACE`

Jika SE16N tidak mengizinkan input di tabel kosong:

1. Buka transaksi **SE37**
2. Masukkan: `SE16N_INTERFACE`
3. Tekan **F8** (Test/Execute)
4. Isi parameter:
   - `I_TAB`: Nama tabel
   - `I_EDIT`: `X`
   - `I_SAPEDIT`: `X`
5. Execute (**F8**) - grid SE16N akan membuka dalam mode edit