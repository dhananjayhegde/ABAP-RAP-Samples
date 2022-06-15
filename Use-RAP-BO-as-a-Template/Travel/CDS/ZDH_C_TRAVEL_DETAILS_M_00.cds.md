```ABAP
@EndUserText.label: 'Projection view for Travel Details model'
@AccessControl.authorizationCheck: #CHECK

@UI: {
    headerInfo: {
        typeName: 'Daywise Details',
        typeNamePlural: 'Daywise Details',
        title: {
            type: #STANDARD,
            value: 'SequenceNumber'
        }
    }
}

@Search.searchable: true
define view entity ZDH_C_TRAVEL_DETAILS_M_00
  as projection on zdh_i_travel_details_m_00
{

      @UI.facet: [
            // General Information Tab
            {   id: 'GeneralInfoTab',
                position: 10,
                label: 'Travel Info',
                type: #COLLECTION
            },
                {   id: 'BasicInfoSection',
                    position: 10,
                    label: 'Basic Data',
                    type: #IDENTIFICATION_REFERENCE,
                    purpose: #STANDARD,
                    parentId: 'GeneralInfoTab'
                },
            {   id: 'DetailsTab',
                position: 20,
                label: 'Details',
                type: #COLLECTION
            },
                {   id: 'DetailsSection',
                    position: 10,
                    label: 'Day Itinarary',
                    type: #IDENTIFICATION_REFERENCE,
                    targetQualifier: 'Details',
                    purpose: #STANDARD,
                    parentId: 'DetailsTab'
                },
            // Datapoint facets
            {
                id: 'DateDataPoint',
                type: #DATAPOINT_REFERENCE,
                targetQualifier: 'TravelDate',
                purpose: #HEADER
            }
       ]
  key Mykey,

  key Travelkey,

      @UI: {
              lineItem:       [ { position: 20, label: 'Travel ID', importance: #HIGH }  ],
              identification: [ { position: 20, label: 'Travel ID' } ] }
      @Search.defaultSearchElement: true
      _Travel.TravelId,

      @UI: {
          lineItem:       [ { position: 10, importance: #HIGH, label: 'Day' }  ],
          identification: [ { position: 10, label: 'Day' } ] }
      SequenceNumber,

      @UI: {
          lineItem:       [ { position: 30, importance: #HIGH, label: 'Date' }  ],
          identification: [ { position: 30, label: 'Date' } ],
          dataPoint: { title: 'Date' } }
      TravelDate,

      @UI: {
          lineItem:       [ { position: 40, importance: #HIGH, label: 'Details', cssDefault.width: '70%' }  ],
          identification: [ { position: 40, qualifier: 'Details' } ],
          multiLineText: true }

      Description,

      @UI.hidden: true
      CreatedBy,

      @UI.hidden: true
      CreatedAt,

      @UI.hidden: true
      LastChangedBy,

      @UI.hidden: true
      LastChangedAt,


      /* Associations */
      _Travel : redirected to parent ZDH_C_TRAVEL_M_00
}

```
