```ABAP
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Travel Overall Status Value Help'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
  serviceQuality: #X,
  sizeCategory: #S,
  dataClass: #MIXED
}

@ObjectModel.resultSet.sizeCategory: #XS
define view entity ZDH_P_TravelStatusVH
  as select from DDCDS_CUSTOMER_DOMAIN_VALUE_T ( p_domain_name : '/DMO/OVERALL_STATUS' )
{
  key value_low as OverallStatus,
      text      as OverallStatusText
}
where
  language = $session.system_language

```
