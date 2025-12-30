# FSA
#Progress  

- [x]  “Nomor FSA” menjadi “FSA Number”
- [x] Isian “Destination” tolong masukkan negara-negara di bawah ini: 
	- Indonesia 
	- China  
	- India 
	- Cambodia 
	- Malaysia 
	- Myanmar 
	- Philippines 
	- Taiwan 
	- Thailand 
	- Vietnam 
	- Others
- [x] “Note Quantity” menjadi “Quantity Note” dan “Note (Catatan)” menjadi “Additional Note”
- [x] Untuk Unit Price dengan Premium/discount pilihan FLAT, maka calculation otomatis 0 dan tidak dapat diubah
- [x] Fungsi “Add to detail” dan “Create & create another” ditake out saja
---
## Contract
- [x] “Approval” menjadi “Waiting approval”
- [ ] Status angka di sini berbeda-beda. Untuk IJ, maka angka yang tampil Adalah “Waiting approval” Adapun untuk user marketing adalah semua kontrak yang belum active kecuali “Waiting approval”, “Terminated”, dan “Complete.” Karena tujjuan dari notification di situ adalah untuk menandakan task yang belum selesai (mirip dengan logika unread message pada app email).
- [ ] Menunggu full function. Tapi sejauh ini, apabila sudah diklik, harusnya pop up auto close.
- [ ] Perlu ditambahkan data soal quantity dari FSA sebagai referensi
- [x] Draft document mandatory
- [x] Tombol “Attach Draft” harusnya menampilkan file yang terakhir diupload (misal, “Attached file:abcde.pdf” Karena sebelumnya saya sudah upload file tersebut di halaman utama.
- [x] Pada dialog “Approve Draft,” menjadi seperti ini:
				Approve draft?  
		Are you sure to approve this draft contract? 
			Cancel                  Yes, Approve
- [x] Dibuat Namanya Attachment Control untuk menggantikan tombol Attachment yang berubah2. Sebagai berikut. Dengan perubahan Judul: 
	- Draft Document -> Draft Contract 
	- Approval Document -> Approved Draft Contract 
	- Approval Both Signed -> Both Signed Contract
- [ ] Opsi revisi hanya ada pada status “Draft Contract.” Setelah itu tidak ada revisi.
---
# Laycan
- [x] Judul Modul “Laycan Bookings” ->  “Laycan Booking” 
- [ ] Fitur search yang sama seperti pada Modul Contract saat user mencari FSA
- [x] “Type of vessel” ->  Vessel Type 
- [x] Column list harusnya seperti ini (dari kiri ke kanan) 
	- a. Laycan Date 
	- b. Buyer 
	- c. Seller 
	- d. Contract No. 
	- e. Vessel Type 
	- f. Loading Port
- [x] List laycan otomatis di-sort berdasarkan hari pertama laycan date dari earliest to latest. Namun user juga bisa set A-Z atau Z-A
- [x] Data perlu difiltrasi berdasarkan Loading Port, mirip seperti modul Vessel Operations.
- [x] Status “Vessel Nominate”  ->  “Vessel Nominated” 
- [x] Status “Await”  ->  “Contract Waiting”
- [x] Harusnya ada input “Loading Port” di bagian Contract Await
- [ ] Perlu punya ringkasan di atas seperti Modul Operations yang isinya jumlah volume terhitung berdasarkan laycan yang sudah di-confirm. Tidak perlu ada persentase karena tidak ada pembaginya. Cukup penjumlahan dari data “Lifting Volume (MT)” pada modul tersebut.
---
# Vessel Operation
- [x] Sama seperti Nomor (5) pada Modul Laycan Bookings. Harus ada filter berdasarkan loading port 
- [x] Sama seperti Nomor (3) dan Nomor (4) Modul Laycan Bookings 
- [x] Harusnya ada data “Cargo Volume” yakni jumlah kargo yang mau diisi berdasarkan isian di Laycan Booking (misal 70,000) serta “Loaded Volume” yakni volume kargo yang telah dialokasikan sejauh ini.
- [x] “Lifting Volume” -> “Loaded Volume” 
- [x] “Laycan Quantity" -> “Cargo Volume”

---
