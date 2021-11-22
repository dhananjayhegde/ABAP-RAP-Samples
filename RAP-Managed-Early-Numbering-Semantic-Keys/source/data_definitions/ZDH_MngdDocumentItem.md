```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Document Item'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
  serviceQuality: #X,
  sizeCategory: #S,
  dataClass: #MIXED
}
define view entity ZDH_MngdDocumentItem
  as select from zdh_item as Item

  composition [0..*] of ZDH_MngdDocumentAccount as _Account
  association to parent ZDH_MngdDocumentHeader         as _Header on _Header.DocumentNumber = $projection.DocumentNumber
{
  key doc_number         as DocumentNumber,
  key item_number        as ItemNumber,
      item_descr         as ItemDescription,
      lastchangedatetime as LastChangeDateTime,
      aedat              as CreatedOn,
      ernam              as CreatedBy,

      _Account,
      _Header
}
```
