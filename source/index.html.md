---
title: Valassis Q2C API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python

toc_footers:
  - <a href='http://developer.valassis.com'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors
search: true
headingLevel: 4
---

# Introduction

Welcome to the Valassis Q2C API! You can use our API to access various API endpoints, which allow you to perform key Business functions and exchange information on Valassis Products and Services.

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.


# Authentication
## POST Token

> To authorize, use this code:

```ruby
require "uri"
require "net/http"

url = URI("https://api.valassis.com/token")

https = Net::HTTP.new(url.host, url.port)
https.use_ssl = true

request = Net::HTTP::Post.new(url)
request["Authorization"] = "Basic <Base64-encoded-client_key:client_secret>"
request["Content-Type"] = "application/x-www-form-urlencoded"
request.body = "grant_type=client_credentials"

response = Net::HTTP.start(uri.hostname, uri.port, req_options) do |http|
  http.request(request)
end

# response.code
# response.body
```


```python
import requests
url = 'https://api.valassis.com/token'
payload = 'grant_type=client_credentials'
headers = {
  'Authorization': 'Basic <Base64-encoded-client_key:client_secret>',
  'Content-Type': 'application/x-www-form-urlencoded'
}
response = requests.request('POST', url, headers = headers, data = payload, allow_redirects=False, timeout=undefined, allow_redirects=false)
print(response.text)
```

```shell
curl -X GET https://api.valassis.com/token/ \
  -H "Authorization: Basic <Base64-encoded-client_key:client_secret>" \
  -D "grant_type=client_credentials"  
```

> Make sure to replace `(client-key:client-secret)` with your specific key pair based on Production or Sandbox ENV.

> The above command returns JSON structured like this:

```json
{
    "access_token": "4cbff4e3-5a1c-3e4c-9315-3b4db223d160",
    "scope": "am_application_scope default",
    "token_type": "Bearer",
    "expires_in": 3600
}
```


