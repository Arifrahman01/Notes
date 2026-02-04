```
*&---------------------------------------------------------------------*  
*& Include          ZIF_PM004_FO  
*&---------------------------------------------------------------------*  
  
FORM f_process_upload.  
  
  IF p_path IS INITIAL.  
    MESSAGE 'Silakan pilih file Excel terlebih dahulu.' TYPE 'S' DISPLAY LIKE 'E'.  
    RETURN.  
  ENDIF.  
  
  REFRESH: it_excel, lt_upload, lt_db.  
  
  lv_file_fm = p_path.  
  
  CALL FUNCTION 'TEXT_CONVERT_XLS_TO_SAP'  
    EXPORTING  
      i_line_header        = 'X'  
      i_tab_raw_data       = it_raw_data  
      i_filename           = lv_file_fm  
    TABLES  
      i_tab_converted_data = it_excel  
    EXCEPTIONS  
      conversion_failed    = 1  
      OTHERS               = 2.  
  
  IF sy-subrc <> 0.  
    MESSAGE 'Gagal mengonversi file Excel. Pastikan format sesuai.' TYPE 'S' DISPLAY LIKE 'E'.  
    RETURN.  
  ENDIF.  
  
  LOOP AT it_excel INTO wa_excel.  
    CLEAR: ls_upload, ls_db.  
  
    ls_upload-invnr    = wa_excel-invnr.  
    ls_upload-quantity = wa_excel-quantity.  
  
    " --- VALIDASI & KONVERSI TANGGAL ---  
    DATA: lv_date_raw TYPE string,  
          lv_date_chk TYPE d. " Wajib tipe D agar tidak dump di FM Plausibility  
  
    lv_date_raw = wa_excel-budat.  
    CONDENSE lv_date_raw NO-GAPS.  
  
    IF lv_date_raw IS INITIAL.  
      ls_upload-status  = '@0A@'.  
      ls_upload-message = 'Tanggal Posting kosong'.  
    ELSE.  
      " 1. Handling Format dengan garis miring (misal: 1/4/2026 atau 01/04/2026)  
      IF lv_date_raw CS '/'.  
        DATA: lt_split TYPE TABLE OF string.  
        SPLIT lv_date_raw AT '/' INTO TABLE lt_split.  
  
        IF lines( lt_split ) = 3.  
          " Susun ke YYYYMMDD (Asumsi format Excel: M/D/YYYY atau D/M/YYYY)  
          " lv_part1 = Bulan/Hari, lv_part2 = Hari/Bulan, lv_part3 = Tahun  
          DATA(lv_p1) = |{ lt_split[ 1 ] WIDTH = 2 ALIGN = RIGHT PAD = '0' }|.  
          DATA(lv_p2) = |{ lt_split[ 2 ] WIDTH = 2 ALIGN = RIGHT PAD = '0' }|.  
          DATA(lv_p3) = |{ lt_split[ 3 ] WIDTH = 4 ALIGN = RIGHT PAD = '0' }|.  
  
          " Gabungkan ke variabel string sementara  
          lv_date_raw = lv_p3 && lv_p1 && lv_p2.  
        ENDIF.  
      ENDIF.  
  
      " 2. Validasi Akhir (Panjang karakter & Plausibility)  
      IF strlen( lv_date_raw ) = 8 AND lv_date_raw CO '0123456789'.  
  
        " PINDAHKAN ke variabel tipe D untuk menghindari CX_SY_DYN_CALL_ILLEGAL_TYPE  
        lv_date_chk = lv_date_raw.  
  
        CALL FUNCTION 'DATE_CHECK_PLAUSIBILITY'  
          EXPORTING  
            date                      = lv_date_chk  
          EXCEPTIONS  
            plausibility_check_failed = 1  
            OTHERS                    = 2.  
  
        IF sy-subrc = 0.  
          ls_upload-budat = lv_date_chk.  
        ELSE.  
          ls_upload-status  = '@0A@'.  
          ls_upload-message = |Tanggal tidak valid: { wa_excel-budat }|.  
        ENDIF.  
      ELSE.  
        ls_upload-status  = '@0A@'.  
        ls_upload-message = |FORMAT terbaca: { lv_date_raw }. Gunakan YYYYMMDD|.  
      ENDIF.  
    ENDIF.  
  
    " --- VALIDASI DATABASE (Hanya jalan jika tanggal sudah OK) ---  
    IF ls_upload-status <> '@0A@'.  
      " Cek Master Data Equipment  
      SELECT SINGLE invnr FROM equi  
      INTO @DATA(lv_chk_eq)  
            WHERE invnr = @wa_excel-invnr.  
  
      IF sy-subrc <> 0.  
        ls_upload-status  = '@0A@'.  
        ls_upload-message = 'Inventory NO tidak ditemukan di Master DATA'.  
      ELSE.  
        " Cek Duplikasi di Tabel Custom  
        SELECT SINGLE invnr FROM zta_pm_rob  
        INTO @DATA(lv_chk_zt)  
              WHERE invnr = @wa_excel-invnr AND dltf <> 'X'.  
  
        IF sy-subrc = 0.  
          ls_upload-status  = '@0A@'.  
          ls_upload-message = 'DATA sudah ada di DATABASE'.  
        ELSE.  
          " Mapping data ke tabel database (lt_db)  
          MOVE-CORRESPONDING wa_excel TO ls_db.  
          ls_db-erdat = sy-datum.  
          ls_db-ernam = sy-uname.  
          ls_db-erzet = sy-uzeit.  
          ls_db-budat = ls_upload-budat.  
  
          APPEND ls_db TO lt_db.  
          ls_upload-status  = '@08@'. " Icon Green (Ready)  
          ls_upload-message = 'Ready TO Upload'.  
        ENDIF.  
      ENDIF.  
    ENDIF.  
  
    APPEND ls_upload TO lt_upload.  
  ENDLOOP.  
  
  TRY.  
      cl_salv_table=>factory(  
        IMPORTING r_salv_table = lo_alv  
        CHANGING  t_table      = lt_upload ).  
  
      DATA(lo_columns_upld) = lo_alv->get_columns( ).  
      lo_columns_upld->set_optimize( abap_true ).  
  
      " --- 1. Set Header Text ---  
      TRY.  
          lo_columns_upld->get_column( 'INVNR' )->set_short_text( 'InvNr' ).  
          lo_columns_upld->get_column( 'BUDAT' )->set_short_text( 'Date' ).  
          lo_columns_upld->get_column( 'QUANTITY' )->set_short_text( 'Qty' ).  
          lo_columns_upld->get_column( 'STATUS' )->set_short_text( 'Stat' ).  
          lo_columns_upld->get_column( 'MESSAGE' )->set_short_text( 'Message' ).  
        CATCH cx_salv_not_found.  
      ENDTRY.  
  
      " --- 2. Set GUI Status (Ganti PREVIEW jadi STATUS) ---  
      lo_alv->set_screen_status(  
        report        = sy-repid  
        pfstatus      = 'ZSALV_STATUS' " Gunakan status yang sudah ada  
        set_functions = lo_alv->c_functions_all ).  
  
  
      DATA(lo_functions) = lo_alv->get_functions( ).  
      TRY.  
          lo_functions->get_function( 'DELETE' )->set_visible( abap_false ).  
  
          lo_functions->get_function( 'POSTING' )->set_visible( abap_true ).  
        CATCH cx_salv_not_found.  
      ENDTRY.  
  
      " --- 4. Register Event ---  
      IF lo_event_handler IS NOT BOUND.  
        CREATE OBJECT lo_event_handler.  
      ENDIF.  
      SET HANDLER lo_event_handler->on_user_command FOR lo_alv->get_event( ).  
  
      " --- 5. Tampilkan ---  
      lo_alv->display( ).  
  
    CATCH cx_salv_msg INTO DATA(lx_msg).  
      MESSAGE lx_msg->get_text( ) TYPE 'E'.  
  ENDTRY.  
  
ENDFORM.
```