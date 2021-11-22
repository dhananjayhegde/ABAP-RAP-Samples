```abap
*"* use this source file for your ABAP unit test classes
CLASS ltcl_eml_tests DEFINITION FINAL FOR TESTING
  DURATION SHORT
  RISK LEVEL HARMLESS.

  PRIVATE SECTION.
    METHODS:
      deep_create_all_entities FOR TESTING RAISING cx_static_check,
      deep_create_multi_items FOR TESTING RAISING cx_static_check,
      delete_1_doc FOR TESTING RAISING cx_static_check.
ENDCLASS.


CLASS ltcl_eml_tests IMPLEMENTATION.

  METHOD deep_create_all_entities.
    MODIFY ENTITIES OF ZDH_MngdDocumentHeader
      ENTITY Header
        CREATE SET FIELDS WITH VALUE #( ( %cid                   = 'doc1'
                                          CompanyCode            = '0001'
                                          PurchasingOrganization = '0001' ) )
        CREATE BY \_Item SET FIELDS WITH VALUE #( (  %cid_ref = 'doc1'
                                                     %target  = VALUE #( ( %cid             = 'item1'
                                                                           itemdescription  = 'EML Test Item 1' ) ) ) )
      ENTITY Item
        CREATE BY \_Account SET FIELDS WITH VALUE #( (  %cid_ref  = 'item1'
                                                        %target   = VALUE #( (  %cid        = 'acc1'
                                                                                costcenter  = 'SVC_001' ) ) ) )
      MAPPED DATA(mapped)
      FAILED DATA(failed)
      REPORTED DATA(reported).

    cl_abap_unit_assert=>assert_initial(     act = failed         msg = `Failed in interaction phase` ).
    cl_abap_unit_assert=>assert_not_initial( act = mapped-header  msg = `Header mapping not filled` ).
    cl_abap_unit_assert=>assert_not_initial( act = mapped-item    msg = `Item mapping not filled` ).
    cl_abap_unit_assert=>assert_not_initial( act = mapped-account msg = `Account mapping not filled` ).

    COMMIT ENTITIES.

    SELECT *
      FROM zdh_header
      FOR ALL ENTRIES IN @mapped-header
      WHERE doc_number = @mapped-header-DocumentNumber
      INTO TABLE @DATA(lt_docs_created).

    SELECT *
      FROM zdh_item
      FOR ALL ENTRIES IN @mapped-item
      WHERE doc_number   = @mapped-item-DocumentNumber
        AND item_number  = @mapped-item-ItemNumber
      INTO TABLE @DATA(lt_item_created).

    SELECT *
      FROM zdh_account
      FOR ALL ENTRIES IN @mapped-account
      WHERE doc_number     = @mapped-account-DocumentNumber
        AND item_number    = @mapped-account-ItemNumber
        AND account_number = @mapped-account-AccountNumber
      INTO TABLE @DATA(lt_acc_created).

    cl_abap_unit_assert=>assert_not_initial( act = lt_docs_created  msg = `Documents not created` ).
    cl_abap_unit_assert=>assert_not_initial( act = lt_item_created  msg = `Items not created` ).
    cl_abap_unit_assert=>assert_not_initial( act = lt_acc_created   msg = `Account Lines not created` ).

  ENDMETHOD.

  METHOD delete_1_doc.

    DATA: lv_doc_number TYPE zdh_header-doc_number VALUE 3.

    READ ENTITIES OF ZDH_MngdDocumentHeader
      ENTITY header
        ALL FIELDS WITH VALUE #( ( DocumentNumber = lv_doc_number ) )
        RESULT DATA(lt_header)
        BY \_Item
          ALL FIELDS WITH VALUE #( ( DocumentNumber = lv_doc_number ) )
          RESULT DATA(lt_items).

    READ ENTITIES OF ZDH_MngdDocumentHeader
     ENTITY Item
       BY \_Account
          ALL FIELDS WITH CORRESPONDING #( lt_items )
          RESULT DATA(lt_accounts).

    IF lt_header IS NOT INITIAL AND lt_items IS NOT INITIAL AND lt_accounts IS NOT INITIAL.
      MODIFY ENTITIES OF ZDH_MngdDocumentHeader
        ENTITY Header DELETE FROM CORRESPONDING #( lt_header )
        ENTITY item DELETE FROM CORRESPONDING #( lt_items )
        ENTITY Account DELETE FROM CORRESPONDING #( lt_accounts ).

      COMMIT ENTITIES.
    ENDIF.

  ENDMETHOD.

  METHOD deep_create_multi_items.
    MODIFY ENTITIES OF ZDH_MngdDocumentHeader
      ENTITY Header
        CREATE SET FIELDS WITH VALUE #( ( %cid                   = 'doc1'
                                          CompanyCode            = '0001'
                                          PurchasingOrganization = '0001' ) )
        CREATE BY \_Item SET FIELDS WITH VALUE #( (  %cid_ref = 'doc1'
                                                     %target  = VALUE #( ( %cid             = 'item1'
                                                                           itemdescription  = 'EML Test Item 1' )
                                                                         ( %cid             = 'item2'
                                                                           itemdescription  = 'EML Test Item 2' ) ) ) )
      ENTITY Item
        CREATE BY \_Account SET FIELDS WITH VALUE #( (  %cid_ref  = 'item1'
                                                        %target   = VALUE #( (  %cid        = 'acc1'
                                                                                costcenter  = 'SVC_001' )
                                                                             (  %cid        = 'acc2'
                                                                                costcenter  = 'SVC_002' ) ) )
                                                     (  %cid_ref  = 'item2'
                                                        %target   = VALUE #( (  %cid        = 'acc3'
                                                                                costcenter  = 'SVC_001' ) ) ) )
      MAPPED DATA(mapped)
      FAILED DATA(failed)
      REPORTED DATA(reported).

    cl_abap_unit_assert=>assert_initial(     act = failed         msg = `Failed in interaction phase` ).
    cl_abap_unit_assert=>assert_not_initial( act = mapped-header  msg = `Header mapping not filled` ).
    cl_abap_unit_assert=>assert_not_initial( act = mapped-item    msg = `Item mapping not filled` ).
    cl_abap_unit_assert=>assert_not_initial( act = mapped-account msg = `Account mapping not filled` ).

    COMMIT ENTITIES.

    SELECT *
      FROM zdh_header
      FOR ALL ENTRIES IN @mapped-header
      WHERE doc_number = @mapped-header-DocumentNumber
      INTO TABLE @DATA(lt_docs_created).

    SELECT *
      FROM zdh_item
      FOR ALL ENTRIES IN @mapped-item
      WHERE doc_number   = @mapped-item-DocumentNumber
        AND item_number  = @mapped-item-ItemNumber
      INTO TABLE @DATA(lt_item_created).

    SELECT *
      FROM zdh_account
      FOR ALL ENTRIES IN @mapped-account
      WHERE doc_number     = @mapped-account-DocumentNumber
        AND item_number    = @mapped-account-ItemNumber
        AND account_number = @mapped-account-AccountNumber
      INTO TABLE @DATA(lt_acc_created).

    cl_abap_unit_assert=>assert_not_initial( act = lt_docs_created  msg = `Documents not created` ).
    cl_abap_unit_assert=>assert_not_initial( act = lt_item_created  msg = `Items not created` ).
    cl_abap_unit_assert=>assert_not_initial( act = lt_acc_created   msg = `Account Lines not created` ).

    cl_abap_unit_assert=>assert_equals( act = lines( lt_item_created )
                                        exp = 2
                                        msg = `Expected number of Items not created` ).

    cl_abap_unit_assert=>assert_equals( act = lines( lt_acc_created )
                                        exp = 3
                                        msg = `Expected number of Account lines not created` ).
  ENDMETHOD.

ENDCLASS.
```
