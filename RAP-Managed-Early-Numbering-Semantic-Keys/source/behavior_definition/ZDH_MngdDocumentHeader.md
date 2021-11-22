```abap
managed implementation in class zcl_bp_dh_mngddocumentheader unique;
strict;

define behavior for ZDH_MngdDocumentHeader alias Header
persistent table zdh_header
lock master
authorization master ( instance )
etag master LastChangeDateTime
early numbering
{
  create;
  update;
  delete;

  association _Item { create; }

  determination Defaults on modify { create; }

  mapping for zdh_header corresponding
  {
    DocumentNumber = doc_number;
    CompanyCode = bukrs;
    PurchasingOrganization = ekorg;
    CreatedBy = ernam;
    CreatedOn = aedat;
    LastChangeDateTime = lastchangedatetime;
  }
}

define behavior for ZDH_MngdDocumentItem alias Item
persistent table zdh_item
lock dependent by _Header
authorization dependent by _Header
etag master LastChangeDateTime
early numbering
{
  update;
  delete;

  field ( readonly ) DocumentNumber;

  association _Header;
  association _Account { create; }

  determination Defaults on modify { create; }

  mapping for zdh_item corresponding
  {
    DocumentNumber = doc_number;
    ItemNumber = item_number;
    ItemDescription = item_descr;
    CreatedBy = ernam;
    CreatedOn = aedat;
    LastChangeDateTime = lastchangedatetime;
  }
}

define behavior for ZDH_MngdDocumentAccount alias Account
persistent table zdh_account
lock dependent by _Header
authorization dependent by _Header
etag master LastChangeDateTime
early numbering
{
  update;
  delete;

  field ( readonly ) DocumentNumber, ItemNumber;

  association _Header;
  association _Item;

  determination Defaults on modify { create; }

  mapping for zdh_account corresponding
  {
    DocumentNumber = doc_number;
    ItemNumber = item_number;
    AccountNumber = account_number;
    CostCenter = kostl;
    CreatedBy = ernam;
    CreatedOn = aedat;
    LastChangeDateTime = lastchangedatetime;
  }
}
```
