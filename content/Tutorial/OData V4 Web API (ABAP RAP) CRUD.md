
Dokumentasi ini menjelaskan langkah demi langkah cara membuat OData Service menggunakan framework **RAP (ABAP RESTful Programming Model)**

 dengan tipe **Managed**. Guideline ini mencakup proses dari pembuatan CDS hingga service siap digunakan oleh aplikasi eksternal (mis. Node.js).

---
 ## 1. Membuat Data Definition (CDS View)

  
Langkah pertama adalah mendefinisikan struktur data. Agar dapat melakukan operasi **Create, Update, dan Delete**, CDS harus didefinisikan sebagai *root entity*.

  

```abap

@AccessControl.authorizationCheck: #NOT_REQUIRED

@EndUserText.label: 'Testing Development Crud'

  

define root view entity Z_TEST_CRUD

  as select from zta_test_crud

{

    key nik          as Nik,

    nama             as Nama,

    tempat_lahir     as TempatLahir

}

```

  

- **File**: `Z_TEST_CRUD`

- **Source table**: `zta_test_crud`

  
  

## 2. Membuat Behavior Definition (BDEF)

  

Behavior Definition menentukan operasi apa saja yang diizinkan pada entitas. Karena menggunakan tipe **Managed**, SAP akan mengurus operasi database secara otomatis.

  

### Aturan penting:

  

1. Gunakan opsi `strict (2)` untuk mengikuti standar terbaru.

2. Sertakan `implementation in class` untuk logika tambahan.

3. Tandai field kunci sebagai `readonly : update` agar tidak dapat diubah.

  

```abap

managed implementation in class zbp_test_crud unique;

strict ( 2 );

  

define behavior for Z_TEST_CRUD

persistent table zta_test_crud

lock master

authorization master ( instance )

{

  create;

  update;

  delete;

  

  " Key field tidak boleh diubah saat Update

  field ( readonly : update ) Nik;

  

  mapping for zta_test_crud {

    Nik = nik;

    Nama = nama;

    TempatLahir = tempat_lahir;

  }

}

```

  
  

## 3. Implementasi Behavior Pool (Class)

  

Karena menggunakan `authorization master (instance)` dalam mode `strict`, wajib membuat kelas implementasi untuk mencegah short dump. Contoh logika otorisasi sederhana untuk fase pengembangan:

  

```abap

CLASS lhc_Z_TEST_CRUD IMPLEMENTATION.

  METHOD get_instance_authorizations.

    DATA(lv_auth) = if_abap_behv=>auth-allowed.

  

    LOOP AT keys INTO DATA(ls_key).

      APPEND VALUE #( %tky = ls_key-%tky

                      %update = lv_auth

                      %delete = lv_auth ) TO result.

    ENDLOOP.

  ENDMETHOD.

ENDCLASS.

```

  
  

## 4. Membuat Service Definition (SRVD)

  

Service Definition digunakan untuk mengekspos entitas CDS ke luar.

  

```abap

@EndUserText.label: 'Testing Development CRUD'

define service ZS_TEST_CRUD {

  expose Z_TEST_CRUD;

}

```

  
  

## 5. Membuat Service Binding (SRVB)

  

Langkah terakhir adalah mendaftarkan service ke Gateway sehingga mendapatkan URL OData.

  

| Parameter | Nilai |

|-----------|-------|

| Name | ZUI_TEST_CRUD_V2 |

| Binding Type | ODATA V2 - UI |

| Status | Published |

  

### Proses:

  

1. Klik **Activate**

2. Klik **Publish** dan tunggu hingga muncul **Local Service Endpoint**

  

### Kesimpulan URL & Operasi

  

Setelah status *Published*, service siap digunakan:

  

- **Service URL**: `.../sap/opu/odata/sap/ZUI_TEST_CRUD_V2/`

  

Operasi CRUD:

  

| Aksi | Method | Endpoint | Keterangan |

|------|--------|----------|------------|

| Read (All) | GET | `/Z_TEST_CRUD` | Mengambil seluruh daftar data. |

| Read (Single) | GET | `/Z_TEST_CRUD('NIK')` | Mengambil satu data spesifik berdasarkan NIK. |

| Create | POST | `/Z_TEST_CRUD` | Menambah data baru ke tabel. |

| Update | MERGE / PUT | `/Z_TEST_CRUD('NIK')` | Mengubah data yang sudah ada berdasarkan NIK. |

| Delete | DELETE | `/Z_TEST_CRUD('NIK')` | Menghapus data berdasarkan NIK. |

  

- **GET (Read)**: Panggil langsung URL entitas

- **POST (Create)**: Diperlukan CSRF Token

- **PUT/MERGE (Update)**: Target ke key tertentu, contoh `Z_TEST_CRUD('123')`

- **DELETE**: Target ke key tertentu

  

---