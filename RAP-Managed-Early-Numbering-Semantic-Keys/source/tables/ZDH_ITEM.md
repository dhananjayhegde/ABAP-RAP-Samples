```abap
@EndUserText.label : 'Item'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zdh_item {
  @AbapCatalog.foreignKey.keyType : #KEY
  @AbapCatalog.foreignKey.screenCheck : true
  key mandt          : mandt not null
    with foreign key [1..*,1] t000
      where mandt = zdh_item.mandt;
  @AbapCatalog.foreignKey.screenCheck : false
  key doc_number     : char10 not null
    with foreign key [1..*,1] zdh_header
      where mandt = zdh_item.mandt
        and doc_number = zdh_item.doc_number;
  key item_number    : ebelp not null;
  item_descr         : char50;
  lastchangedatetime : changedatetime;
  aedat              : mmpur_erdat;
  @AbapCatalog.anonymizedWhenDelivered : true
  ernam              : mmpur_ernam;

}
```