Valassis requires the use of OAuth2.0 Access Tokens to access the API. Access Tokens are generated from Token Request calls made using a Customer Key and Customer Secret. You maybe eligible to register for a Client Key and Client Secret at our [developer portal](http://developer.valassis.com).

Alternatively the Customer Key and Customer Secret with be provided to you by a Valassis Implementation Representative.

You'll be given two sets of ClientKey:ClientSecret credentials.  One for Production and a second for QA/Sandbox testing.  Credentials must be Base64 encoded before placing into Authorization Header.

Example of Base64 encoded client-key:client-secret **wU62DjlyDBnq87GlBwplfqvmAbAa:ksdSdoefDDP7wpaElfqvmjDue**

  * Authorization Notes:
    * The credentials must be passed in the “Authorization” header in a Token request before making the API requests.
    * Upon receiving the correct credentials, authorization server issues an access token called “bearer token” as below.
    * All subsequent API requests must use this <access_token> in the http “authorization header”
    * The access token has a definite time to live (TTL) or expires_in ( by default its 3600 seconds), and until it meets the expires_in ,the client will get the same token for the Token requests.
    * The client must have logic in the code to track and handle the expires_in , and must make a new request to get the next token

`Authorization: Bearer: <access_token>`

<aside class="warning">
You must replace <code>access_token</code> with token value received from the POST Token Call.
</aside>

# Availability
## GET - Availability

```shell
# You can also use wget
curl -X GET https://api.valassis.com/Availabilty/2.0/{Product}/{Startdate}/{Enddate}/search? \
      q=availabilityDetail&geocode=48152B1&fields=fields.cardigent \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <access_token>'

```

```ruby
require 'rest-client'
require 'json'

headers = {
  'Accept' => 'application/json',
  'Authorization' => 'Bearer <access_token>'
}

result = RestClient.get 'https://api.valassis.com/Availabilty/2.0/{Product}/{Startdate}/{Enddate}/search',
  params: {
  }, headers: headers

p JSON.parse(result)

```

```python
import requests
headers = {
  'Accept': 'application/json',
  'Authorization': 'access_token'
}

r = requests.get('https://api.valassis.com/Availabilty/2.0/{Product}/{Startdate}/{Enddate}/search', params={

}, headers = headers)

print r.json()

```


This endpoint retrieves availability for each geography within date range.

### HTTPS Request

`GET http://api.valassis.com/Availability/2.0/VDP/{Product}/{Startdate}/{Enddate}/search?q=availabilityDetail&geocode=48152B1&fields=fields.cardigent`

### Path Parameters
|Parameter|Type|Required|Description|
|---|---|---|---|
|Product|string|true|Valassis Product Name "(**VDP**, WRAP, ALL INS_SHARED)"|
|Startdate|string|true|In Home Week (Sunday) Start Date **"YYYY-MM-DD"**|
|Enddate|string|true|In Home Week End Date **"YYYY-MM-DD"**|

<aside class="notice">
Date ranch used for Startdate & Enddate must cross a Sunday Date - Best practice is to always use Sunday dates for the IHW values.
</aside>

### Query Parameters
|Parameter|Type|Required|Description|
|---|---|---|---|
|q|string|true|**"availabilityDetail"**|
|**geocode**|string|false|5-Digit **ZIP** or 8-Digit Valassis **ATZ**. Multiple values separated by ","|
|**stageId**|Numeric|false|When availability request URI will exceed 8190 characters limit due to high volume of geocodes. "348"|
|fields|string|true|Query Results Filter **"fields.cardigent"**|
|isAvailable|Numeric|false| If not used - returns Availability for all geographies. "1" - Returns only Available Geos. "0" - Returns only Unavailable Geos.|

<aside class="notice">
Use either <code>geocode</code> or <code>stageId</code> parameters, not both.  StageId is used only if the AvailabilityHelper service was previously used.
</aside>
### Responses
> The above command returns JSON structured like this:

```json
{
    "payload": {
        "total": 1,
        "records": 4,
        "pageSize": 4,
        "page": 1,
        "rows": [
            {
                "productCode": "VDP",
                "geoCode": "91604B1",
                "ihw": "2019-12-08T12:12:00Z",
                "isAvailable": 1,
                "inHomeDate": "2019-12-10T12:12:00Z",
                "primaryDeliveryMethod": "MAIL",
                "subDeliveryMethod": "MAIL",
                "hhc": 4507
            },
            {
                "productCode": "VDP",
                "geoCode": "91604B1",
                "ihw": "2019-12-01T12:12:00Z",
                "isAvailable": 1,
                "inHomeDate": "2019-12-03T12:12:00Z",
                "primaryDeliveryMethod": "MAIL",
                "subDeliveryMethod": "MAIL",
                "hhc": 4506
            },
            {
                "productCode": "VDP",
                "geoCode": "91604C1",
                "ihw": "2019-12-08T12:12:00Z",
                "isAvailable": 1,
                "inHomeDate": "2019-12-10T12:12:00Z",
                "primaryDeliveryMethod": "MAIL",
                "subDeliveryMethod": "MAIL",
                "hhc": 3640
            },
            {
                "productCode": "VDP",
                "geoCode": "91604C1",
                "ihw": "2019-12-01T12:12:00Z",
                "isAvailable": 1,
                "inHomeDate": "2019-12-03T12:12:00Z",
                "primaryDeliveryMethod": "MAIL",
                "subDeliveryMethod": "MAIL",
                "hhc": 3640
            }
        ]
    },
    "exception": null,
    "returnCode": 200
}
```

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Availability call call was successful|[Availability Response](#schemaavailabilityresponse)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request - See the AvailabilityResponse object for further information|None|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Server Error - The JSON file could not be processed due to errors which occurred on the Valassis server side. Please notify Valassis IT Support.|None|

<a id="schemaavailabilityresponse"></a>
### Availability Response Schema


|Name|Type|Description|
|---|---|---|
|payload|[[Payload](#schemapayload1)]|Shell object which holds final result/result set|
|exception|object|**"Null"** or reason of exception|
|returnCode|integer(int32)|Http Status Code|

<a id="schemapayload1"></a>
### Payload


*Shell object which holds final result/result set from the Availability call.*

|Name|Type|Description|
|---|---|---|
|total|integer(int32)|Number of Payloads returned|
|records|integer(int32)|Number of Records returned|
|pageSize|integer(int32)|Number of Records returned|
|page|integer(int32)|Number of Payload Properties returned|
|rows|[[Rows](#schemarows1)]|Array of GEOs by In Home Date|

<a id="schemarows1"></a>
### Rows

*Records will be retunred for each in home date within Startdata and Enddate for each geography.*

|Name|Type|Description|
|---|---|---|
|productCode|string|Valassis Product **"VDP"**|
|geoCode|string|**ZIP** or **ATZ** Value|
|ihw|string|In home Week date (Sunday Date & Time)  (ISO8601-EXT Format)|
|isAvailable|integer(int32)|"1" available "0" not available|
|inHomeDate|string|Day of week Product is scheduled for delivery  (ISO8601-EXT Format)|
|primaryDeliveryMethod|string|Delivery Method - Internal use|
|subDeliveryMethod|string|Delivery Method - Internal use|
|hhc|integer(int32)|Number of House Holds in the Geography|

--------------------------------------------------------------------------------
## POST - AvailabilityHelper

```shell
# You can also use wget
curl -X POST \
  https://api.valassis.com/AvailabilityHelper/1.0/stage \
  -H 'Content-Type: application/json'
  -H 'Authorization: Bearer <access_token>'

```

```ruby
require 'rest-client'
require 'json'

headers = {
  'Content-Type' => 'application/json',
  'Authorization' => 'Bearer <access_token>'
}

result = RestClient.get 'https://api.valassis.com/AvailabilityHelper/1.0/stage',
  params: {
  }, headers: headers

p JSON.parse(result)

```

```python
import requests
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'access_token'
}

r = requests.get('https://api.valassis.com/AvailabilityHelper/1.0/stage', params={

}, headers = headers)

print r.json()

```


This endpoint stages large list of Geographies to pull Availability within date range.

### HTTPS Request

`POST https://api.valassis.com/AvailabilityHelper/1.0/stage`

### Parameters
|Parameter|Type|Required|Description|
|---|---|---|---|---|
|body|[AvailabilityHelperRequest](#schemaavailabilityhelperrequest)|true|Body containing Availability Request data|

<a id="schemaavailabilityhelperrequest"></a>
### AvailabilityHelper Request Schema

*Payload parameters required to stage Availability request data*

```json
{
   "productCode":"VDP",
   "fromDate":"2018-08-05",
   "toDate":"2018-12-30",
   "geographies":["91604C1","91604B1”, “91604D1”, “91607D1”, “91423B1”, “91423G1”, “91604F1”, “91607C1",    "91602B1”, “91423C1”, “91601C1”, “91423D1”, “91607B1”,
     “90068B1”, “91605D1”, “90069C1", "91411C1”, “90046B1”, “91505B1",    "90046F1”, “90046K1”, “91405D1”, “91405G1”, “90068C1”, “90028B1”, “90048C1”, “91605F1”,
     “91504D1”, “90036F1”, “91405C1", "91402G1”, “91406B1”, “90211”, “91406C1”, “90028D1”, “91436C1”, “90048D1”, “90212C1”, “90036C1”, “90212B1”, “90028F1”,
     “91504B1”, “90038B1”, “90024F1", "91502”, “90067”, “90024G1”, “90036G1”, “90049F1”, “91402D1”, “90035C1”, “90049G1”, “91402F1”, “90024B1”, “90038D1”,
     “90036B1”, “90027B1”, “90024D1”, “91504C1”, “91406G1”, “90036D1”, “90035B1”, “90004F1”, “91331B1”, “90025F1",    "90027F1”, “91201C1”, “91316C1”, “90027C1”,
     “91352D1”, “90035D1”, “90004B1”, “90029C1”, “91402C1”, “90019J1”, “91406F1”, “91343B1”, “90020G1”, “91316D1”, “90027J1”, “90064C1”, “90025B1”, “90027D1”,
     “91316B1”, “90049C1”, “90019G1”, “91201B1”, “91501C1”, “91352C1”, “90029B1”, “91402B1”, “90005F1”, “90004G1”, “90025C1”, “91406D1”, “90034C1”, “90019C1”,
     “90019K1”, “91501B1”, “90034J1”, “90064B1”, “91331D1”, “90020F1”, “90027G1”, “90010”, “90025D1”, “91331G1”, “91343C1”, “90029D1”, “90049D1”, “91356B1”,
     “91343F1”, “90004C1”, “90020C1”, “91202C1”, “90049B1”, “90016F1”, “90004D1”, “90034F1”, “90005D1”, “90025G1”, “90019F1”, “91203”, “90039D1”, “90034B1”,
     “91335G1”, “90039B1”, “90020B1”, “91204”, “90019D1”, “90026D1”, “91202B1”, “91356C1”, “90016B1”, “90064D1”, “90005B1”, “90034G1”, “91335B1”, “90019B1”,
     “90006D1”, “90020D1”, “90016D1”, “91040C1”, “90034D1”, “90026G1”, “91325B1”, “90404C1”, “91343D1”, “90232C1”, “90403D1”, “91331F1”, “91335D1”,
     “91356D1”, “90018B1"
   ],
   "isAvailable":"1"
}
```

### Properties
<aside class="notice">
Property values in <code>BOLD</code> should be used as default values
</aside>

|Parameter|Type|Required|Description|
|---|---|---|---|
|productCode|string|true|Valassis Product Name "(**VDP**, WRAP, ALL INS_SHARED)"|
|FromDate|string|true|In Home Week (Sunday) Start Date **"YYYY-MM-DD"**|
|toDate|string|true|In Home Week End Date **"YYYY-MM-DD"**|
|**geocode**|string|false|5-Digit **ZIP** or 8-Digit Valassis **ATZ**. Multiple values separated by ","|
|isAvailable|Numeric|false| If not used - returns Availability for all geographies. "1" - Returns only Available Geos. "0" - Returns only Unavailable Geos.|


### Responses
> The above command returns JSON structured like this:

```json
{
    "payload": "6988",
    "exception": null,
    "returnCode": 200
}
```

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|AvailabilityHelper call was successful|[AvailabilityHelper Response](#schemaavailabilityhelperresponse)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request - See the AvailabilityHelperResponse object for further information|None|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Server Error - The JSON file could not be processed due to errors which occurred on the Valassis server side. Please notify Valassis IT Support.|None|

<a id="schemaavailabilityhelperresponse"></a>
### AvailabilityHelper Response Schema

|Name|Type|Description|
|---|---|---|
|payload|Number|**stageId** value - used in Availability API query parm.|
|exception|object|**"Null"** or reason of exception|
|returnCode|integer(int32)|Http Status Code|


--------------------------------------------------------------------------------

# MediaPlan
## POST - Create Mediaplan
```shell
# You can also use wget
curl -X POST https://api.valassis.com/Mediaplan/1.0/emptymediaplan \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <access_token>'

```

```ruby
require 'rest-client'
require 'json'

headers = {
  'Content-Type' => 'application/json',
  'Accept' => 'application/json'
}

result = RestClient.post 'https://api.valassis.com/Mediaplan/1.0/emptymediaplan',
  params: {
  }, headers: headers

p JSON.parse(result)

```

```python
import requests
headers = {
  'Content-Type': 'application/json',
  'Accept': 'application/json'
}

r = requests.post('https://api.valassis.com/Mediaplan/1.0/emptymediaplan', params={

}, headers = headers)

print r.json()

```


Builds Media Plan with Valassis backends systems.  Media Plan is used to define product and geo details needed to create the final order.  Media Plan details stay within Valassis backend systems and or not returned to call application.

<aside class="notice">
Separate(multiple) Mediaplans are required for each change in IHD and Product.
</aside>
### HTTPS Request

`POST http://api.valassis.com/Mediaplan/1.0/emptymediaplan`



### Parameters
|Parameter|Type|Required|Description|
|---|---|---|---|---|
|body|[MediaplanRequest](#schemamediaplanrequest)|true|Body containing Media Plan Request data|



<a id="schemamediaplanrequest"></a>
### Mediaplan Request Schema

*Payload parameters required to create standard Mediaplan:
- Creates Media Plan details within Valassis backend systems as input into Inventory Reserve and Ordering.
- THIS VERSION APPLIES TO VDP PRODUCT ONLY.
- Synchronous API Call*

```json
{  
   "username":"TBD@valassis.com.dev",
   "targetingProfile":{  
      "name":"TBD Order",
      "description":null,
      "clientId":"TBD",
      "promoPeriodStartDate":"2019-01-04T12:00:00.0000",
      "promoPeriodEndDate":"2019-01-04T12:00:00.0000",
      "preferredDate":"2019-01-04T12:00:00.0000",
      "sites":[  
         {  
            "xcoord":-118.4027079,
            "ycoord":34.143063,
            "name":"Pizza Palace",
            "address":"12345 Ventura Blvd",
            "city":"Studio City",
            "state":"CA",
            "zip":"91604"
         }
      ]
   },
   "geocodes":["91604C1","91604B1"],
   "productAllocations":[  
      {  
         "productCode":"VDP",
         "sfdcProductCd":"VDP_BASIC"
      }
   ]
}
```
### Properties
<aside class="notice">
Property values in <code>BOLD</code> should be used as default values
</aside>
|Name|Type|Required|Description|
|---|---|---|---|---|
|username|string|true|A unique user name will be provided to you during implementation |
|targetingProfile|[[TargetingProfile](#schematargetingprofile)]|true|Array of Media Plan properties|
|geocodes|[string]|true|Geographies being requested. Can be single or multiple GEOs.|
|productAllocations|[[ProductAllocations](#schemaproductallocations)]|true|Array|

<a id="schematargetingprofile"></a>
### TargetingProfile
*Object contains the properties required to create a standard Mediaplan within Valassis.*

|Name|Type|Required|Description|
|---|---|---|---|---|
|name|string|false|A unique user name will be provided to you during implementation.|
|description|object|false|**"null"**|
|clientId|string|false|A unique 3-Digit value will be provided to you during implementation.|
|promoPeriodStartDate|string|true|IHD - See Availability IHD Property.  (ISO8601-EXT Format)|
|promoPeriodEndDate|string|true|IHD - See Availability IHD Property. (ISO8601-EXT Format)|
|preferredDate|string|true|IHD - See Availability IHD Property. **Must be Future Dated**  (ISO8601-EXT Format)|
|sites|[[Sites](#schemasites)]|false|Array of Store locations to build Media Plan around (optional).|

<a id="schemasites"></a>
### Sites(optional)

*Crate a Site object for each store location in MediaPlan.*

|Name|Type|Required|Description|
|---|---|---|---|---|
|xcoord|number(double)|false|Geocode longitude of location.|
|ycoord|number(double)|false|Geocode latitude of location.|
|name|string|false|Location Name|
|address|string|false|Location street address|
|city|string|false|Location City|
|state|string|false|Location State|
|zip|string|false|Location Zip|

<a id="schemaproductallocations"></a>
###Product Allocations
*Describes the Valassis product attributes of the Mediaplan being created.*

|Name|Type|Required|Description|
|---|---|---|---|---|
|productCode|string|true|Valassis Product Name "(**VDP**, WRAP, ALL INS_SHARED)"|
|sfdcProductCd|string|false|Valassis Salesforce Product Code **"(VDP_BASIC)"**|


###Responses
> The above command returns JSON structured like this:

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Create MediaplanRequest call successful|[MediaplanResponse](#schemamediaplanresponse)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request - See the MediaplanResponse object for further information|None|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Server Error - The JSON file could not be processed due to errors which occurred on the Valassis server side. Please notify Valassis IT Support.|None|

-----------

<a id="schemamediaplanresponse"></a>
### Mediaplan Response Schema

```json
{
    "payload": {
        "mediaPlanId": "96695",
        "mediaPlanGroupId": "48471"
    },
    "exception": null,
    "returnCode": null
}
```

|Name|Type|Description|
|---|---|---|---|
|payload|[PayloadResponse](#schemapayloadresponse)|Shell object which holds final result/result set|
|exception|object|**"Null"** or reason of exception|
|returnCode|integer(int32)|Http Status Code - **"Null"** if successful|

<a id="schemapayloadresponse"></a>
### Payload Response

*The mediaPlanGroupId should be used when creating a Valassis Order*

|Name|Type|Description|
|---|---|---|---|
|mediaPlanId|string|Media Plan ID|
|mediaPlanGroupId|string|Media Plan Group ID associates several individual Mediaplans together.  |


------------------------------------------------------------------------------
# Inventory
## POST - Reserve Product Inventory


> Code samples

```shell
# You can also use wget
curl -X POST https://api.valassis.com/Inventory/1.0/reserve \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <access_token>'\
  -D {  "mediaPlanId": "string",
        "accountNumber": 0
      }
```

```ruby
require 'rest-client'
require 'json'
  headers = {
  'Content-Type' => 'application/json',
  'Accept' => 'application/json'
}
  result = RestClient.post 'https://api.valassis.com/Inventory/1.0/reserve',
  params: {
  }, headers: headers
  p JSON.parse(result)
  ```
  ```python
import requests
headers = {
    'Content-Type': 'application/json',
  'Accept': 'application/json'
}
  r = requests.post('https://api.valassis.com/Inventory/1.0/reserve', params={
}, headers = headers)
  print r.json()
```

Places a hold on the selected geographies within a defined Media Plan so that inventory is not consumed by other applications during the formal Order process.

Orders submitted to Valassis using the exposed Order API rely on scheduled processes to finalize the Order details within our Order Management and Fulfillment systems.  As result the Order Service API is asynchronous.  While the Order Service API returns a response the Order is not actually completely submitted until these scheduled processes execute.  To avoid possible loss of inventory within the desired products availability the InventoryReserve Service API was created.   This service marks the requested inventory as unavailable which prevents other Valasssis order systems from booking the same inventory.   InventoryReserve service should be used once the client has selected their delivery date and Geographies.   

<aside class="warning">
Reserving Inventory places a "hard" hold on the Availability for the given geographies and dates. There is currently no API method/endpoint to release the reserved inventory in the event a followup Order is not made.  Contact Valassis Operations Support to clear the reservation.   
</aside>

### HTTPS Request

`POST https://api.valassis.com/Inventory/1.0/reserve`


### Parameters
|Parameter|Type|Required|Description|
|---|---|---|---|---|
|body|[InventoryReserveRequest](#schemainventoryreserverequest)|true|Body containing Inventory Reserve Request data|

<aside class="notice">
Separate(multiple) Inventory Reserve calls are required for each Mediaplan created.
</aside>

### Schemas

<a id="schemainventoryreserverequest"></a>
### Inventory Reserve Request Schema

*Inventory Reserve Request requires the mediaPlanId returned as part of the emptymediaplan call.  This call should be made for each unique Mediaplan created.  The Valassis account number will be provided by a Valassis Implementation Resource during on-boarding activities*

```json
{
  "mediaPlanId": "string",
  "accountNumber": 0
}
```

### Properties
<aside class="notice">
Property values in <code>BOLD</code> should be used as default values
</aside>

|Name|Type|Required|Description|
|---|---|---|---|---|
|mediaPlanId|string|true|Valassis MediaPlanID Number.  Returned from MediaPlan Service.|
|accountNumber|number|true|Valassis Account Number that the Order will be placed under.|

> The above command returns JSON structured like this:

### Response
|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|InventoryReserveRequest call Successful|[InventoryReserveResponse](#schemainventoryreserveresponse)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request - See the InventoryReserveResponse object for further information|None|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Server Error - The JSON file could not be processed due to errors which occurred on the Valassis server side. Please notify Valassis IT Support.|None|

<a id="schemainventoryreserveresponse"></a>
### Inventory Reserve Response Schemas

```json
{
  "payload": {
    "consumeReqStageId": "string",
    "val3cpConsumeInvDtls": {
      "ref": "string",
      "consumeReqStageId": "string",
      "geography": "string",
      "ihw": "string",
      "ihd": "string",
      "custAccountId": "string",
      "status": "string",
      "consumedFlag": "string",
      "errorFlag": "string",
      "errorDescription": "string",
      "createdBy": "string",
      "creationDate": "string",
      "lastUpdatedBy": "string",
      "lastUpdateDate": "string",
      "lastUpdateLogin": "string"
    }
  },
  "exception": {},
  "returnCode": {}
}
```

|Name|Type|Description|
|---|---|---|---|
|payload|[PayloadResponse](#schemainv_payloadresponse)|Shell object which holds final result/result set|
|exception|object|"null" or reason of exception|
|returnCode|integer(int32)|Http Status Code - "null" if successful|



<a id="schemainv_payloadresponse"></a>
### PayloadResponse
|Name|Type|Description|
|---|---|---|---|
|consumeReqStageId|string|ERP Primary Key – Internal Valassis use Only.|
|val3cpConsumeInvDtls|[val3cpConsumeInvDtls](#schemaval3cpconsumeinvdtls)|Array|

<a id="schemaval3cpconsumeinvdtls"></a>
### val3cpConsumeInvDtls

|Name|Type|Description|
|---|---|---|---|
|@ref{n}|string|"n" = sequential number for each geography.|
|consumeReqStageId|string|UUID generated from fuse. Internal use.|
|geography|string|Geography (ZIP or ATZ).|
|ihw|string|In Home Week - Date is returned in milliseconds(epoc).  |
|ihd|string|In Home Day of the week.  Date is returned in milliseconds(epoc).  |
|custAccountId|string|Conformation of the Oracle Account the Mediaplan was created with.|
|status|string|"SUCCESS" or "FAILURE"|
|consumedFlag|string|Booking Status of Geography - “Y”/"N"|
|errorFlag|string|“Y”/"N"|
|errorDescription|string|Description of Error.|
|createdBy|string|"null"|
|creationDate|string|"null"|
|lastUpdatedBy|string|"null"|
|lastUpdateDate|string|"null"|
|lastUpdateLogin|string|"null"|




---------------------------------------------------------------------------

# Order
## POST - Submit Product Order
Submit Valassis Product Order

### HTTPS Request
> Code samples

```shell
# You can also use wget
curl -X POST https://api.valassis.com/Order/1.0/submitOrder \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <access_token>'
```

```ruby
require 'rest-client'
require 'json'

headers = {
  'Content-Type' => 'application/json',
  'Accept' => 'application/json'
}

result = RestClient.post 'https://api.valassis.com/Order/1.0/submitOrder',
  params: {
  }, headers: headers

p JSON.parse(result)

```

```python
import requests
headers = {
  'Content-Type': 'application/json',
  'Accept': 'application/json'
}

r = requests.post('https://api.valassis.com/Order/1.0/submitOrder', params={

}, headers = headers)

print r.json()
```



`POST https://api.valassis.com/Order/1.0/submitOrder`


### Body Parameters
|Parameter|Type|Required|Description|
|---|---|---|---|---|
|body|[OrderRequest](#schemaorderrequest)|true|A JSON object containing Order information|

<aside class="notice">
One Order request can be used for multiple Mediaplans.
</aside>

<a id="schemaorderrequest"></a>
### Order Request Schema

*Order Request contains a combination of properties that will be static for all orders from a single source system, properties that will change for each order, and properties that will not apply to most situations.  Several properties will require specific values that will be provided to you by the Valassis implementation representative during onboarding activities*

```json
{
  "baseProductlist": [
    {
      "productType": [
        {
          "oracleAccountNumber": "9999999",
          "salesRepInitials": "AAA",
          "salesRepEmployeeId": 99999,
          "contractNumber": "17PLATINUM.3",
          "orderSource": "CAR",
          "distributionMethod": "Shared",
          "printMethod": "ADVO Printed",
          "draftType": "Client Supplied",
          "goldSeal": "No",
          "finishedSize": "Basic Data Postcard",
          "productType": "VDP",
          "mediaPlanGroupList": ["21822","21828"],
          "copyDisposition": "Kill",
          "orderSourceRef": "1234re32",
          "clientNameAppearingOnAd": "Client Business Name",
          "clientInvoicePromotion": "DV4208940",
          "clientPromotion": "Client Business Name",
          "advertisementDate": "12-15-2018",
          "uniqueAdVersionCode": "DV4208940",
          "storeCopys": [{
            "printAddressType": "Store Copy",
            "businessName": "Client Business Name",
            "contactName": "Client Contact Name",
            "streetAddress": "Business Address",
            "zipCode": "Business Zip",
            "quantity": 9999,
            "rate": 9999
              }],
          "profileName": null,
          "opportunityNumber": "999999",
          "preferredPosition": null,
          "formName": null,
          "creatorEmployeeId": 99999,
          "anneCrossSellNumber": null,
          "graphicsHubOverride": null,
          "contractAgreement": "TBD17PLATINUM.3",
          "clientPO": null,
          "clientName": "Client Business Name"
        }
      ],
      "productFamilyType": "SHAREDMAIL"
    }
  ]
}
```

### Properties
<aside class="notice">
Property values in <code>BOLD</code> should be used as default values
</aside>

|Name|Type|Required|Description|
|---|---|---|---|---|
|baseProductlist|[[baseProductlist](#schemabaseproductlist)]|false|Array of Order parameters|


<a id="schemabaseproductlist"></a>
### baseProductlist

|Name|Type|Required|Description|
|---|---|---|---|---|
|productType|[[productType](#schemaproducttype)]|true|Array of Order parameters|
|productFamilyType|string|true|**"SHAREDMAIL"**|


<a id="schemaproducttype"></a>
###productType

|Name|Type|Required|Description|
|---|---|---|---|---|
|oracleAccountNumber|string|true|Oracle billing Account number|
|salesRepInitials|string|true|Unique sales person's initials or virtual sales rep.|
|salesRepEmployeeId|number(double)|true|Valid SAP Employee ID of salesRep.|
|contractNumber|string|true|Oracle Contract Number|
|orderSource|string|false|Can be used to identify source system of Order Request|
|distributionMethod|string|true|**"Shared"** - Valassis Product Distribution Method.  Indicates how product will be delivered.|
|printMethod|string|true|**"ADVO Printed"** - Indicates if Valassis or Client will be printing artwork.|
|draftType|string|true|**"Client Suppied"** - Proof required for artwork.|
|goldSeal|string|true|**"No"** - Special pricing attributes from a contract.  Default to "No".|
|finishedSize|string|true|**"Basic Data Postcard"** - Standard Valassis Product Categories|
|productType|string|true|**"VDP"** - Standard Valassis Product Names|
|mediaPlanGroupList|number(double)|true|Array - List of all mediaPlanGroupId's from all Mediaplan calls for selected Geographies in Order.|
|copyDisposition|string|true|Indicates if Order is single delivery or multiple week delivery. Used when perpetual orders are sent at the same time. |
|orderSourceRef|string|true|Unique Order number from Requesting Application|
|clientNameAppearingOnAd|string|false|Name of the client ordering the Valassis product. 25 Chars Max.|
|clientInvoicePromotion|string|true|Same as uniqueAdVersionCode. 30 Chars Max.|
|clientPromotion|string|true|??? 30 Chars Max.|
|advertisementDate|string|true|Sunday date of the IN-Home week of the first delivery. (MM-DD-YYYY)|
|uniqueAdVersionCode|string|true|a unique value to Valassis.  Different value for each IHW|
|storeCopys|[[StoreCopys](#schemastoreCopys)]|false|Array of Store Copy Information|
|profileName|string|false|none|
|opportunityNumber|string|false|none|
|preferredPosition|string|false|Used only for Wrap Product|
|formName|string|false|none|
|creatorEmployeeId|number(double)|false|none|
|anneCrossSellNumber|string|false|none|
|graphicsHubOverride|string|false|none|
|contractAgreement|string|false|none|
|clientPO|string|false|20 Chars Max|
|clientName|string|false|none|

<a id="schemastoreCopys"></a>
###storeCopys

|Name|Type|Required|Description|
|---|---|---|---|---|
|printAddressType|string|false|Indicates if a store copy is to be sent to client.|
|businessName|string|false|Name of Business Client to receive Store Copy. 30 Chars Max|
|contactName|string|false|Name of Client contact|
|streetAddress|string|false|Address of where Story Copy is to be sent.|
|zipCode|string|false|Zip of where Story Copy is to be sent|
|quantity|string|false|Number of store copies to be sent|
|rate|string|false|CPM Rate to use|




> The above command returns JSON structured like this:

### Response
<aside class="warning">
Successful completion of this call means that the Order as only been submitted to Valassis.  Required back-office scheduled processes are executed to place the Order into a final submitted status.
</aside>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OrderRequest call successful|[OrderResponse](#schemaorderresponse)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request - See the OrderResponse object for further information|None|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Internal Server Error - The JSON file could not be processed due to errors which occurred on the Valassis server side. Please notify Valassis IT Support.|None|


<a id="schemaorderresponse"></a>
### Order Response Schema
```json
{
  "payload": "280698",
  "exception": {},
  "returnCode": 0
}
```

|Name|Type|Description|
|---|---|---|
|payload|string|UUID Reference Number|
|exception|object|"null" or reason of exception|
|returnCode|integer(int32)|Http Status Code - "null" if successful|

*Order Payload contains only a UUID reference number which can only be used to tracing the Order internally to Valassis.  This is not an Order conformation number.*
