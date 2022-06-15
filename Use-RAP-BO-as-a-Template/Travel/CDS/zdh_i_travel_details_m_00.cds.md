```ABAP
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Travel Details CDS View'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity zdh_i_travel_details_m_00
  as select from zdh_trvl_details
  
  association to parent ZDH_I_TRAVEL_M_00 as _Travel on _Travel.Mykey = $projection.Travelkey
{
  key mykey           as Mykey,
  key travelkey       as Travelkey,
      sequence_num    as SequenceNumber,
      travel_date     as TravelDate,
      description     as Description,
      created_by      as CreatedBy,
      created_at      as CreatedAt,
      last_changed_by as LastChangedBy,
      last_changed_at as LastChangedAt,
      
      // Compositions
      _Travel
      
      // Associations
}

```
