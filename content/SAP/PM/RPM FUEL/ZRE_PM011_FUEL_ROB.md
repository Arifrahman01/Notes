```
*&---------------------------------------------------------------------*  
*&  zre_pm011_fuel_rob  
*&---------------------------------------------------------------------*  
*& Developer   : Arif Rahman  
*& Date        : 03.02.2026  
*& Description : Report Fuel Remain ON Board  
*& Author      : M. Rizki Ansyari & Tri Saputra Wijaya  
*& Note        : COPY From ZCS002  
*&---------------------------------------------------------------------*  
REPORT zre_pm011_fuel_rob.  
  
INCLUDE ZIN_PM011_TOP.  
INCLUDE ZIN_PM011_F.  
  
  
  
  
DEFINE mac_value_sh.  
  REFRESH: &1.  
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'  
  EXPORTING  
    retfield        = &2  
    dynpprog        = sy-repid  
    dynpnr          = sy-dynnr  
    dynprofield     = &3  
    value_org       = 'S'  
    multiple_choice = ' '  
  TABLES  
    value_tab       = &4  
    return_tab      = &1  
  EXCEPTIONS  
    parameter_error = 1  
    no_values_found = 2  
    OTHERS          = 3.  
END-OF-DEFINITION.  
  
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_var.  
  CLEAR g_variant.  
  g_variant-report = sy-repid.  
  
  CALL FUNCTION 'REUSE_ALV_VARIANT_F4'  
    EXPORTING  
      is_variant    = g_variant  
      i_save        = 'A'           " Memungkinkan penyimpanan layout  
    IMPORTING  
      es_variant    = g_variant  
    EXCEPTIONS  
      not_found     = 1  
      program_error = 2  
      OTHERS        = 3.  
  
  IF sy-subrc = 0.  
    p_var = g_variant-variant. " Isi parameter dengan variant yang dipilih  
  ELSE.  
    MESSAGE 'NO variant selected OR an error occurred.' TYPE 'I'.  
  ENDIF.  
  
  
AT SELECTION-SCREEN.  
  
  CASE sy-ucomm.  
    WHEN 'ONLI'.  "Upload  
  AUTHORITY-CHECK OBJECT 'I_IWERK'  
  ID 'IWERK' FIELD s_werks-low.  
  IF sy-subrc NE 0.  
      MESSAGE 'You are not authorized for selected maintenance plant!' TYPE 'E'.  
    ELSE.  
      PERFORM Display.  
    ENDIF.  
  
  ENDCASE.
```