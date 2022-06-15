```ABAP
@EndUserText.label : 'Draft table for entity ZDH_I_TRAVEL_DETAILS_M_00'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zdh_trvl_det_d {
  key mandt      : mandt not null;
  key mykey      : sysuuid_x16 not null;
  key travelkey  : sysuuid_x16 not null;
  sequencenumber : abap.numc(3);
  traveldate     : /dmo/begin_date;
  description    : /dmo/description;
  createdby      : syuname;
  createdat      : timestampl;
  lastchangedby  : syuname;
  lastchangedat  : timestampl;
  "%admin"       : include sych_bdl_draft_admin_inc;

}
```
