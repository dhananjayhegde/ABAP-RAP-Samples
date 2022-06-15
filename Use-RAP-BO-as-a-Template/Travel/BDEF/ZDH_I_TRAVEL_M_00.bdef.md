```ABAP
managed implementation in class zcl_bp_dh_i_travel_m_00 unique;
strict;
with draft;

define behavior for ZDH_I_TRAVEL_M_00 alias Travel with additional save
persistent table zdh_travel_00
draft table zdh_travel_00_d
lock master
total etag LastChangedAt
authorization master ( instance )
{
  create;
  update;
  delete;

  association _Details { create; with draft; }

  // Managed Key - UUID based key
  field ( numbering : managed ) Mykey;

  // Semantic ID field (not a key field) - calculated in determination - GenerateTravelId
  field ( readonly ) TravelId;

  // Admin fields
  field ( readonly ) LastChangedAt, LastChangedBy, CreatedAt, CreatedBy;

  field ( mandatory ) AgencyId, OverallStatus, BookingFee, CurrencyCode, BeginDate, EndDate, CustomerId;

  field ( features : instance ) TotalPrice;

  action ( features : instance ) AcceptTravel; // result [1] $self;
  action ChangeDates parameter ZDH_D_ChangeDateParam;
  factory action Copy [1];

//  action AddFromPackage parameter ZDH_D_TravelPackage_P result [1] $self;

  determination GenerateTravelId on modify { create; }
  determination CalculateTotal on modify { field BookingFee; }

  validation CheckDates on save { field BeginDate, EndDate; }

  // Only for draft
  draft action Edit;
  draft action Discard;
  draft action Activate;
  draft action Resume;

  draft determine action Prepare
  {
    validation CheckDates;
    validation ( always ) Details ~ checkDate;
  }

  mapping for ZDH_TRAVEL_00
  {
    Mykey = mykey;
    TravelId = travel_id;
    Description = description;
    CustomerId = customer_id;
    AgencyId = agency_id;
    BeginDate = begin_date;
    EndDate = end_date;
    BookingFee = booking_fee;
    TotalPrice = total_price;
    CurrencyCode = currency_code;
    CreatedAt = created_at;
    CreatedBy = created_by;
    LastChangedAt = last_changed_at;
    LastChangedBy = last_changed_by;
    OverallStatus = overall_status;
  }

}

define behavior for zdh_i_travel_details_m_00 alias Details
persistent table zdh_trvl_details
draft table zdh_trvl_det_d
lock dependent by _Travel
authorization dependent by _Travel
{
  update;
  delete;

  association _Travel { with draft; }

  // Managed Key - UUID based key
  field ( numbering : managed ) Mykey;

  field ( readonly ) Travelkey;

  // Semantic ID field (not a key field) - calculated in determination - setDefaultValues
  field ( readonly : update ) SequenceNumber;

  // Admin fields
  field ( readonly ) LastChangedAt, LastChangedBy, CreatedAt, CreatedBy;


  static factory action AddItineraryFromPackage parameter ZDH_D_AddItinerary_P [1];

  // Determinations
  determination setDefaultValues on modify { create; }

  // Validations
  validation checkDate on save { create; update; }

  mapping for zdh_trvl_details corresponding
  {
    Mykey = mykey;
    Travelkey = travelkey;
    SequenceNumber = sequence_num;
    TravelDate = travel_date;
    Description = description;
    CreatedAt = created_at;
    CreatedBy = created_by;
    LastChangedAt = last_changed_at;
    LastChangedBy = last_changed_by;
  }

}

```
