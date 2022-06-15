```ABAP
@EndUserText.label: 'Projection view for Travel model'
@AccessControl.authorizationCheck: #NOT_REQUIRED

@UI: {
    headerInfo: {
        typeName: 'Travel',
        typeNamePlural: 'Travels',
        title: {
            type: #STANDARD,
            value: 'TravelId'            
        },
        description: { 
            value: 'Description'
        }
    }
}

@Search.searchable: true

define root view entity ZDH_C_TRAVEL_M_00
  provider contract transactional_query
  as projection on ZDH_I_TRAVEL_M_00
{

      @UI.facet: [
        // General Information Tab
        {   id: 'GeneralInfoTab',
            position: 10,
            label: 'Travel Info',
            type: #COLLECTION
        },
            {   id: 'TravelGeneralSection',
                position: 10,
                label: 'Basic Data',
                type: #IDENTIFICATION_REFERENCE,
                purpose: #STANDARD,
                parentId: 'GeneralInfoTab'
            },
            {   id: 'ImportantDates',
                position: 20,
                label: 'Import Dates',
                type: #FIELDGROUP_REFERENCE,
                purpose: #STANDARD,
                parentId: 'GeneralInfoTab',
                targetQualifier: 'Dates'
            },
        {
            id: 'TravelDetailsFacet',
            position: 30,
            label: 'Daywise Details',
            type: #LINEITEM_REFERENCE,
            targetElement: '_Details'
        },

        // Fares Tab
        {   id: 'FaresTab',
            position: 20,
            label: 'Fare Details',
            type: #COLLECTION
        },
            {   id: 'FaresSection',
                position: 20,
                label: 'Fares',
                type: #FIELDGROUP_REFERENCE,
                purpose: #STANDARD,
                parentId: 'FaresTab',
                targetQualifier: 'FaresGroup'
            },

        // Data point facets
        {
            id: 'StatusDatapoint',
            type: #DATAPOINT_REFERENCE,
            targetQualifier: 'OverallStatus',
            purpose: #HEADER
        },
        
        {
            id: 'BookingFeeDatapoint',
            type: #DATAPOINT_REFERENCE,
            targetQualifier: 'BookingFee',
            purpose: #HEADER
        },
        
        {
            id: 'StartingDateDataPointFacet',
            type: #DATAPOINT_REFERENCE,
            targetQualifier: 'BeginDate',
            purpose: #HEADER
        }
      ]

      @UI.hidden: true
  key Mykey,

      @UI: {
          lineItem:       [ { position: 10, importance: #HIGH },
                            { type: #FOR_ACTION, position: 10, dataAction: 'Copy',        label: 'Copy' },
                            { type: #FOR_ACTION, position: 20, dataAction: 'ChangeDates', label: 'Change Dates' }  ],
          identification: [ { position: 10, label: 'Travel ID [1,...,99999999]' },
                            { type: #FOR_ACTION, dataAction: 'AddFromPackage', label: 'Add Itinerary from Package' },
                            { type: #FOR_ACTION, dataAction: 'ChangeDates', label: 'Change Dates' } ] }
      @Search.defaultSearchElement: true
      TravelId,

      @UI: {
          lineItem:       [ { position: 20, importance: #HIGH } ],
          identification: [ { position: 20 } ],
          selectionField: [ { position: 20 } ] }
      @Consumption.valueHelpDefinition: [{ entity : {name: '/DMO/I_Agency', element: 'AgencyID'  } }]

      @ObjectModel.text: { element: ['AgencyName'] }
      @Search.defaultSearchElement: true
      AgencyId,
      _Agency.Name       as AgencyName,

      @UI: {
          lineItem:       [ { position: 30, importance: #HIGH } ],
          identification: [ { position: 30 } ],
          selectionField: [ { position: 30 } ] }
      @Consumption.valueHelpDefinition: [{ entity : {name: '/DMO/I_Customer', element: 'CustomerID'  } }]

      @ObjectModel.text.element: ['CustomerName']
      @Search.defaultSearchElement: true
      CustomerId,

      @UI.hidden: true
      _Customer.LastName as CustomerName,

      @UI: {
          lineItem:   [ { position: 40, importance: #MEDIUM } ],
          fieldGroup: [{ qualifier: 'Dates', position: 10 }],
          dataPoint: { title: 'Strting' } }
      BeginDate,

      @UI: {
          lineItem:   [ { position: 41, importance: #MEDIUM } ],
          fieldGroup: [{ qualifier: 'Dates', position: 20 }]}
      EndDate,

      @UI: {
          lineItem:   [ { position: 50, importance: #MEDIUM } ],
          fieldGroup: [{ qualifier: 'FaresGroup', position: 10 }],
          dataPoint: { title: 'BookingFee' }
      }
      @Semantics.amount.currencyCode: 'CurrencyCode'
      BookingFee,

      @UI: {
          lineItem:   [ { position: 60, importance: #MEDIUM } ],
          fieldGroup: [{ qualifier: 'FaresGroup', position: 20 }]  }
      @Semantics.amount.currencyCode: 'CurrencyCode'
      TotalPrice,
      
      @Consumption.valueHelpDefinition: [{ entity: { name: 'I_Currency', element: 'Currency' } }]
      CurrencyCode,

      @UI: {
       // lineItem:   [ { position: 15, importance: #HIGH } ],
        identification: [ { position: 80, label: 'Description' } ]
      }
      Description,

      @UI: {
        lineItem:       [ { position: 70, importance: #HIGH },
                          { type: #FOR_ACTION, dataAction: 'AcceptTravel', label: 'Accept Travel' } ],
        identification: [ { position: 70, label: 'Status [O(Open)|A(Accepted)|X(Canceled)]' } ],
        selectionField: [ { position: 20 } ],
        dataPoint: { title: 'Status', criticality: 'StatusCriticality' }
      }

      @Consumption.valueHelpDefinition: [{
        entity: {
          name: 'ZDH_P_TravelStatusVH',
          element: 'OverallStatus'
        }
      }]
      @ObjectModel.text.element: ['OverallStatusText']
      @UI.textArrangement: #TEXT_LAST
      @ObjectModel.filter.enabled: true
      OverallStatus,
      StatusCriticality,
      OverallStatusText,

      //      CreatedBy,
      //      CreatedAt,
      //      LastChangedBy,

      @UI.hidden: true
      LastChangedAt,
      
      // Compositions
      _Details : redirected to composition child ZDH_C_TRAVEL_DETAILS_M_00,

      /* Associations */
      _Agency,
      _Currency,
      _Customer,
      _TravelStatus
}

```
