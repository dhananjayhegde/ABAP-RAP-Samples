```abap
*"* use this source file for your ABAP unit test classes
CLASS ltcl_eml_tests DEFINITION FINAL FOR TESTING
  DURATION SHORT
  RISK LEVEL HARMLESS.

  PRIVATE SECTION.
    METHODS:
      create_with_1_item FOR TESTING RAISING cx_static_check,

      "-- utility methods
      _display IMPORTING msg TYPE string.
ENDCLASS.


CLASS ltcl_eml_tests IMPLEMENTATION.

  METHOD create_with_1_item.

    MODIFY ENTITIES OF ZDH_R_PurCtr
     ENTITY  Header
       CREATE SET FIELDS WITH VALUE #( ( %cid                        = 'header1'
                                         CompanyCode                 = '0001'
                                         PurchasingDocumentCategory  = 'K'
                                         PurchaseContractType        = 'MK'
                                         PurchasingOrganization      = '0001'
                                         PurchasingGroup             = '001'
                                         DocumentCurrency            = 'EUR'
                                         Supplier                    = 'TEST-VENDOR'
                                         ValidityStartDate           = sy-datum
                                         ValidityEndDate             = sy-datum + 30
                                         QuotationSubmissionDate     = sy-datum ) )
        CREATE BY \_Item
          SET FIELDS WITH VALUE #( ( %cid_ref = 'header1'
                                     %target  = VALUE #( ( %cid                           = 'item1'
                                                           CompanyCode                    = '0001'
                                                           PurchasingDocumentItemCategory = '0'
                                                           Material                       = 'TEST-MATERIAL'
                                                           ManufacturerMaterial           = 'TEST-MATERIAL'
                                                           PurchaseContractItemText       = 'Test Item RAP Later Numbering'
                                                           MaterialGroup                  = '01'
                                                           Plant                          = '0001'
                                                           StorageLocation                = '0001'
                                                           ContractNetPriceAmount         = '1000'
                                                           TargetQuantity                 = '200'
                                                           NetPriceQuantity               = '1'
                                                           OrderPriceUnit                 = 'EA'
                                                           OrderQuantityUnit              = 'EA'
                                                           AccountAssignmentCategory      = ''
                                                           MultipleAcctAssgmtDistribution = '' " = Single, 1 = By Qty, 2 = By %, 3 = By Amount
                                                           OrdPriceUnitToOrderUnitDnmntr  = '1'
                                                           OrderPriceUnitToOrderUnitNmrtr = '1'
                                                           GoodsReceiptIsExpected         = 'X'
                                                           GoodsReceiptIsNonValuated      = ''
                                                           EvaldRcptSettlmtIsAllowed      = ''
                                                           InvoiceIsExpected              = 'X'
                                                           InvoiceIsGoodsReceiptBased     = 'X'
                                                           PurgDocPriceDate               ='99991231' ) ) ) )
        FAILED DATA(failed)
        REPORTED DATA(reported)
        MAPPED DATA(mapped).

    IF failed IS INITIAL.
      COMMIT ENTITIES
       BEGIN RESPONSE OF ZDH_R_PurCtr
         FAILED DATA(failed_late)
         REPORTED DATA(reported_late).

      LOOP AT mapped-header ASSIGNING FIELD-SYMBOL(<mapped>).

        CONVERT KEY OF ZDH_R_PurCtr FROM <mapped>-%pid TO DATA(ls_ctr).
        <mapped>-PurchaseContract = ls_ctr-PurchaseContract.

        _display( |Purchase Contract { ls_ctr-PurchaseContract } Created| ).

      ENDLOOP.

      COMMIT ENTITIES END.
    ENDIF.

    IF failed IS NOT INITIAL OR failed_late IS NOT INITIAL.
      _display( |Purchase Contract creation failed| ).
    ENDIF.
  ENDMETHOD.

  METHOD _display.
    "-- a method that logs any message to test framework output and then continues the test
    cl_abap_unit_assert=>fail(
        msg    = msg
        level  = if_abap_unit_constant=>severity-low
        quit   = if_abap_unit_constant=>quit-no
    ).
  ENDMETHOD.

ENDCLASS.
```
