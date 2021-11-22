```abap
CLASS zcl_dh_purctr_buffer DEFINITION
  PUBLIC
  FINAL
  CREATE PRIVATE .

  PUBLIC SECTION.

    INTERFACES zif_dh_purctr_buffer .

    CLASS-METHODS get_instance
      RETURNING VALUE(ro_buffer) TYPE REF TO zif_dh_purctr_buffer.

    CLASS-METHODS refresh.

  PROTECTED SECTION.
  PRIVATE SECTION.

    CLASS-DATA:
      mo_instance TYPE REF TO zif_dh_purctr_buffer,

      mt_header   TYPE zif_dh_purctr_buffer=>tt_header,
      mt_item     TYPE zif_dh_purctr_buffer=>tt_item.
ENDCLASS.

CLASS zcl_dh_purctr_buffer IMPLEMENTATION.

  METHOD get_instance.
    IF mo_instance IS NOT BOUND.
      mo_instance = NEW zcl_dh_purctr_buffer( ).
    ENDIF.

    ro_buffer = mo_instance.
  ENDMETHOD.

  METHOD refresh.
    CLEAR:
     mt_header,
     mt_item,
     mo_instance.
  ENDMETHOD.

  METHOD zif_dh_purctr_buffer~add_header.
    ASSIGN mt_header[ cid = is_header-cid ] TO FIELD-SYMBOL(<ls_header>).

    IF <ls_header> IS NOT ASSIGNED.
      INSERT INITIAL LINE INTO TABLE mt_header ASSIGNING <ls_header>.
    ENDIF.

    <ls_header>-cid     = is_header-cid.
    <ls_header>-pid     = is_header-pid.
    <ls_header>-key     = CORRESPONDING #( is_header-key ).
    <ls_header>-data    = CORRESPONDING #( is_header-data ).
  ENDMETHOD.


  METHOD zif_dh_purctr_buffer~add_item.
    ASSIGN mt_item[ cid = is_item-cid ] TO FIELD-SYMBOL(<ls_item>).

    IF <ls_item> IS NOT ASSIGNED.
      INSERT INITIAL LINE INTO TABLE mt_item ASSIGNING <ls_item>.
    ENDIF.

    <ls_item>-cid     = is_item-cid.
    <ls_item>-cid_ref = is_item-cid_ref.
    <ls_item>-pid     = is_item-pid.
    <ls_item>-key     = CORRESPONDING #( is_item-key ).
    <ls_item>-data    = CORRESPONDING #( is_item-data ).
  ENDMETHOD.


  METHOD zif_dh_purctr_buffer~get_header_by_cid.
    TRY.
        IF iv_cid IS NOT INITIAL.
          rs_header = mt_header[ cid = iv_cid ].
        ELSEIF iv_pid IS NOT INITIAL.
          rs_header = mt_header[ pid = iv_pid ].
        ENDIF.
      CATCH cx_root.
    ENDTRY.
  ENDMETHOD.


  METHOD zif_dh_purctr_buffer~get_item_by_cid.
    TRY.
        IF iv_cid IS NOT INITIAL.
          rs_item = mt_item[ cid = iv_cid ].
        ELSEIF iv_pid IS NOT INITIAL.
          rs_item = mt_item[ pid = iv_pid ].
        ENDIF.
      CATCH cx_root.
    ENDTRY.
  ENDMETHOD.

  METHOD zif_dh_purctr_buffer~get_all_header_data.
    rt_header = mt_header.
  ENDMETHOD.

  METHOD zif_dh_purctr_buffer~get_all_item_data.
    rt_item = mt_item.
  ENDMETHOD.

ENDCLASS.
```
