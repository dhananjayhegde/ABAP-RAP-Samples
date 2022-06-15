```ABAP
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'CDS Data Model for Travel'
define root view entity ZDH_I_TRAVEL_M_00
  as select from zdh_travel_00
  
  composition [0..*] of zdh_i_travel_details_m_00 as _Details

  association [0..1] to /DMO/I_Agency        as _Agency       on $projection.AgencyId = _Agency.AgencyID
  association [0..1] to /DMO/I_Customer      as _Customer     on $projection.CustomerId = _Customer.CustomerID
  association [0..1] to I_Currency           as _Currency     on $projection.CurrencyCode = _Currency.Currency
  association [0..1] to ZDH_P_TravelStatusVH as _TravelStatus on $projection.OverallStatus = _TravelStatus.OverallStatus
{
  key mykey           as Mykey,
      travel_id       as TravelId,
      agency_id       as AgencyId,
      customer_id     as CustomerId,
      begin_date      as BeginDate,
      end_date        as EndDate,
      booking_fee     as BookingFee,
      total_price     as TotalPrice,
      currency_code   as CurrencyCode,
      description     as Description,
      overall_status  as OverallStatus,
      case overall_status
        when 'O' then 2
        when 'X' then 1
        else 3
        end as StatusCriticality,

      @Semantics.user.createdBy: true
      created_by      as CreatedBy,
      @Semantics.systemDateTime.createdAt: true
      created_at      as CreatedAt,
      @Semantics.user.lastChangedBy: true
      last_changed_by as LastChangedBy,
      @Semantics.systemDateTime.lastChangedAt: true
      last_changed_at as LastChangedAt,
      
      _TravelStatus.OverallStatusText as OverallStatusText,

      // Compositions
      _Details,
      
      /* associations */
      _Agency,
      _Customer,
      _Currency,
      _TravelStatus
}


```
