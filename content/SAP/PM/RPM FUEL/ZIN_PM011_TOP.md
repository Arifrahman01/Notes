```
*&---------------------------------------------------------------------*

*& Include ZIN_PM011_TOP

*&---------------------------------------------------------------------*

TABLES : zta_cs_101, iloa, equi, rp50g.

  

" ty_data (Trip) dihapus karena p_stat_2 sudah tidak digunakan

  

TYPES: BEGIN OF ty_data2,

timesheet_num TYPE zta_cs_101-timesheet_num,

plant TYPE zta_cs_101-swerk,

ship_number TYPE zta_cs_101-main_invnr,

rsnum TYPE resb-rsnum,

trip_period TYPE zta_cs_101-period,

trip_number TYPE zta_cs_101-trip_num,

posting_date TYPE zta_cs_101-posting_date,

shipping_instruction TYPE zta_cs_101-sidoc,

start_point TYPE zta_cs_101-start_point,

end_point TYPE zta_cs_101-end_point,

location TYPE zta_cs_003-value1,

start_time TYPE zta_cs_102-start_time,

end_time TYPE zta_cs_102-end_time,

tot_time TYPE p DECIMALS 1,

activity TYPE zta_cs_102-act_code,

premi_code TYPE zta_cs_101-premi_code,

cs_order TYPE aufnr,

barge_trip TYPE zta_cs_102-barge_trip,

working TYPE zta_cs_102-work_code,

barge TYPE zta_cs_102-sec_invnr,

cargo TYPE zta_cs_102-cargo_code,

qty TYPE zta_cs_102-cargo_qty,

uom TYPE zta_cs_102-cargo_uom,

avg_speed TYPE zta_cs_102-avg_speed,

remarks TYPE zta_cs_102-remarks,

indicator_crew TYPE zta_cs_103-crew_status,

trip_start TYPE zta_cs_101-trip_start,

trip_end TYPE zta_cs_101-trip_end,

END OF ty_data2.

  

DATA: gi_data2 TYPE TABLE OF ty_data2,

gw_data2 TYPE ty_data2,

gi_t211 TYPE TABLE OF zpp_t211,

gw_t211 TYPE zpp_t211.

  

  

DATA: lv_start_sec TYPE i,

lv_end_sec TYPE i,

lv_diff_sec TYPE i,

lv_hours TYPE p DECIMALS 2,

ld_aufnr TYPE aufnr,

ld_rsnum TYPE rsnum.

  

  

DATA: o_alv TYPE REF TO cl_salv_table,

lx_msg TYPE REF TO cx_salv_msg,

columns TYPE REF TO cl_salv_columns_table,

column TYPE REF TO cl_salv_column.

  

DATA g_variant LIKE disvariant.

  

*----------------------------------------------------------------------*

* SELECTION SCREEN

*----------------------------------------------------------------------*

SELECTION-SCREEN BEGIN OF BLOCK data WITH FRAME TITLE TEXT-001.

SELECT-OPTIONS:

s_werks FOR iloa-swerk,

s_minvnr FOR zta_cs_101-main_invnr,

s_time FOR zta_cs_101-timesheet_num,

s_pdate FOR zta_cs_101-posting_date.

SELECTION-SCREEN END OF BLOCK data.

  

  

SELECTION-SCREEN BEGIN OF BLOCK a2 WITH FRAME TITLE TEXT-002.

PARAMETERS: p_var LIKE disvariant-variant.

SELECTION-SCREEN END OF BLOCK a2.

  

SELECTION-SCREEN: FUNCTION KEY 1.
```