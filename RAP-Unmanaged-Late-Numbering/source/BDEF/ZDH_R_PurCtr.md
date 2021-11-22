```abap
unmanaged implementation in class zcl_bp_dh_r_purctr unique;
strict;
define behavior for ZDH_R_PurCtr alias Header
late numbering
lock master
etag master LastChangeDateTime
authorization master ( global, instance )
{
  create;
  update;
  //  delete;

  association _Item { create; }

  mapping for meout_header control meout_headerx corresponding
  {
    PurchaseContract = ebeln;
    purchasecontracttype = bsart;
    PurchasingDocumentCategory = bstyp;
    CreatedByUser = ernam;
    CreationDate = aedat;
    cashdiscount1days = zbd1t;
    cashdiscount1percent = zbd1p;
    cashdiscount2days = zbd2t;
    cashdiscount2percent = zbd2p;
    companycode = bukrs;
    correspncexternalreference = ihrez;
    correspncinternalreference = unsez;
    documentcurrency = waers;
    exchangerate = wkurs;
    exchangerateisfixed = kufix;
    incotermsclassification = inco1;
    incotermslocation1 = inco2_l;
    incotermslocation2 = inco3_l;
    incotermstransferlocation = inco2;
    incotermsversion = incov;
    invoicingparty = lifre;
    netpaymentdays = zbd3t;
    paymentterms = zterm;
    //    PURCHASECONTRACT = konnr;
    purchasecontracttargetamount = ktwrt;
    purchasingdocumentdeletioncode = loekz;
    purchasinggroup = ekgrp;
    purchasingorganization = ekorg;
    quotationsubmissiondate = ihran;
    releasecode = frgke;
    supplierquotation = angnr;
    supplier = lifnr;
    supplierphonenumber = telf1;
    supplierrespsalespersonname = verkf;
    supplyingsupplier = llief;
    validityenddate = kdate;
    validitystartdate = kdatb;
    //    PurgContractIsInPreparation = frgrl;
    PurchasingProcessingStatus = procstat;

  }
}

define behavior for ZDH_R_PurCtrItem alias Item
late numbering
lock dependent by _Header
etag master LastChangeDateTime
authorization dependent by _Header
{

  field ( readonly ) PurchaseContract;

  update;
  //  delete;

  association _Account { create; }
  association _Header;

  mapping for meout_item control meout_itemx corresponding
  {

    PurchaseContract = ebeln;
    purchasecontractitem = ebelp;
    accountassignmentcategory = knttp;
    evaldrcptsettlmtisallowed = xersy;
    goodsreceiptisexpected = wepos;
    goodsreceiptisnonvaluated = weunb;
    invoiceisexpected = repos;
    invoiceisgoodsreceiptbased = webre;
    //    isinforecordupdated = spinf;
    PurchasingInfoRecordUpdateCode = spinf;
    isorderacknrqd = kzabs;
    manualdeliveryaddressid = adrnr;
    material = matnr;
    ManufacturerMaterial = ematn;
    materialgroup = matkl;
    materialtype = mtart;
    multipleacctassgmtdistribution = vrtkz;
    ContractNetPriceAmount = netpr;
    netpricequantity = peinh;
    nodaysreminder1 = mahn1;
    nodaysreminder2 = mahn2;
    nodaysreminder3 = mahn3;
    orderpriceunit = bprme;
    orderpriceunittoorderunitnmrtr = bpumz;
    orderquantityunit = meins;
    ordpriceunittoorderunitdnmntr = bpumn;
    overdelivtolrtdlmtratioinpct = uebto;
    plant = werks;
    priceistobeprinted = prsdr;
    producttypecode = producttype;
    purchasecontractitemtext = txz01;
    purchasingcontractdeletioncode = loekz;
    purchasingdocumentitemcategory = pstyp;
    //    PurchasingPriceIsEstimated = schpr;
    purgdocorderacknnumber = labnr;
    purgdocreleaseorderquantity = abmng;
    requirementtracking = bednr;
    serviceperformer = serviceperformer;
    shippinginstruction = evers;
    stocktype = insmk;
    storagelocation = lgort;
    subcontractor = emlif;
    suppliermaterialnumber = idnlf;
    targetquantity = ktmng;
    taxcode = mwskz;
    underdelivtolrtdlmtratioinpct = untto;
    unlimitedoverdeliveryisallowed = uebtk;
    volumeunit = voleh;

  }
}

define behavior for ZDH_R_PurCtrAccount alias Account
late numbering
lock dependent by _Header
etag master LastChangeDateTime
authorization dependent by _Header
{

  field ( readonly ) PurchaseContract, PurchaseContractItem;

  update;
  //  delete;

  association _Header;
  association _Item;

  mapping for exkn control mepoaccounting_datax corresponding
  {

    AccountAssignment = zexkn;
    budgetperiod = budget_pd;
    businessarea = gsber;
    businessprocess = prznr;
    CommitmentItemShortID = fipos;
    controllingarea = kokrs;
    costcenter = kostl;
    costctractivitytype = lstar;
    costobject = kstrg;
    earmarkedfundsdocument = kblnr;
    fixedasset = anln2;
    functionalarea = fkber;
    fundscenter = fistl;
    glaccount = sakto;
    goodsrecipientname = wempf;
    grantid = grant_nbr;
    isdeleted = loekz;
    jointventurerecoverycode = recid;
    masterfixedasset = anln1;
    multipleacctassgmtdistrpercent = vproz;
    nondeductibleinputtaxamount = navnw;
    orderid = aufnr;
    partneraccountnumber = vptnr;
    profitcenter = prctr;
    projectnetwork = nplnr;
    purgdocnetamount = netwr;
    quantity = menge;
    realestateobject = imkey;
    salesorder = vbeln;
    salesorderitem = vbelp;
    salesorderscheduleline = veten;
    settlementreferencedate = dabrz;
    taxcode = mwskz;
    taxjurisdiction = txjcd;
    unloadingpointname = ablad;


  }
}
```
