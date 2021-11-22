```abap
@EndUserText.label : 'Header'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zdh_header {
  @AbapCatalog.foreignKey.keyType : #KEY
  @AbapCatalog.foreignKey.screenCheck : true
  key mandt          : mandt not null
    with foreign key [1..*,1] t000
      where mandt = zdh_header.mandt;
  key doc_number     : char10 not null;
  bukrs              : bukrs;
  ekorg              : ekorg;
  lastchangedatetime : changedatetime;
  aedat              : mmpur_erdat;
  @AbapCatalog.anonymizedWhenDelivered : true
  ernam              : mmpur_ernam;

}
````
