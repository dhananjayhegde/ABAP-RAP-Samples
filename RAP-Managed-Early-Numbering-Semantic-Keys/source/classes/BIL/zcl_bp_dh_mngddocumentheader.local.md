```abap
CLASS lhc_Header DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS get_instance_authorizations FOR INSTANCE AUTHORIZATION
      IMPORTING keys REQUEST requested_authorizations FOR Header RESULT result.
    METHODS defaults FOR DETERMINE ON MODIFY
      IMPORTING keys FOR header~defaults.

    METHODS earlynumbering_create FOR NUMBERING
      IMPORTING entities FOR CREATE Header.

    METHODS earlynumbering_cba_Item FOR NUMBERING
      IMPORTING entities FOR CREATE Header\_Item.

ENDCLASS.

CLASS lhc_Header IMPLEMENTATION.

  METHOD get_instance_authorizations.
  ENDMETHOD.

  METHOD earlynumbering_create.
    DATA(lo_doc_handler) = zcl_dh_mngd_document_handler=>get_instance( ).

    LOOP AT entities ASSIGNING FIELD-SYMBOL(<ls_entity>).
      INSERT VALUE #( %cid            = <ls_entity>-%cid
                      DocumentNumber  = lo_doc_handler->get_next_doc_number( ) ) INTO TABLE mapped-header.
    ENDLOOP.
  ENDMETHOD.

  METHOD earlynumbering_cba_Item.
    DATA(lo_doc_handler) = zcl_dh_mngd_document_handler=>get_instance( ).

    LOOP AT entities ASSIGNING FIELD-SYMBOL(<ls_entity>).
      LOOP AT <ls_entity>-%target ASSIGNING FIELD-SYMBOL(<ls_item_create>).
        INSERT VALUE #( %cid            = <ls_item_create>-%cid
                        DocumentNumber  = <ls_entity>-DocumentNumber
                        ItemNumber      = lo_doc_handler->get_next_item_number( iv_doc_number = <ls_entity>-DocumentNumber ) ) INTO TABLE mapped-item.
      ENDLOOP.
    ENDLOOP.
  ENDMETHOD.

  METHOD Defaults.
    READ ENTITIES OF ZDH_MngdDocumentHeader IN LOCAL MODE
        ENTITY Header FROM CORRESPONDING #( keys )
        RESULT DATA(lt_docs).


    MODIFY ENTITIES OF ZDH_MngdDocumentHeader IN LOCAL MODE
      ENTITY Header
        UPDATE FIELDS ( CreatedOn CreatedBy )
          WITH VALUE #( FOR doc IN lt_docs ( DocumentNumber = doc-DocumentNumber
                                             CreatedOn      = sy-datum
                                             CreatedBy      = sy-uname ) ).
  ENDMETHOD.

ENDCLASS.

CLASS lhc_Item DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS earlynumbering_cba_Account FOR NUMBERING
      IMPORTING entities FOR CREATE Item\_Account.

    METHODS Defaults FOR DETERMINE ON MODIFY
      IMPORTING keys FOR Item~Defaults.

ENDCLASS.

CLASS lhc_Item IMPLEMENTATION.

  METHOD earlynumbering_cba_Account.
    DATA(lo_doc_handler) = zcl_dh_mngd_document_handler=>get_instance( ).

    LOOP AT entities ASSIGNING FIELD-SYMBOL(<ls_entity>).
      LOOP AT <ls_entity>-%target ASSIGNING FIELD-SYMBOL(<ls_account_create>).
        INSERT VALUE #( %cid            = <ls_account_create>-%cid
                        DocumentNumber  = <ls_entity>-DocumentNumber
                        ItemNumber      = <ls_entity>-ItemNumber
                        AccountNumber   = lo_doc_handler->get_next_account_number( iv_doc_number  = <ls_entity>-DocumentNumber
                                                                                   iv_item_number = <ls_entity>-ItemNumber ) ) INTO TABLE mapped-account.
      ENDLOOP.
    ENDLOOP.
  ENDMETHOD.

  METHOD Defaults.

    READ ENTITIES OF ZDH_MngdDocumentHeader IN LOCAL MODE
      ENTITY Item FROM CORRESPONDING #( keys )
      RESULT DATA(lt_items).


    MODIFY ENTITIES OF ZDH_MngdDocumentHeader IN LOCAL MODE
      ENTITY Item
        UPDATE FIELDS ( CreatedOn CreatedBy )
          WITH VALUE #( FOR item IN lt_items ( DocumentNumber = item-DocumentNumber
                                               ItemNumber     = item-ItemNumber
                                               CreatedOn      = sy-datum
                                               CreatedBy      = sy-uname ) ).
  ENDMETHOD.

ENDCLASS.

CLASS lhc_account DEFINITION INHERITING FROM cl_abap_behavior_handler.

  PRIVATE SECTION.

    METHODS Defaults FOR DETERMINE ON MODIFY
      IMPORTING keys FOR Account~Defaults.

ENDCLASS.

CLASS lhc_account IMPLEMENTATION.

  METHOD Defaults.
    READ ENTITIES OF ZDH_MngdDocumentHeader IN LOCAL MODE
        ENTITY Account FROM CORRESPONDING #( keys )
        RESULT DATA(lt_acount).


    MODIFY ENTITIES OF ZDH_MngdDocumentHeader IN LOCAL MODE
      ENTITY Account
        UPDATE FIELDS ( CreatedOn CreatedBy )
          WITH VALUE #( FOR account IN lt_acount (  DocumentNumber = account-DocumentNumber
                                                    ItemNumber     = account-ItemNumber
                                                    AccountNumber  = account-AccountNumber
                                                    CreatedOn      = sy-datum
                                                    CreatedBy      = sy-uname ) ).
  ENDMETHOD.

ENDCLASS.
```
