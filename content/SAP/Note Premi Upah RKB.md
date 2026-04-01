## TCODEyfe010
```perhitungan 'Upah Insentif Pupuk' dapat ditemukan di beberapa bagian utama sebagai berikut:

### 1. Metode `calc_totalupah_hk` dalam Class `lcl_alv_kwn`

Di bagian ini, sistem menghitung insentif berdasarkan hasil kerja dan prestasi pupuk.

- **Pemanggilan Fungsi:** Nilai insentif diperoleh dari pemanggilan metode `ycl_premi_bkm=>calc_premi_rawat`.
    
- **Input Perhitungan:** Menggunakan parameter `im_hasil_kerja` dan `im_hasil_pupuk` (yang diambil dari field `prestasi_pupuk` karyawan).
    
- **Output Perhitungan:** Hasil perhitungan (`ex_premi_insentif`) kemudian dimasukkan ke dalam variabel lokal `lv_premi_insentif`.
    
- **Penyimpanan ke Tabel:** Nilai dari `lv_premi_insentif` tersebut dipindahkan ke field `<fs_karyawan>-upah_insentif_pupuk`.
    

### 2. Akumulasi ke Total Upah

Masih dalam metode yang sama, `upah_insentif_pupuk` menjadi salah satu komponen penambah dalam perhitungan total upah harian karyawan:

- **Rumus:** `<fs_karyawan>-total_upah = <fs_karyawan>-upah_harian + <fs_karyawan>-upah_premi + <fs_karyawan>-upah_insentif_pupuk + <fs_karyawan>-upah_kerjalibur + <fs_karyawan>-insentif_lembur - <fs_karyawan>-advance`.
    

### 3. Kondisi Khusus (Pengecekan Status)

Terdapat pengecekan status kerja tertentu (`lv_wc_others = 'M'`). Jika kondisi ini terpenuhi, maka nilai `upah_insentif_pupuk` akan dipaksa menjadi **0**.

### 4. Metode `simulate_calc` dalam Class `lcl_app`

Kode ini juga melakukan simulasi perhitungan yang identik dengan logika di atas, menggunakan pemanggilan fungsi premi yang sama untuk menentukan nilai insentif pupuk sebelum ditampilkan atau disimpan


```

### LYFG_BKM_RAWATTOP
Berdasarkan analisis kode pada file `LYFG_BKM_RAWATTOP.txt`, masalah di mana karyawan tidak mendapatkan insentif saat bekerja di dua blok kemungkinan besar disebabkan oleh cara program melakukan **filter data** dan **perhitungan total upah** yang bersifat parsial (per baris/per RKH), bukan akumulatif untuk satu karyawan di semua blok.

Berikut adalah poin-poin penyebab teknisnya:

### 1. Filter Data Berdasarkan No RKH (Blok)

Program menggunakan sistem filter yang sangat ketat saat menampilkan atau memproses data karyawan. Ketika Anda memilih sebuah blok (No RKH), program melakukan filter pada tabel `gi_karyawan` hanya untuk RKH tersebut.

- Jika seorang karyawan bekerja di Blok A dan Blok B, maka saat Anda melihat Blok A, data di Blok B "tersembunyi" dari konteks perhitungan saat itu.
    

### 2. Perhitungan Insentif yang Bersifat Per Baris

Logika perhitungan upah (`calc_totalupah_hk`) dan simulasi (`simulate_calc`) bekerja dengan cara melakukan _looping_ pada baris yang sedang aktif atau berubah.

- Di dalam _loop_ tersebut, program memanggil `ycl_premi_bkm=>calc_premi_rawat` dengan mengirimkan `im_hasil_kerja` dan `im_hasil_pupuk` hanya dari baris tersebut.
    
- Program **tidak menjumlahkan** hasil kerja karyawan tersebut dari blok lain sebelum menghitung insentif. Akibatnya, jika hasil kerja di satu blok belum mencapai batas minimum (basis) untuk mendapatkan insentif, maka nilai `ex_premi_insentif` yang dikembalikan akan tetap 0.
    

### 3. Validasi Duplikasi yang Mencegah Input

Terdapat validasi yang mencegah input karyawan yang sama dalam satu nomor RKH yang sama. Namun, jika karyawan diinput di RKH (blok) yang berbeda, program mengizinkannya tetapi tetap menghitungnya sebagai entitas yang terpisah.

### 4. Risiko Pengosongan Upah (Status 'M')

Terdapat pengecekan status kerja lain melalui `ycl_premi_utility=>get_pernr_wc_other`.

- Jika karena bekerja di dua blok sistem mendeteksi adanya konflik status (misal status 'M'), maka kode secara eksplisit akan **mengnolkan semua upah dan insentif** karyawan tersebut: