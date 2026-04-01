---
title: 📥 Membuat Download Template
layout: layout
---

# 📥 Membuat Download Template

## Menyimpan Binary Data (Template/Dokumen)

### Cara menyimpan dokumen dalam SAP:

1. Gunakan transaksi **SMW0** untuk mengelola Binary Data
2. Pilih **Create** untuk menambah dokumen baru
3. Upload file template (Excel, PDF, dll)
4. Catat **Object Name** yang diberikan sistem
5. Gunakan Object Name ini dalam program ABAP untuk download

### Menggunakan di ABAP:

```abap
* Retrieve dokumen dari Binary Data
FUNCTION 'DOWNLOAD_WEB_OBJECT'
  USING
    object_name = 'NAMA_DOKUMEN'
    ...
```
