```abap
@EndUserText.label : 'Account'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zdh_account {
  @AbapCatalog.foreignKey.keyType : #KEY
  @AbapCatalog.foreignKey.screenCheck : true
  key mandt          : mandt not null
    with foreign key [1..*,1] t000
      where mandt = zdh_account.mandt;
  @AbapCatalog.foreignKey.screenCheck : false
  key doc_number     : char10 not null
    with foreign key [1..*,1] zdh_header
      where mandt = zdh_account.mandt
        and doc_number = zdh_account.doc_number;
  @AbapCatalog.foreignKey.screenCheck : false
  key item_number    : ebelp not null
    with foreign key [1..*,1] zdh_item
      where mandt = zdh_account.mandt
        and doc_number = zdh_account.doc_number
        and item_number = zdh_account.item_number;
  key account_number : dzekkn not null;
  kostl              : kostl;
  lastchangedatetime : changedatetime;
  aedat              : mmpur_erdat;
  @AbapCatalog.anonymizedWhenDelivered : true
  ernam              : mmpur_ernam;

}
```
