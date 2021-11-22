```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Document Header'
define root view entity ZDH_MngdDocumentHeader
  as select from zdh_header as Header
  composition [1..*] of ZDH_MngdDocumentItem as _Item
{
  key doc_number         as DocumentNumber,
      bukrs              as CompanyCode,
      ekorg              as PurchasingOrganization,
      lastchangedatetime as LastChangeDateTime,
      aedat              as CreatedOn,
      ernam              as CreatedBy,

      _Item
}
```
