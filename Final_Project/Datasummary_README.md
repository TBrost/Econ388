DB1BTicket
This table contains summary characteristics of each domestic itinerary on the Origin and Destination Survey, including the reporting carrier, itinerary fare, number of passengers, originating airport, roundtrip indicator, and miles flown.

Field Name: Description -- Only Variables marked with a "*" have been included--
*ItinID: Itinerary ID		 
Coupons: Number of Coupons in the Itinerary		 
*Year: Year		 
*Quarter:	Quarter (1-4)		 
Origin: Origin Airport Code		 
*OriginAirportID:	Origin Airport, Airport ID. An identification number assigned by US DOT to identify a unique airport. Use this field for airport analysis across a range of years because an airport can change its airport code and airport codes can be reused.
*OriginAirportSeqID:	Origin Airport, Airport Sequence ID. An identification number assigned by US DOT to identify a unique airport at a given point of time. Airport attributes, such as airport name or coordinates, may change over time.		 
*OriginCityMarketID:	Origin Airport, City Market ID. City Market ID is an identification number assigned by US DOT to identify a city market. Use this field to consolidate airports serving the same city market.		 
OriginCountry:	Origin Airport, Country		 
OriginStateFips:	Origin Airport, State FIPS		 
OriginState:	Origin Airport, State		 
OriginStateName:	Origin State Name		 
OriginWac:	Origin Airport, World Area Code		 
*RoundTrip:	Round Trip Indicator (1=Yes)		 
OnLine:	Single Carrier Indicator (1=Yes)		 
DollarCred:	Dollar Credibility Indicator		 
*FarePerMile:	Itinerary Fare Per Miles Flown in Dollars (ItinFare/MilesFlown). -ITIN_YIELD-
RPCarrier:	Reporting Carrier		 
*Passengers:	Number of Passengers		 
*ItinFare:	Itinerary Fare Per Person		 
BulkFare:	Bulk Fare Indicator (1=Yes)		 
Distance:	Itinerary Distance (Including Ground Transport)		 
*DistanceGroup:	Distance Group, in 500 Mile Intervals		 
*MilesFlown:	Itinerary Miles Flown (Track Miles)		 
*ItinGeoType:	Itinerary Geography Type, 0	= International, 1 = Non-contiguous Domestic (Includes Hawaii, Alaska and Territories), 2	= Contiguous Domestic (Lower 48 U.S. States Only)

