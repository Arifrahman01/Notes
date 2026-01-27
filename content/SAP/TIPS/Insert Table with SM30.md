### Langkah 1: Membuat Maintenance Dialog (TMG)

1. Buka transaksi **SE11**, masukkan nama tabel `ZTA_FI_GRGL`, lalu klik **Change**.
    
2. Di menu bagian atas, pilih: **Utilities** > **Table Maintenance Generator**.
    
3. Isi parameter berikut:
    
    - **Authorization Group:** Isi dengan `&NC&` (artinya tidak ada otorisasi khusus, siapa saja bisa akses).
        
    - **Function Group:** Isi dengan nama yang sama dengan tabel Anda, yaitu `ZTA_FI_GRGL` (Jika belum ada, sistem akan meminta Anda membuatnya, klik _Yes_ saja).
        
    - **Maintenance Screens:** Pilih tipe **One Step**.
        
    - **Screen Number:** Klik tombol **Find Screen Number(s)** di toolbar, pilih **Propose Screen Number**, maka sistem akan mengisi angka otomatis (biasanya `0001`).
        
4. Klik tombol **Create** (ikon kertas putih/folder di pojok kiri atas).
    
5. Jika muncul _pop-up_ permintaan **Request/Task** (Transport Request), masukkan nomor request yang ada atau klik _Create Request_ baru.
    
6. Tunggu hingga muncul pesan di status bar: _"Request for ZTA_FI_GRGL completed successfully"_.