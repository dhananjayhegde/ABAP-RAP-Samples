```abap
CLASS zcl_dh_mngd_document_handler DEFINITION
  PUBLIC
  FINAL
  CREATE PRIVATE .

  PUBLIC SECTION.

    CLASS-METHODS:
      get_instance
        RETURNING VALUE(ro_handler) TYPE REF TO zcl_dh_mngd_document_handler,

      refresh.

    METHODS set_header_for_create
      IMPORTING
        is_header TYPE zdh_header.

    METHODS set_item_for_create
      IMPORTING
        is_item TYPE zdh_item.

    METHODS set_account_for_create
      IMPORTING
        is_account TYPE zdh_account.

    METHODS check
      EXPORTING
        !et_messages TYPE mepo_t_messages_bapi .

    METHODS post
      RETURNING VALUE(rv_success) TYPE abap_bool.

    METHODS get_next_doc_number
      RETURNING VALUE(rv_number) TYPE char10.

    METHODS get_next_item_number
      IMPORTING
                !iv_doc_number        TYPE char10
      RETURNING VALUE(rv_item_number) TYPE ebelp.

    METHODS get_next_account_number
      IMPORTING
                !iv_doc_number           TYPE char10
                !iv_item_number          TYPE ebelp
      RETURNING VALUE(rv_account_number) TYPE dzekkn.

  PROTECTED SECTION.
  PRIVATE SECTION.

    CLASS-DATA:
          mo_handler TYPE REF TO zcl_dh_mngd_document_handler.

    DATA:
      mt_header  TYPE SORTED TABLE OF zdh_header WITH UNIQUE KEY doc_number,
      mt_item    TYPE SORTED TABLE OF zdh_item WITH UNIQUE KEY doc_number item_number,
      mt_account TYPE SORTED TABLE OF zdh_account WITH UNIQUE KEY doc_number item_number account_number.
ENDCLASS.



CLASS zcl_dh_mngd_document_handler IMPLEMENTATION.
  METHOD get_instance.
    IF mo_handler IS NOT BOUND.
      mo_handler = NEW #( ).
    ENDIF.

    ro_handler = mo_handler.
  ENDMETHOD.

  METHOD refresh.
    CLEAR mo_handler.
  ENDMETHOD.

  METHOD set_header_for_create.
    INSERT is_header INTO TABLE mt_header.
  ENDMETHOD.

  METHOD set_item_for_create.
    INSERT is_item INTO TABLE mt_item.
  ENDMETHOD.

  METHOD set_account_for_create.
    INSERT is_account INTO TABLE mt_account.
  ENDMETHOD.

  METHOD check.

  ENDMETHOD.

  METHOD post.
    rv_success = abap_false.

    INSERT zdh_header FROM TABLE mt_header.

    CHECK sy-subrc = 0.
    INSERT zdh_item FROM TABLE mt_item.

    CHECK sy-subrc = 0.
    INSERT zdh_account FROM TABLE mt_account.

    CHECK sy-subrc = 0.

    rv_success = abap_true.
    refresh( ).
  ENDMETHOD.

  METHOD get_next_doc_number.
    IF mt_header IS INITIAL.
      "-- no unsaved documents
      SELECT MAX( doc_number ) FROM zdh_header INTO @rv_number.
    ELSE.
      "-- unsaved new documents
      rv_number = mt_header[ lines( mt_header ) ]-doc_number.
    ENDIF.

    rv_number += 1.
    INSERT VALUE #( doc_number = rv_number ) INTO TABLE mt_header.
  ENDMETHOD.

  METHOD get_next_item_number.
    DATA(lt_doc_items) = FILTER #( mt_item WHERE doc_number = iv_doc_number ).

    IF lt_doc_items IS NOT INITIAL.
      rv_item_number = lt_doc_items[ lines( lt_doc_items ) ]-item_number.
    ELSE.
      SELECT MAX( item_number ) FROM zdh_item WHERE doc_number = @iv_doc_number INTO @rv_item_number.
    ENDIF.

    rv_item_number += 1.
    INSERT VALUE #( doc_number  = iv_doc_number
                    item_number = rv_item_number ) INTO TABLE mt_item.
  ENDMETHOD.

  METHOD get_next_account_number.
    DATA(lt_item_accounts) = FILTER #( mt_account WHERE doc_number = iv_doc_number AND item_number = iv_item_number ).

    IF lt_item_accounts IS NOT INITIAL.
      rv_account_number = lt_item_accounts[ lines( lt_item_accounts ) ]-account_number.
    ELSE.
      SELECT MAX( account_number ) FROM zdh_account WHERE doc_number  = @iv_doc_number
                                                      AND item_number = @iv_item_number INTO @rv_account_number.
    ENDIF.

    rv_account_number += 1.
    INSERT VALUE #( doc_number      = iv_doc_number
                    item_number     = iv_item_number
                    account_number  = rv_account_number ) INTO TABLE mt_account.

  ENDMETHOD.

ENDCLASS.
```
