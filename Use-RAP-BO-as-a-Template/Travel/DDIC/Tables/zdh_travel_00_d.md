```ABAP
@EndUserText.label : 'Draft table for entity ZDH_I_TRAVEL_M_00'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zdh_travel_00_d {
  key mandt         : mandt not null;
  key mykey         : sysuuid_x16 not null;
  travelid          : /dmo/travel_id;
  agencyid          : /dmo/agency_id;
  customerid        : /dmo/customer_id;
  begindate         : /dmo/begin_date;
  enddate           : /dmo/end_date;
  @Semantics.amount.currencyCode : 'zdh_travel_00_d.currencycode'
  bookingfee        : /dmo/booking_fee;
  @Semantics.amount.currencyCode : 'zdh_travel_00_d.currencycode'
  totalprice        : /dmo/total_price;
  currencycode      : /dmo/currency_code;
  description       : /dmo/description;
  overallstatus     : /dmo/overall_status;
  statuscriticality : abap.int1;
  createdby         : syuname;
  createdat         : timestampl;
  lastchangedby     : syuname;
  lastchangedat     : timestampl;
  overallstatustext : abap.char(60);
  "%admin"          : include sych_bdl_draft_admin_inc;

}

```
