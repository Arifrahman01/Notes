1. Sebelum Looping
```
  DATA(lv_lines_calc) = lines( lt_zta_hc_101a ).
```
2. Di dalam Looping
```
lv_tabix = sy-tabix.

    "Form routine for Progress Bar
    PERFORM progress_bar USING 'Calculating Premi....'(003)
                                lv_tabix
                                lv_lines_calc.
```
