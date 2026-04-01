---
title: ⏳ Progress Bar Implementation
layout: layout
---

# ⏳ Progress Bar Implementation

## Cara Mengimplementasikan Progress Bar di ABAP

### 1️⃣ Sebelum Looping - Hitung Total Records

```abap
DATA(lv_lines_calc) = lines( lt_zta_hc_101a ).
```

### 2️⃣ Di Dalam Looping - Display Progress

```abap
lv_tabix = sy-tabix.

"Form routine untuk menampilkan Progress Bar
PERFORM progress_bar USING 'Calculating Premi....'(003)
                            lv_tabix
                            lv_lines_calc.
```

> [!NOTE] Tips
> `sy-tabix` adalah index baris saat ini dalam looping loop, gunakan ini untuk tracking progress secara real-time.

