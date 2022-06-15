```ABAP
projection;
strict;
use draft;

define behavior for ZDH_C_TRAVEL_M_00 alias Travel
use etag
{
  use create;
  use update;
  use delete;

  use association _Details { create; with draft; }

  use action AcceptTravel;
  use action ChangeDates;
  use action Copy;

//  use action AddFromPackage;

  use action Edit;
  use action Discard;
  use action Resume;
  use action Activate;

  use action Prepare;
}

define behavior for ZDH_C_TRAVEL_DETAILS_M_00 alias Details
use etag
{
  use update;
  use delete;

  use association _Travel { with draft; }

  use action AddItineraryFromPackage;
}
```
