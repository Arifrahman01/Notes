```
*&---------------------------------------------------------------------*  
*& Include          ZIN_PM011_f  
*&---------------------------------------------------------------------*  
  
CLASS cl_event_handler DEFINITION.  
  PUBLIC SECTION.  
    DATA: ld_simu_flg TYPE c.  
    DATA: ls_api  TYPE REF TO if_salv_gui_om_extend_grid_api,  
          ls_edit TYPE REF TO if_salv_gui_om_edit_restricted.  
  
    METHODS:  
      on_user_command FOR EVENT added_function OF cl_salv_events  
        IMPORTING e_salv_function.  
*      on_link_click FOR EVENT link_click OF cl_salv_events_table  
*        IMPORTING row column.  
  
ENDCLASS.  
  
CLASS cl_event_handler IMPLEMENTATION.  
  METHOD: on_user_command.  
    o_alv->refresh( ).  
  ENDMETHOD.  
ENDCLASS.  
FORM display.  
  DATA: lo_events        TYPE REF TO cl_salv_events_table,  
        lo_event_handler TYPE REF TO cl_event_handler.  
  DATA: lv_total   TYPE zpp_value,  
        lo_layout  TYPE REF TO cl_salv_layout,  
        lf_variant TYPE slis_vari,  
        ls_key     TYPE salv_s_layout_key..  
  
  IF p_stat_2 IS NOT INITIAL.  
    SELECT * FROM zta_cs_101 INTO TABLE @DATA(li_zta_cs_101_s)  
          WHERE doc_status = 'ACTV'  
          AND trip_start NE ''  
          AND posting_date IN @s_pdate  
          AND swerk IN @s_werks  
          AND main_invnr IN @s_minvnr.  
*          AND trip_num IN @s_tnum  
*          AND period = @p_perio.  
    IF li_zta_cs_101_s[] IS NOT INITIAL.  
      SELECT * FROM zta_cs_101 INTO TABLE @DATA(li_zta_cs_101_e)  
            FOR ALL ENTRIES IN @li_zta_cs_101_s  
            WHERE "doc_status = 'ACTVT'  
            trip_code = @li_zta_cs_101_s-trip_code  
            AND trip_end NE ''.  
      SELECT * FROM zta_cs_003 INTO TABLE @DATA(li_zta_cs_003).  
    ENDIF.  
  
    LOOP AT li_zta_cs_101_e INTO DATA(lw_zta_cs_101_e).  
      CLEAR gw_data.  
      gw_data-plant = lw_zta_cs_101_e-swerk.  
      gw_data-ship_number = lw_zta_cs_101_e-main_invnr.  
      gw_data-trip_period = lw_zta_cs_101_e-period.  
      gw_data-trip_number = lw_zta_cs_101_e-trip_num.  
      gw_data-shipping_instruction = lw_zta_cs_101_e-sidoc.  
*      SELECT SINGLE d~bstkd INTO @DATA(ld_bstkd)    "edit by mahfud 19/07/2025  
*        FROM aufk AS a  
*        LEFT JOIN afih AS b ON a~aufnr = b~aufnr  
*        LEFT JOIN zta_cs_101 AS c ON a~ztrpnr = c~trip_num AND b~equnr = c~main_equnr  
*        LEFT JOIN pmsdo AS d ON a~objnr = d~objnr  
*        WHERE a~ztrpnr = @lw_zta_cs_101_e-trip_num  
*          AND b~equnr = @lw_zta_cs_101_e-main_equnr  
*          AND c~timesheet_num = @lw_zta_cs_101_e-timesheet_num  
*          AND c~main_equnr = @lw_zta_cs_101_e-main_equnr  
*          AND c~trip_num = @lw_zta_cs_101_e-trip_num.  
*  
*      gw_data-shipping_instruction = ld_bstkd.  
  
      READ TABLE li_zta_cs_101_s INTO DATA(lw_zta_cs_101_s) WITH KEY  
            trip_code = lw_zta_cs_101_e-trip_code.  
      IF sy-subrc = 0.  
        gw_data-start_date = lw_zta_cs_101_s-posting_date.  
      ENDIF.  
      gw_data-end_date = lw_zta_cs_101_e-posting_date.  
      READ TABLE li_zta_cs_003 INTO DATA(lw_start_point) "gw_data2-location  
            WITH KEY opt = lw_zta_cs_101_e-start_point.  
      IF sy-subrc = 0.  
        gw_data-start_point = lw_start_point-value1.  
      ENDIF.  
      READ TABLE li_zta_cs_003 INTO DATA(lw_end_point) "gw_data2-location  
            WITH KEY opt = lw_zta_cs_101_e-end_point.  
      IF sy-subrc = 0.  
        gw_data-end_point = lw_end_point-value1.  
      ENDIF.  
*      gw_data-start_point = lw_zta_cs_101_e-start_point.  
*      gw_data-end_point = lw_zta_cs_101_e-end_point.  
      gw_data-premi_code = lw_zta_cs_101_e-premi_code.   "add by mahfud 02/07/2025  
*      SELECT SINGLE nama_premi INTO @DATA(ld_nama_premi)  
*        FROM zta_hc_001  
*        WHERE bukrs = @lw_zta_cs_101_e-bukrs AND premi_code = @lw_zta_cs_101_e-premi_code.  
*      gw_data-nama_premi = ld_nama_premi.  
      gw_data-distance = lw_zta_cs_101_e-trip_distance.  
      gw_data-uom = lw_zta_cs_101_e-trip_uom.  
      gw_data-fuel_consumption = lw_zta_cs_101_e-fuel_con.  
      gw_data-running_me_1 = lw_zta_cs_101_e-engine1_run.  
      gw_data-running_me_2 = lw_zta_cs_101_e-engine2_run.  
      gw_data-running_ae_1 = lw_zta_cs_101_e-engine3_run.  
      gw_data-running_ae_2 = lw_zta_cs_101_e-engine4_run.  
      gw_data-running_ae_3 = lw_zta_cs_101_e-engine5_run.  
*  
      APPEND gw_data TO gi_data.  
*      CLEAR ld_nama_premi.  
*      CLEAR: ld_bstkd.  
    ENDLOOP.  
  ELSE.  
  
    SELECT * FROM zta_cs_101  
    INTO TABLE @DATA(li_zta_cs_101_d)  
          WHERE doc_status = 'ACTV'  
*    AND trip_start NE ''  
          AND posting_date IN @s_pdate  
          AND swerk IN @s_werks  
          AND main_invnr IN @s_minvnr  
*          AND trip_num IN @s_tnum  
*          AND period = @p_perio  
          AND timesheet_num IN @s_time.  
    IF sy-subrc = 0.  
      SELECT * FROM zta_cs_102 INTO TABLE @DATA(li_zta_cs_102)  
            FOR ALL ENTRIES IN @li_zta_cs_101_d  
            WHERE timesheet_num = @li_zta_cs_101_d-timesheet_num.  
      SELECT * FROM zta_cs_003 INTO TABLE li_zta_cs_003.  
    ENDIF.  
*  FOR ALL ENTRIES IN @li_zta_cs_102  
*  WHERE opt = @li_zta_cs_102-work_location  
*     OR opt = @li_zta_cs_102-act_code  
*     OR opt = @li_zta_cs_102-work_code  
*     OR opt = @li_zta_cs_102-cargo_code  
*     OR opt = @li_zta_cs_102-cargo_code.  
    LOOP AT li_zta_cs_101_d INTO DATA(lw_zta_cs_101_d).  
      CLEAR gw_data2.  
      gw_data2-timesheet_num = lw_zta_cs_101_d-timesheet_num.  
      gw_data2-plant = lw_zta_cs_101_d-swerk.  
      gw_data2-ship_number = lw_zta_cs_101_d-main_invnr.  
      gw_data2-trip_period = lw_zta_cs_101_d-period.  
      gw_data2-trip_number = lw_zta_cs_101_d-trip_num.  
      gw_data2-posting_date = lw_zta_cs_101_d-posting_date.  
      gw_data2-shipping_instruction = lw_zta_cs_101_d-sidoc.  
*         add by Arif 01-09-25 add trip  
      gw_data2-trip_start = lw_zta_cs_101_d-trip_start.  
      gw_data2-trip_end = lw_zta_cs_101_d-trip_end.  
*      SELECT SINGLE d~bstkd INTO @DATA(ld_bstkd2) "edit by mahfud 19/07/2025  
*        FROM aufk AS a  
*        LEFT JOIN afih AS b ON a~aufnr = b~aufnr  
*        LEFT JOIN zta_cs_101 AS c ON a~ztrpnr = c~trip_num AND b~equnr = c~main_equnr  
*        LEFT JOIN pmsdo AS d ON a~objnr = d~objnr  
*        WHERE a~ztrpnr = @lw_zta_cs_101_d-trip_num  
*          AND b~equnr = @lw_zta_cs_101_d-main_equnr  
*          AND c~timesheet_num = @lw_zta_cs_101_d-timesheet_num  
*          AND c~main_equnr = @lw_zta_cs_101_d-main_equnr  
*          AND c~trip_num = @lw_zta_cs_101_d-trip_num.  
*  
*      gw_data2-shipping_instruction = ld_bstkd2.  
      gw_data2-premi_code = lw_zta_cs_101_d-premi_code.   "add by mahfud 02/07/2025  
*      SELECT SINGLE nama_premi INTO @DATA(ld_nama_premi2)  
*        FROM zta_hc_001  
*        WHERE bukrs = @lw_zta_cs_101_d-bukrs AND premi_code = @lw_zta_cs_101_d-premi_code.  
*      gw_data2-nama_premi = ld_nama_premi2.  
*      gw_data2-start_point = lw_zta_cs_101_d-start_point.  
*      gw_data2-end_point = lw_zta_cs_101_d-end_point.  
      LOOP AT li_zta_cs_102 INTO DATA(lw_zta_cs_102)  
            WHERE timesheet_num = lw_zta_cs_101_d-timesheet_num.  
*      READ TABLE li_zta_cs_102 INTO DATA(lw_zta_cs_102)  
*        WITH KEY timesheet_num = lw_zta_cs_101_d-timesheet_num.  
        READ TABLE li_zta_cs_003 INTO lw_start_point "gw_data2-location  
        WITH KEY opt = lw_zta_cs_101_d-start_point.  
        IF sy-subrc = 0.  
          gw_data2-start_point = lw_start_point-value1.  
        ENDIF.  
        READ TABLE li_zta_cs_003 INTO lw_end_point "gw_data2-location  
        WITH KEY opt = lw_zta_cs_101_d-end_point.  
        IF sy-subrc = 0.  
          gw_data2-end_point = lw_end_point-value1.  
        ENDIF.  
        READ TABLE li_zta_cs_003 INTO DATA(lw_location) "gw_data2-location  
              WITH KEY opt = lw_zta_cs_102-work_location.  
        IF sy-subrc = 0.  
          gw_data2-location = lw_location-value1.  
        ENDIF.  
        READ TABLE li_zta_cs_003 INTO DATA(lw_activity) "gw_data2-activity  
              WITH KEY opt = lw_zta_cs_102-act_code.  
        IF sy-subrc = 0.  
          gw_data2-activity = lw_activity-value1.  
        ENDIF.  
        READ TABLE li_zta_cs_003 INTO DATA(lw_working) "gw_data2-working  
              WITH KEY opt = lw_zta_cs_102-work_code.  
        IF sy-subrc = 0.  
          gw_data2-working = lw_working-value1.  
        ENDIF.  
        gw_data2-start_time = lw_zta_cs_102-start_time.  
        gw_data2-end_time = lw_zta_cs_102-end_time.  
        gw_data2-barge = lw_zta_cs_102-sec_invnr.  
  
        lv_start_sec = ( lw_zta_cs_102-start_time(2) * 3600 ) +       "add Helmy 26/8  
        ( lw_zta_cs_102-start_time+2(2) * 60 ).  
  
        lv_end_sec = ( lw_zta_cs_102-end_time(2) * 3600 ) +  
        ( lw_zta_cs_102-end_time+2(2) * 60 ).  
  
        lv_diff_sec = lv_end_sec - lv_start_sec.  
  
        lv_hours = lv_diff_sec / 3600.  
  
        gw_data2-tot_time = lv_hours.  
        READ TABLE li_zta_cs_003 INTO DATA(lw_cargo) "gw_data2-cargo  
              WITH KEY opt = lw_zta_cs_102-cargo_code.  
        IF sy-subrc = 0.  
          gw_data2-cargo = lw_cargo-value1.  
        ENDIF.  
  
        DATA lv_period(10).  
  
        CLEAR lv_period.  
        lv_period = lw_zta_cs_101_d-period.  
        " Tambah leading zero kalau perlu  
        IF strlen( lv_period ) = 5.  
          lv_period = |0{ lv_period }|.  
        ENDIF.  
        IF strlen( lv_period ) = 6.  
          lv_period = lv_period(2) && '.' && lv_period+2(4).  
        ENDIF.  
  
        SELECT SINGLE a~aufnr INTO @DATA(ld_aufnr)  
              FROM aufk AS a  
              LEFT JOIN afih AS b ON a~aufnr = b~aufnr  
              LEFT JOIN zta_cs_101 AS c ON a~ztrpnr = c~trip_num AND a~zperiod = @lv_period  
              AND b~equnr = c~main_equnr  
              WHERE a~ztrpnr = @lw_zta_cs_101_d-trip_num  
              AND b~equnr = @lw_zta_cs_101_d-main_equnr  
              AND c~timesheet_num = @lw_zta_cs_101_d-timesheet_num  
              AND c~main_equnr = @lw_zta_cs_101_d-main_equnr  
              AND c~period = @lw_zta_cs_101_d-period  
              AND c~trip_num = @lw_zta_cs_101_d-trip_num.  
  
        IF ld_aufnr IS NOT INITIAL.  
          SELECT SINGLE a~rsnum INTO @DATA(ld_rsnum)  
                FROM resb AS a  
                WHERE a~aufnr = @ld_aufnr.  
        ENDIF.  
  
        gw_data2-cs_order = ld_aufnr.  "lw_zta_cs_102-cs_order.  
        gw_data2-barge_trip = lw_zta_cs_102-barge_trip.  
        gw_data2-qty = lw_zta_cs_102-cargo_qty.  
        gw_data2-uom = lw_zta_cs_102-cargo_uom.  
        gw_data2-avg_speed = lw_zta_cs_102-avg_speed.  
        gw_data2-remarks = lw_zta_cs_102-remarks.  
        gw_data2-rsnum  = ld_rsnum.  
        SELECT SINGLE * FROM zta_cs_103 INTO @DATA(lw_zta_cs_103)  
              WHERE timesheet_num = @lw_zta_cs_101_d-timesheet_num  
              AND delete_flag = ''.  
        IF sy-subrc = 0.  
          gw_data2-indicator_crew = 'X'.  
        ELSE.  
          gw_data2-indicator_crew = ''.  
        ENDIF.  
        APPEND gw_data2 TO gi_data2.  
      ENDLOOP.  
      CLEAR: ld_aufnr.", ld_bstkd2.  
      CLEAR: ld_rsnum.  
    ENDLOOP.  
  
  ENDIF.  
  
  IF p_stat_1 IS NOT INITIAL.  
*    LOOP AT gi_data INTO gw_data.  
*      MOVE-CORRESPONDING gw_data TO gw_data2.  
*      APPEND gw_data2 TO gi_data2.  
*    ENDLOOP.  
    IF gi_data2[] IS INITIAL.  
      MESSAGE 'DATA NOT Found' TYPE 'E'.  
    ENDIF.  
    TRY.  
        cl_salv_table=>factory(  
        IMPORTING  
          r_salv_table = o_alv  
        CHANGING  
          t_table      = gi_data2 ).  
  
      CATCH cx_salv_msg INTO lx_msg.  
    ENDTRY.  
    columns = o_alv->get_columns( ).  
    PERFORM change_column_name2.  
  ELSE.  
    IF gi_data[] IS INITIAL.  
      MESSAGE 'DATA NOT Found' TYPE 'E'.  
    ENDIF.  
    TRY.  
        cl_salv_table=>factory(  
        IMPORTING  
          r_salv_table = o_alv  
        CHANGING  
          t_table      = gi_data ).  
  
      CATCH cx_salv_msg INTO lx_msg.  
    ENDTRY.  
    columns = o_alv->get_columns( ).  
    PERFORM change_column_name.  
  ENDIF.  
  
*  columns = o_alv->get_columns( ).  
*  PERFORM change_column_name.  
  PERFORM optimize_column_width.  
*---Get Event.  
  lo_events = o_alv->get_event( ).  
  
  CREATE OBJECT lo_event_handler.  
  SET HANDLER lo_event_handler->on_user_command FOR lo_events.  
*  SET HANDLER lo_event_handler->on_link_click FOR lo_events.  
  
  lo_layout = o_alv->get_layout( ).  
*  
*   set Layout save restriction  
*   1. Set Layout Key .. Unique key identifies the Differenet ALVs  
  ls_key-report = sy-repid.  
  lo_layout->set_key( ls_key ).  
*   2. Remove Save layout the restriction.  
  lo_layout->set_save_restriction( if_salv_c_layout=>restrict_none ).  
*  
*   set initial Layout  
  lf_variant = 'DEFAULT'.  
  lo_layout->set_initial_layout( lf_variant ).  
  
  o_alv->set_screen_status(  
  pfstatus = 'ZSTANDARD'  
  report   = sy-repid  
  set_functions = o_alv->c_functions_all ).  
  
  o_alv->display( ).  
ENDFORM.  
  
FORM change_column_name.  
  " cambia el nombre a una columna...  
  DATA not_found TYPE REF TO cx_salv_not_found.  
  TRY.  
*      column = columns->get_column( 'POSTING_DATE' ).  
*      column->set_short_text( 'Pstng Date' ).  
*      column->set_medium_text( 'Posting Date' ).  
*      column->set_long_text( 'Posting Date' ).  
      column = columns->get_column( 'PLANT' ).  
      column->set_short_text( 'Plant' ).  
      column->set_medium_text( 'Plant' ).  
      column->set_long_text( 'Plant' ).  
      column = columns->get_column( 'SHIP_NUMBER' ).  
      column->set_short_text( 'Ship Num' ).  
      column->set_medium_text( 'Ship NUMBER' ).  
      column->set_long_text( 'Ship NUMBER' ).  
      column = columns->get_column( 'TRIP_PERIOD' ).  
      column->set_short_text( 'Period' ).  
      column->set_medium_text( 'Trip Period' ).  
      column->set_long_text( 'Trip Period' ).  
      column = columns->get_column( 'TRIP_NUMBER' ).  
      column->set_short_text( 'ShipTNum' ).  
      column->set_medium_text( 'ShipTripNumber' ).  
      column->set_long_text( 'Ship Trip NUMBER' ).  
      column = columns->get_column( 'SHIPPING_INSTRUCTION' ).  
      column->set_short_text( 'Shipping' ).  
      column->set_medium_text( 'Shipping Instruction' ).  
      column->set_long_text( 'Shipping Instruction' ).  
      column = columns->get_column( 'START_DATE' ).  
      column->set_short_text( 'Start DATE' ).  
      column->set_medium_text( 'Start DATE' ).  
      column->set_long_text( 'Start DATE' ).  
      column = columns->get_column( 'END_DATE' ).  
      column->set_short_text( 'END DATE' ).  
      column->set_medium_text( 'END DATE' ).  
      column->set_long_text( 'END DATE' ).  
      column = columns->get_column( 'START_POINT' ).  
      column->set_short_text( 'Start P' ).  
      column->set_medium_text( 'Start Point' ).  
      column->set_long_text( 'Start Point' ).  
      column = columns->get_column( 'END_POINT' ).  
      column->set_short_text( 'END Point' ).  
      column->set_medium_text( 'END Point' ).  
      column->set_long_text( 'END Point' ).  
      column = columns->get_column( 'DISTANCE' ).  
      column->set_short_text( 'Trip Dist' ).  
      column->set_medium_text( 'Trip Distance' ).  
      column->set_long_text( 'Trip Distance' ).  
      column = columns->get_column( 'PREMI_CODE' ).  
      column->set_short_text( 'PremiCode' ).  
      column->set_medium_text( 'Premi CODE' ).  
      column->set_long_text( 'Premi CODE' ).  
*      column = columns->get_column( 'NAMA_PREMI' ).  
*      column->set_short_text( 'PremiName' ).  
*      column->set_medium_text( 'Premi Name' ).  
*      column->set_long_text( 'Premi Name' ).  
      column = columns->get_column( 'UOM' ).  
      column->set_short_text( 'Trip UoM' ).  
      column->set_medium_text( 'Trip UoM' ).  
      column->set_long_text( 'Trip UoM' ).  
      column = columns->get_column( 'FUEL_CONSUMPTION' ).  
      column->set_short_text( 'Fuel Cons' ).  
      column->set_medium_text( 'Fuel Consumption' ).  
      column->set_long_text( 'Fuel Consumption' ).  
      column = columns->get_column( 'RUNNING_ME_1' ).  
      column->set_short_text( 'RUN ME 1' ).  
      column->set_medium_text( 'Running Me 1' ).  
      column->set_long_text( 'Running Me 1' ).  
      column = columns->get_column( 'RUNNING_ME_2' ).  
      column->set_short_text( 'RUN ME 2' ).  
      column->set_medium_text( 'Running Me 2' ).  
      column->set_long_text( 'Running Me 2' ).  
      column = columns->get_column( 'RUNNING_AE_1' ).  
      column->set_short_text( 'RUN AE 1' ).  
      column->set_medium_text( 'Running AE 1' ).  
      column->set_long_text( 'Running AE 1' ).  
      column = columns->get_column( 'RUNNING_AE_2' ).  
      column->set_short_text( 'RUN AE 2' ).  
      column->set_medium_text( 'Running AE 2' ).  
      column->set_long_text( 'Running AE 2' ).  
      column = columns->get_column( 'RUNNING_AE_3' ).  
      column->set_short_text( 'RUN AE 3' ).  
      column->set_medium_text( 'Running AE 3' ).  
      column->set_long_text( 'Running AE 3' ).  
    CATCH cx_salv_not_found INTO not_found.  
      " error handling  
  ENDTRY.  
ENDFORM.  
  
FORM change_column_name2.  
  " cambia el nombre a una columna...  
  DATA not_found TYPE REF TO cx_salv_not_found.  
  TRY.  
      column = columns->get_column( 'TIMESHEET_NUM' ).  
      column->set_short_text( 'Tmsht Num' ).  
      column->set_medium_text( 'Timesheet Num' ).  
      column->set_long_text( 'Timesheet Num' ).  
      column = columns->get_column( 'PLANT' ).  
      column->set_short_text( 'Plant' ).  
      column->set_medium_text( 'Plant' ).  
      column->set_long_text( 'Plant' ).  
      column = columns->get_column( 'SHIP_NUMBER' ).  
      column->set_short_text( 'Ship Num' ).  
      column->set_medium_text( 'Ship NUMBER' ).  
      column->set_long_text( 'Ship NUMBER' ).  
      column = columns->get_column( 'TRIP_PERIOD' ).  
      column->set_short_text( 'Period' ).  
      column->set_medium_text( 'Trip Period' ).  
      column->set_long_text( 'Trip Period' ).  
      column = columns->get_column( 'TRIP_NUMBER' ).  
      column->set_short_text( 'ShipTNum' ).  
      column->set_medium_text( 'ShipTripNumber' ).  
      column->set_long_text( 'Ship Trip NUMBER' ).  
  
      column = columns->get_column( 'RSNUM' ).  
  
      column = columns->get_column( 'POSTING_DATE' ).  
      column->set_short_text( 'Posting Dt' ).  
      column->set_medium_text( 'Posting DATE' ).  
      column->set_long_text( 'Posting DATE' ).  
      column = columns->get_column( 'SHIPPING_INSTRUCTION' ).  
      column->set_short_text( 'Shipping' ).  
      column->set_medium_text( 'Shipping Instruction' ).  
      column->set_long_text( 'Shipping Instruction' ).  
      column = columns->get_column( 'START_POINT' ).  
      column->set_short_text( 'Start P' ).  
      column->set_medium_text( 'Start Point' ).  
      column->set_long_text( 'Start Point' ).  
      column = columns->get_column( 'END_POINT' ).  
      column->set_short_text( 'END Point' ).  
      column->set_medium_text( 'END Point' ).  
      column->set_long_text( 'END Point' ).  
      column = columns->get_column( 'LOCATION' ).  
      column->set_short_text( 'Location' ).  
      column->set_medium_text( 'Location' ).  
      column->set_long_text( 'Location' ).  
      column = columns->get_column( 'START_TIME' ).  
      column->set_short_text( 'Start T' ).  
      column->set_medium_text( 'Start TIME' ).  
      column->set_long_text( 'Start TIME' ).  
      column = columns->get_column( 'END_TIME' ).  
      column->set_short_text( 'END TIME' ).  
      column->set_medium_text( 'END TIME' ).  
      column->set_long_text( 'END TIME' ).  
      column = columns->get_column( 'TOT_TIME' ).  
      column->set_short_text( 'Total TIME' ).  
      column->set_medium_text( 'Total TIME' ).  
      column->set_long_text( 'Total TIME' ).  
      column = columns->get_column( 'ACTIVITY' ).  
      column->set_short_text( 'Activity' ).  
      column->set_medium_text( 'Activity' ).  
      column->set_long_text( 'Activity' ).  
      column = columns->get_column( 'PREMI_CODE' ).  
      column->set_short_text( 'PremiCode' ).  
      column->set_medium_text( 'Premi CODE' ).  
      column->set_long_text( 'Premi CODE' ).  
*      column = columns->get_column( 'NAMA_PREMI' ).  
*      column->set_short_text( 'PremiName' ).  
*      column->set_medium_text( 'Premi Name' ).  
*      column->set_long_text( 'Premi Name' ).  
      column = columns->get_column( 'WORKING' ).  
      column->set_short_text( 'Working' ).  
      column->set_medium_text( 'Working' ).  
      column->set_long_text( 'Working' ).  
      column = columns->get_column( 'BARGE' ).  
      column->set_short_text( 'Barge' ).  
      column->set_medium_text( 'Barge' ).  
      column->set_long_text( 'Barge' ).  
      column = columns->get_column( 'CARGO' ).  
      column->set_short_text( 'Cargo' ).  
      column->set_medium_text( 'Cargo' ).  
      column->set_long_text( 'Cargo' ).  
      column = columns->get_column( 'CS_ORDER' ).  
      column->set_short_text( 'CS ORDER' ).  
      column->set_medium_text( 'CS ORDER' ).  
      column->set_long_text( 'CS ORDER' ).  
      column = columns->get_column( 'BARGE_TRIP' ).  
      column->set_short_text( 'BargeTrip' ).  
      column->set_medium_text( 'Barge Trip' ).  
      column->set_long_text( 'Barge Trip' ).  
      column = columns->get_column( 'QTY' ).  
      column->set_short_text( 'Quantity' ).  
      column->set_medium_text( 'Quantity' ).  
      column->set_long_text( 'Quantity' ).  
      column = columns->get_column( 'UOM' ).  
      column->set_short_text( 'UoM' ).  
      column->set_medium_text( 'UoM' ).  
      column->set_long_text( 'UoM' ).  
      column = columns->get_column( 'AVG_SPEED' ).  
      column->set_short_text( 'AVG Speed' ).  
      column->set_medium_text( 'AVG Speed(Knot)' ).  
      column->set_long_text( 'AVG Speed(Knot)' ).  
      column = columns->get_column( 'REMARKS' ).  
      column->set_short_text( 'Act Remark' ).  
      column->set_medium_text( 'Activity Remarks' ).  
      column->set_long_text( 'Activity Remarks' ).  
      column = columns->get_column( 'INDICATOR_CREW' ).  
      column->set_short_text( 'Ind: Crew' ).  
      column->set_medium_text( 'Indicator: Crew List' ).  
      column->set_long_text( 'Indicator: Crew List' ).  
  
      column = columns->get_column( 'TRIP_START' ).  
      column->set_short_text( 'TripStart' ).  
      column->set_medium_text( 'Trip Start' ).  
      column->set_long_text( 'Trip Start' ).  
  
      column = columns->get_column( 'TRIP_END' ).  
      column->set_short_text( 'TripEnd' ).  
      column->set_medium_text( 'Trip END' ).  
      column->set_long_text( 'Trip END' ).  
    CATCH cx_salv_not_found INTO not_found.  
      " error handling  
  ENDTRY.  
ENDFORM.  
  
FORM optimize_column_width.  
  columns->set_optimize( ).  
ENDFORM.
```