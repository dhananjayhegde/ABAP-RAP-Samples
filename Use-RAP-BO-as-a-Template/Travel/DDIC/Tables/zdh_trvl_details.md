```ABAP
@EndUserText.label : 'DB table for Travel data'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zdh_trvl_details {
  key client      : abap.clnt not null;
  key mykey       : sysuuid_x16 not null;
  @AbapCatalog.foreignKey.screenCheck : false
  key travelkey   : sysuuid_x16 not null
    with foreign key zdh_travel_00
      where client = zdh_trvl_details.client
        and mykey = zdh_trvl_details.travelkey;
  sequence_num    : abap.numc(3);
  travel_date     : /dmo/begin_date;
  description     : /dmo/description;
  created_by      : syuname;
  created_at      : timestampl;
  last_changed_by : syuname;
  last_changed_at : timestampl;

}
```
