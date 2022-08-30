```abap

CLASS lcl_util DEFINITION.

  PUBLIC SECTION.
    CLASS-METHODS generate_pid RETURNING VALUE(rv_id)  TYPE sysuuid_x16.
ENDCLASS.

CLASS lcl_util IMPLEMENTATION.

  METHOD generate_pid.
    TRY.
        rv_id = cl_system_uuid=>create_uuid_x16_static( ).
      CATCH cx_uuid_error.
        ASSERT 1 = 0.
    ENDTRY.
  ENDMETHOD.

ENDCLASS.

CLASS lcl_factory DEFINITION.

  PUBLIC SECTION.

    TYPES:
      BEGIN OF ty_bo_handler,
        root_pid   TYPE abp_behv_pid,
        ctr_number TYPE ebeln,
        handler    TYPE REF TO cl_ctr_handler_mm,
        mode       TYPE char1,
      END OF ty_bo_handler .

    TYPES:
      tt_bo_handlers TYPE STANDARD TABLE OF ty_bo_handler WITH DEFAULT KEY .

    CLASS-METHODS get_bo_handler_instance
      IMPORTING
        !iv_ctr_number    TYPE ebeln OPTIONAL
        !iv_pid           TYPE abp_behv_pid OPTIONAL
        !iv_mode          TYPE char1
        !iv_without_lock  TYPE abap_bool OPTIONAL
      EXPORTING
        !et_messages      TYPE mepo_t_messages_bapi
      RETURNING
        VALUE(ro_handler) TYPE REF TO cl_ctr_handler_mm.

    "! <p class="shorttext synchronized" lang="en">Get All handlers</p>
    CLASS-METHODS get_all_handlers
      RETURNING
        VALUE(rt_bo_handlers) TYPE tt_bo_handlers .

  PRIVATE SECTION.

    CLASS-DATA:
                mt_bo_handlers TYPE tt_bo_handlers.

ENDCLASS.

CLASS lcl_factory IMPLEMENTATION.

  METHOD get_bo_handler_instance.
    IF iv_ctr_number IS NOT INITIAL.
      ASSIGN mt_bo_handlers[ ctr_number = iv_ctr_number ] TO FIELD-SYMBOL(<ls_bo_handler>).
    ELSEIF iv_pid IS NOT INITIAL.
      ASSIGN mt_bo_handlers[ root_pid = iv_pid ] TO <ls_bo_handler>.
    ELSE.
      RETURN.
    ENDIF.

    IF <ls_bo_handler> IS NOT ASSIGNED.

      INSERT VALUE #( ctr_number = iv_ctr_number
                      root_pid   = iv_pid
                      mode       = iv_mode ) INTO TABLE mt_bo_handlers ASSIGNING <ls_bo_handler>.

      <ls_bo_handler>-handler = cl_ctr_handler_mm=>get_ctr_instance( ).

      TRY.
          <ls_bo_handler>-handler->open(
            EXPORTING
              im_ebeln        = iv_ctr_number
              im_aktyp        = iv_mode
              im_bstyp        = cl_mmpur_constants=>bstyp_k " Purchasing Contract
              im_without_lock = iv_without_lock
            IMPORTING
              et_messages     = et_messages
          ).
        CATCH cx_mmpur_root.
          " BO Handler already opened in incompatible mode
          MESSAGE e019(06) WITH iv_ctr_number INTO DATA(lv_dummy).
          INSERT CORRESPONDING #( sy ) INTO TABLE et_messages.
          RETURN.
      ENDTRY.
    ENDIF.

    ro_handler = <ls_bo_handler>-handler.

  ENDMETHOD.

  METHOD get_all_handlers.
    rt_bo_handlers = mt_bo_handlers.
  ENDMETHOD.

ENDCLASS.

CLASS lhc_Header DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS get_instance_authorizations FOR INSTANCE AUTHORIZATION
      IMPORTING keys REQUEST requested_authorizations FOR Header RESULT result.

    METHODS get_global_authorizations FOR GLOBAL AUTHORIZATION
      IMPORTING REQUEST requested_authorizations FOR Header RESULT result.

    METHODS create FOR MODIFY
      IMPORTING entities FOR CREATE Header.

    METHODS update FOR MODIFY
      IMPORTING entities FOR UPDATE Header.

    METHODS read FOR READ
      IMPORTING keys FOR READ Header RESULT result.

    METHODS lock FOR LOCK
      IMPORTING keys FOR LOCK Header.

    METHODS rba_Item FOR READ
      IMPORTING keys_rba FOR READ Header\_Item FULL result_requested RESULT result LINK association_links.

    METHODS cba_Item FOR MODIFY
      IMPORTING entities_cba FOR CREATE Header\_Item.

ENDCLASS.

CLASS lhc_Header IMPLEMENTATION.

  METHOD get_instance_authorizations.
  ENDMETHOD.

  METHOD get_global_authorizations.
  ENDMETHOD.

  METHOD create.

    DATA:
      lt_messages TYPE mepo_t_messages_bapi,
      ls_header   TYPE outline_agrmnt_header_data.

    LOOP AT entities ASSIGNING FIELD-SYMBOL(<ls_header_payload>).

      DATA(lv_pid) = lcl_util=>generate_pid( ).

      DATA(lo_handler) = lcl_factory=>get_bo_handler_instance(
                           EXPORTING
                             iv_pid          = lv_pid
                             iv_mode         = cl_mmpur_constants=>hin
                           IMPORTING
                             et_messages     = lt_messages
                         ).

      IF lo_handler IS BOUND.

        ls_header-data  = CORRESPONDING #( <ls_header_payload>-%data MAPPING FROM ENTITY ).
        ls_header-datax = CORRESPONDING #( <ls_header_payload> MAPPING FROM ENTITY USING CONTROL ).

        lo_handler->set_outl_agreement_header(  is_outl_agrmnt_header    = ls_header
                                                is_outl_agrmnt_set_flags = VALUE #( header = abap_true ) ).

        lo_handler->outl_agrmnt_process( IMPORTING ex_messages =  lt_messages ).

      ENDIF.


      IF NOT line_exists( lt_messages[ msgty = 'E' ] ).

        INSERT VALUE #( %cid = <ls_header_payload>-%cid
                        %pid = lv_pid ) INTO TABLE mapped-header.

        zcl_dh_purctr_buffer=>get_instance( )->add_header( VALUE #( cid   = <ls_header_payload>-%cid
                                                                    pid   = lv_pid ) ).

      ELSE.

        "
        " Create failed.  Fill FAILED and REPORTED
        "

      ENDIF.
    ENDLOOP.

  ENDMETHOD.

  METHOD update.
  ENDMETHOD.

  METHOD read.
  ENDMETHOD.

  METHOD lock.
  ENDMETHOD.

  METHOD rba_Item.
  ENDMETHOD.

  METHOD cba_Item.
    DATA:
      lv_ctr      TYPE ebeln,
      lt_messages TYPE mepo_t_messages_bapi,
      lt_item     TYPE outline_agrmnt_t_item.


    LOOP AT entities_cba ASSIGNING FIELD-SYMBOL(<ls_item_cba>).
      LOOP AT <ls_item_cba>-%target ASSIGNING FIELD-SYMBOL(<ls_item_payload>).

        IF <ls_item_cba>-PurchaseContract IS NOT INITIAL.
          lv_ctr = <ls_item_cba>-PurchaseContract.
        ELSEIF <ls_item_cba>-%cid_ref IS NOT INITIAL.
          DATA(ls_header_key) = zcl_dh_purctr_buffer=>get_instance( )->get_header_by_cid( iv_cid = <ls_item_cba>-%cid_ref ).
        ELSE.
          "-- Invalid parent key
        ENDIF.


        DATA(lo_handler) = lcl_factory=>get_bo_handler_instance(
                             EXPORTING
                               iv_ctr_number   = lv_ctr
                               iv_pid          = ls_header_key-pid
                               iv_mode         = cl_mmpur_constants=>hin
                             IMPORTING
                               et_messages     = lt_messages
                           ).

        IF lo_handler IS BOUND.

          lt_item  = VALUE #( ( id    = lo_handler->get_id( )
                                data  = CORRESPONDING #( <ls_item_payload>-%data MAPPING FROM ENTITY )
                                datax = CORRESPONDING #( <ls_item_payload> MAPPING FROM ENTITY USING CONTROL ) ) ).


          lo_handler->set_outl_agrrement_items( it_items          = lt_item
                                                is_item_set_flags = VALUE #( item = abap_true ) ).

          lo_handler->outl_agrmnt_process( IMPORTING ex_messages =  lt_messages ).

        ENDIF.


        IF NOT line_exists( lt_messages[ msgty = 'E' ] ).

          DATA(lv_item_pid) = lcl_util=>generate_pid( ).

          INSERT VALUE #( %cid = <ls_item_payload>-%cid
                          %pid = lv_item_pid  ) INTO TABLE mapped-item.

          zcl_dh_purctr_buffer=>get_instance( )->add_item( VALUE #( cid     = <ls_item_payload>-%cid
                                                                    cid_ref = <ls_item_cba>-%cid_ref
                                                                    pid     = lv_item_pid ) ).

        ELSE.

          "
          " Create failed.  Fill FAILED and REPORTED
          "

        ENDIF.

        CLEAR: lt_item, lv_ctr, lv_item_pid, ls_header_key.
      ENDLOOP.
    ENDLOOP.
  ENDMETHOD.

ENDCLASS.

CLASS lhc_Item DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS update FOR MODIFY
      IMPORTING entities FOR UPDATE Item.

    METHODS read FOR READ
      IMPORTING keys FOR READ Item RESULT result.

    METHODS rba_Account FOR READ
      IMPORTING keys_rba FOR READ Item\_Account FULL result_requested RESULT result LINK association_links.

    METHODS rba_Header FOR READ
      IMPORTING keys_rba FOR READ Item\_Header FULL result_requested RESULT result LINK association_links.

    METHODS cba_Account FOR MODIFY
      IMPORTING entities_cba FOR CREATE Item\_Account.

ENDCLASS.

CLASS lhc_Item IMPLEMENTATION.

  METHOD update.
  ENDMETHOD.

  METHOD read.
  ENDMETHOD.

  METHOD rba_Account.
  ENDMETHOD.

  METHOD rba_Header.
  ENDMETHOD.

  METHOD cba_Account.
  ENDMETHOD.

ENDCLASS.

CLASS lhc_Account DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS update FOR MODIFY
      IMPORTING entities FOR UPDATE Account.

    METHODS read FOR READ
      IMPORTING keys FOR READ Account RESULT result.

    METHODS rba_Header FOR READ
      IMPORTING keys_rba FOR READ Account\_Header FULL result_requested RESULT result LINK association_links.
    METHODS rba_Item FOR READ
      IMPORTING keys_rba FOR READ Account\_Item FULL result_requested RESULT result LINK association_links.

ENDCLASS.

CLASS lhc_Account IMPLEMENTATION.

  METHOD update.
  ENDMETHOD.

  METHOD read.
  ENDMETHOD.

  METHOD rba_Header.
  ENDMETHOD.

  METHOD rba_Item.
  ENDMETHOD.

ENDCLASS.

CLASS lsc_ZDH_R_PURCTR DEFINITION INHERITING FROM cl_abap_behavior_saver.
  PROTECTED SECTION.
    TYPES:
      ts_mapped_late TYPE RESPONSE FOR MAPPED LATE zdh_r_purctr.

    METHODS finalize REDEFINITION.

    METHODS check_before_save REDEFINITION.

    METHODS adjust_numbers REDEFINITION.

    METHODS save REDEFINITION.

    METHODS cleanup REDEFINITION.

    METHODS cleanup_finalize REDEFINITION.

  PRIVATE SECTION.

    DATA:
      mo_buffer        TYPE REF TO zif_dh_purctr_buffer,
      mt_header_buffer TYPE zif_dh_purctr_buffer=>tt_header,
      mt_item_buffer   TYPE zif_dh_purctr_buffer=>tt_item.


    "! <p class="shorttext synchronized" lang="en">Map results to CT_MAPPED</p>
    METHODS _map_results
      IMPORTING
        !iv_header_pid TYPE abp_behv_pid
        !iv_ctr        TYPE ebeln
      CHANGING
        !cs_mapped     TYPE ts_mapped_late.

ENDCLASS.

CLASS lsc_ZDH_R_PURCTR IMPLEMENTATION.

  METHOD finalize.
  ENDMETHOD.

  METHOD check_before_save.

    LOOP AT lcl_factory=>get_all_handlers( ) INTO DATA(ls_bo_handler).

      ls_bo_handler-handler->outl_agrmnt_process(  ).

    ENDLOOP.
  ENDMETHOD.

  METHOD adjust_numbers.

    mo_buffer = zcl_dh_purctr_buffer=>get_instance( ).

    mt_header_buffer = mo_buffer->get_all_header_data( ).
    mt_item_buffer   = mo_buffer->get_all_item_data( ).

    LOOP AT lcl_factory=>get_all_handlers( ) INTO DATA(ls_bo_handler).

      ls_bo_handler-handler->outl_agrmnt_post(
         EXPORTING
           im_no_commit = abap_true
         IMPORTING
           ex_messages  = DATA(lt_messages)
       ).

      ASSIGN lt_messages[ msgty = 'S' msgid = '06' msgno = '017' ] TO FIELD-SYMBOL(<ls_message>).

      IF sy-subrc = 0.
        DATA(lv_ctr_created) = <ls_message>-ebeln.

        _map_results(
          EXPORTING
            iv_header_pid = ls_bo_handler-root_pid
            iv_ctr        = lv_ctr_created
          CHANGING
            cs_mapped     = mapped
        ).
      ENDIF.
    ENDLOOP.

  ENDMETHOD.

  METHOD save.
  ENDMETHOD.

  METHOD cleanup.
  ENDMETHOD.

  METHOD cleanup_finalize.
  ENDMETHOD.

  METHOD _map_results.

    ASSIGN mt_header_buffer[ pid = iv_header_pid ] TO FIELD-SYMBOL(<ls_header_buff>).

    IF <ls_header_buff> IS ASSIGNED.
      cs_mapped-header = VALUE #( BASE cs_mapped-header ( %pid              = iv_header_pid
                                                          PurchaseContract  = iv_ctr ) ).

      LOOP AT mt_item_buffer ASSIGNING FIELD-SYMBOL(<ls_item_buff>) USING KEY sorted_cid_ref WHERE cid_ref = <ls_header_buff>-cid.
        cs_mapped-item = VALUE #( BASE cs_mapped-item ( %pid                  = <ls_item_buff>-pid
                                                        PurchaseContract      = iv_ctr
                                                        PurchaseContractItem  = <ls_item_buff>-key-PurchaseContractItem ) ).
      ENDLOOP.

    ENDIF.


  ENDMETHOD.

ENDCLASS.
```
