### Langkah-Langkah Mengulang Proses (Untuk Subdomain Baru):

1. **Jalankan Aplikasi:**
    
    PowerShell
    
    ```
    .\wacs.exe
    ```
    
2. Pilih **`M`** (_Create certificate - full options_).
    
3. Pilih **`2`** (_Manual input_).
    
4. Masukkan nama subdomain baru kamu (contoh: `mcu.jhonlingroup.id` atau sesuai nama proyek baru).
    
5. **Friendly name:** Langsung tekan **Enter**.
    
6. **Split into multiple...:** Pilih **`4`** (_Single certificate_).
    
7. **How would you like to prove ownership...?:** Pilih **`1`** (_[http] Save verification files on path_).
    
8. **Path:** Masukkan folder `public` dari proyek baru tersebut (contoh: `C:\ProyekBaru\public`).
    
9. **Copy default web.config...?:** Ketik **`n`** lalu **Enter**.
    
10. **What kind of private key...?:** Pilih **`2`** (_RSA key_).
    
11. **How would you like to store...?:** Pilih **`2`** (_PEM encoded files_).
    
12. **File path:** Arahkan ke folder SSL kamu: `C:\nginx-1.28.0\conf\ssl`
    
13. **Password...:** Pilih **`1`** (_None_).
    
14. **Would you like to store it in another way too?:** Pilih **`5`** (_No additional store steps_).
    
15. **Which installation step should run first?:** Pilih **`3`** (_No additional installation steps_).
    

### Tips Nginx Setelah Sertifikat Terbit:

Nanti setelah sertifikat barunya selesai di-generate, kamu tinggal buka file `C:\nginx-1.28.0\conf\nginx.conf`, lalu buat blok `server { ... }` baru di bawah proyek Marketing tadi.