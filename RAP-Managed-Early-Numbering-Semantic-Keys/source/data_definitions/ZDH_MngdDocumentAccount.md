```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Document Account'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
  serviceQuality: #X,
  sizeCategory: #S,
  dataClass: #MIXED
}
define view entity ZDH_MngdDocumentAccount
  as select from zdh_account as Account

  association to parent ZDH_MngdDocumentItem as _Item   on  _Item.DocumentNumber = $projection.DocumentNumber
                                                        and _Item.ItemNumber     = $projection.ItemNumber
  association to ZDH_MngdDocumentHeader      as _Header on  _Header.DocumentNumber = $projection.DocumentNumber
{
  key doc_number         as DocumentNumber,
  key item_number        as ItemNumber,
  key account_number     as AccountNumber,
      kostl              as CostCenter,
      lastchangedatetime as LastChangeDateTime,
      aedat              as CreatedOn,
      ernam              as CreatedBy,

      _Item,
      _Header

}
```
