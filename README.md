# Introduction
This document describes the quickclaim API.
Audience of this document is software developers. To access the APIs you need to have an account with us at https://app.quickclaim.io/

quickclaim is an NDIS payment solution to help Disability Providers automate their claiming and budget management process. For more information visit us at https://quickclaim.io/


## quickclaim products
Currently we are offering three apps for NDIS Providers and Participants:
* **quickclaim online**: which is an online application for the Providers to manage their ndis budget and payments. This is our enterprise solution that connects to majority of CRM and Finance systems to automate the ndis claiming process.

* **qc.pay**:  is a Point-Of-Sales terminal connected to our payment gateway and quickclaim online app. This is a mobile application using QR Payment technology. As a Provider you can process ndis claims instantly within this app. Also you can check ndis plans and service bookings of Participants by scanning their quickclaim card. More information at: https://quickclaim.io/qc-pay/


* **qc.card**: is a mobile app for Participants. As a Participant you can secure your ndis information behind a digital signature in a QR Code. Then when you interact with your Providers and Planner you don't need to share your personal information. It also comes with a nice plastic card with your QR signature. You can use your card to pay for the service using your ndis fund (Currently only at registered providers and for agency and plan-managed budgets). There are many other features in the app like checking your plan and statements, giving (or revoking) consent to Providers to access your data or automatically process your invoices, or to communicate both ways with your Providers via notifications. More information at: https://quickclaim.io/qc-card/

# APIs
## What APIs are available
All of our systems are connected and managed from our central AWS infrastructure. Most of functionalities in each of our products are accessible via API. We have API for creating and managing:

* Participants
* Plans and ServiceBookings
* NDIS Claims
* Invoices (Plan/Self managed or private)
* QR codes
* NDIS pricing

## How to use the API
First thing you need is the base url. Add this base you url at beginning of all the endpoints.
The base url is:
```bash
https://api.quickclaim.io/public/
```

Second thing you need is your credentials. All APIs have the same authentication method which is a pair of org-id and x-api-key. You should get your credentials within our app at https://app.quickclaim.io/.
Contact support@quickclaim.io if you need help. 

Please note that your credentials are global and the same across all the products.

You are going to add the org-id and x-api-key to the headers of your calls.
### Header
Header is always the same with the below fields:

```bash
org-id: YOUR_ORG_ID
x-api-key: YOUR_X_API-KEY
```

## Participant APIs
### GET /participant
This function gets list of all participants you have registered in quickclaim app in your organisation.
Request sample:
``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/participant',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        },
};

```
Response body sample:
``` bash
200- {
  "version": "2.0.4",
  "data": [
    {
            "participantId": 238,
            "orgId": 19,
            "firstName": "Ross",
            "lastName": "Geller",
            "clientNumber": "12345",
            "ndisNumber": "430000001",
            "createDate": "2021-10-12T04:07:14.000Z",
            "modifyDate": "2021-10-12T04:07:14.000Z",
            "isValidated": 0,
            "isDeleted": 0
        },
        {
            "participantId": 1,
            "orgId": 19,
            "firstName": "Ricky",
            "lastName": "Martin",
            "clientNumber": "1235",
            "ndisNumber": "430000001",
            "createDate": "2021-02-09T05:51:47.000Z",
            "modifyDate": "2021-02-09T05:51:47.000Z",
            "isValidated": 0,
            "isDeleted": 0
        },
        .
        .
  ]
}
400-{"version": "2.0.4", "message":"Read from Database was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### POST /participant
This function adds a list of participants to the participant table and returns the last participantId.
Request sample:
``` bash
var options = { 
    method: 'POST',
    url: 'https://api.quickclaim.io/public/participant',
    headers: 
        headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        },
    body:  
        {"participants":[
            {
            "firstName": "Michael",
            "lastName":"Jackson",
            "dob": "1985-01-01",
            "clientNumber" :  "123456",
            "ndisNumber": "430000001"
            },
            {
            "firstName": "Chris",
            "lastName":"Ronaldo",
            "dob": "2020-01-01",
            "clientNumber" :  "456",
            "ndisNumber": "430000002"
            }
    ]} 
};

```
Response body sample:
``` bash
201- {  "version": "2.0.3","message": "The provided participant list has been accepted."}
400-{   "version": "2.0.3","message":"Participant info should be provided in body."}
400-{   "version": "2.0.3","message":"Participant info cannot all be null."}
400-{   "version": "2.0.3","message":"Something went wrong, if the issue persists contact support."}
```

### PUT /participant
This function updates some fields of a participant in the participant table. It uses participant ID as reference to update a participant. 
Request sample:
``` bash
var options = { 
    method: 'PUT',
    url: 'https://api.quickclaim.io/public/participant',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        },
    body: 
        {"participantId": 10
        "firstName": "Michael",
        "lastName":"Jackson",
        "dob": "1970-01-01",
        "clientNumber" :  "1234567",
        "ndisNumber": 430000001
        }
      
};

```
Response body sample: 
``` bash
200 - {"version": "2.0.3","message": "The participant is updated."}
422 - {"version": "2.0.3","message": "ndisNumber should be 9 digits starting with 43XXXXXXX."}
400 - {"version": "2.0.3","message": "update Database was not successful"}
422 - {"version": "2.0.3","message": "The participant has been validated and lastName, dob and ndisNumber cannot be changed."}
404 - {"version": "2.0.3","message":"The participant ID does not exist in the Database"}
400 - {"version": "2.0.3","message":"ParticipantId should be provided in the body."}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```

## Plans and ServiceBookings APIs
### GET /planByNdisNumber/{ndisNumber}
### GET /planByQr/{cardNumber}
### GET /planByParticipantId/{participantId}
This endpoint returns ndis plans of the given participantId.
Request sample:
``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/planByParticipantId/12',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response samples :
``` bash
200 - {     "version": "2.0.3",
        "data": [
            {
                "planId": 2,
                "participantId": 79,
                "orgId": 19,
                "planNumber": 1234567,
                "startDate": "2020-08-22",
                "endDate": "2021-08-23",
                "ndisRefreshDate": "2021-02-25T23:51:27.000Z",
                "rego": 4300000001
            }
        ]   
    }
    
403 - {"version": "2.0.3","message":"participantId should be provided in the path /getParticipantPlan/{id}"}
422 - {"version": "2.0.3","message":"No rego is registered for the provided orgId or rego is not ready to be used."}
404 - {"version": "2.0.3","message":"There are not all required information for the participant."}
400 - {"version": "2.0.3","message":"There is no access token from NDIS"}
400 - {"version": "2.0.3","message":"NDIS call to get participant plan failed"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### GET /refreshAServiceBooking/{serviceBookingId}
This endpoint calls NDIS and gets the details information of the given serviceBookingId (the ID in our database). 
Request sample:

``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/refreshAServiceBooking/26',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response samples :
``` bash
200 - {
    "version": "2.0.3",
    "data": {
        "serviceBookingId": 26,
        "serviceBookingNumber": 12345678,
        "orgId": 19,
        "rego": 4300000001,
        "participantId": 79,
        "participantName": "Michael Jackson",
        "ndisNumber": 430000001,
        "startDate": "2021-09-11",
        "endDate": "2021-09-15",
        "supportCategory": "Assistance with social and community participation",
        "supportCategoryApi": "SOCIAL_COMMUNITY_CIVIC",
        "supportItem": "",
        "supportItemName": "",
        "unit": 1,
        "rate": "20.00",
        "amount": 20,
        "remaining": 20,
        "ndisRefreshDate": "2021-02-25T23:54:38.172Z",
        "createDate": "2021-02-24T23:27:49.000Z"
    }
}
403 - {"version": "2.0.3","message":"serviceBookingId should be provided in the path"}
404 - {"version": "2.0.3","message":"There is not any information for the provided serviceBookingId and org-id"}
404 - {"version": "2.0.3","message":"There is not any information for the provided ndis-rego and org-id"}
400 - {"version": "2.0.3","message":"There is no access token from NDIS"}
400 - {"version": "2.0.3","message":"NDIS call to get Service Booking failed"}
400 - {"version": "2.0.3","message":"QuickClaim Database write was not successful"}
422 - {"version": "2.0.3","message":"There is not any information for this Service Booking"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### GET /serviceBookingByNdisNumber/{ndisNumber}
### GET /serviceBookingByQr/{cardNumber}
### GET /refreshParticipantServiceBooking/{participantId}
This endpoint calls NDIS and gets all ServiceBookings of the given participantId.
Request sample:

``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/refreshParticipantServiceBooking/80',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response samples :
``` bash
200 - {
    "version": "2.0.3",
    "data": [
        {
            "serviceBookingId": 3,
            "serviceBookingNumber": 12345678,
            "orgId": 19,
            "rego": 4300000001,
            "participantId": 79,
            "participantName": "Michael Jackson",
            "ndisNumber": 430000001,
            "startDate": "2021-03-21",
            "endDate": "2021-03-25",
            "supportCategory": "Assistance with social and community participation",
            "supportCategoryApi": "SOCIAL_COMMUNITY_CIVIC",
            "supportItem": "04_153_0104_6_1",
            "supportItemName": "Group Based Activities In The Community - Ratio 1:5 - Complex - Saturday",
            "unit": 4,
            "rate": "5.00",
            "amount": 20,
            "remaining": 20,
            "ndisRefreshDate": "2021-02-26 00:03:39.42",
            "createDate": "2021-02-24 00:51:31.00"
        },
        .
        .
        .
    }
403 - {"version": "2.0.3","message":"participantId should be provided in the path"}
404 - {"version": "2.0.3","message":"There is not any information for the provided participantId and org-id"}
404 - {"version": "2.0.3","message":"There is not any rego for the provided participantId and org-id"}
400 - {"version": "2.0.3","message":"There is no access token from NDIS"}
400 - {"version": "2.0.3","message":"NDIS call to get Service Booking failed"}
400 - {"version": "2.0.3","message":"QuickClaim Database write was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### GET /serviceBooking
This endpoint returns all ServiceBookings from the qc database and NOT from NDIS(for all participants).
You can use the below optional parameters in this endpoint to filter the response:
* serviceBookingNumber
* participantName
* ndisNumber
* supportItem
* supportCategory

NOTE: if more than one parameter used, it returns the AND result.
``` bash
/getServiceBooking?ndisNumber=302&supportItem=01_101 
returns the ServiceBookings of ndisNumber like %302% and supportItem like %01_101%

```
Request sample:

``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/serviceBooking',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "data": [
        {
            "serviceBookingId": 5,
            "serviceBookingNumber": 1234567,
            "orgId": 19,
            "rego": 4300000001,
            "participantId": 79,
            "participantName": "Michael Jackson",
            "ndisNumber": 430000001,
            "startDate": "2021-03-11",
            "endDate": "2021-03-15",
            "supportCategory": "Assistance with social and community participation",
            "supportCategoryApi": "SOCIAL_COMMUNITY_CIVIC",
            "supportItem": "04_153_0104_6_1",
            "supportName": "Group Based Activities In The Community - Ratio 1:5 - Complex - Saturday",
            "unit": 4,
            "rate": null,
            "amount": 20,
            "remaining": 20,
            "ndisRefreshDate": "2021-02-26 00:03:41.00",
            "createDate": "2021-02-24 00:51:31.00",
            "modifyBy": null,
            "note": null,
            "isCancelled": 0
        },
        .
        .
        .
    ]
}
400 - {"version": "2.0.3","message":"Query to Database was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### GET /serviceBooking/{id}
This endpoint returns ServiceBooking data of the given serviceBookingId from the qc database (won't refresh data from NDIS).

``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/serviceBooking/12',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "data": {
        "serviceBookingId": "12",
        "participantId": 123,
        "orgId": "1",
        "startDate": "2020-02-07T08:13:24.595Z",
        "endDate": "2021-02-07T08:13:24.595Z",
        "name": "Monica Geller",
        "ndisNumber": 430000001,
        "supportCategory": "ACCOMMODATION",
        "supportItem": "01_795_0104_1_1",
        "unit": 2.5,
        "rate": 57.56,
        "total": 143.9,
        "remaining": 2200.2,
        "ndisRefreshDate": "2021-02-09T10:53:24.023Z",
        "createDate": "2021-02-09T10:53:24.023Z",
        "modifyDate": "2021-02-09T10:53:24.023Z",
        "modifyBy": "2",
        "isDeleted": 0
    }
}
400 - {"version": "2.0.3","message":"Query to Database was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### POST /serviceBooking
This endpoint creates a ServiceBooking on NDIS. It gets all required information for creating a ServiceBooking in the body as shown below.

``` bash
var options = { 
    method: 'POST',
    url: 'https://api.quickclaim.io/public/createServiceBooking',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
    body:
        { 
            "participantId" : 79,
            "planId":  12345,
            "startDate": "2021-09-21",
            "endDate": "2021-09-25",    
            "categoryCode": "04",
            "supportItem": "04_153_0104_6_1",
            "rate": 7.00,
            "quantity": 8.00,
            "note": "This is a new note."
        }
};

```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "data": {
        "serviceBookingNumber": "1234567"
    }
}
400 - {"version": "2.0.3","message":"Service info should be provided in body."}
422 - {"version": "2.0.3","message":"Category Code is invalid"}
404 - {"version": "2.0.3","message":"There is not any information for the provided planId, participantId and org-id in plan table."}
404 - {"version": "2.0.3","message":"There is not any information for the provided participantId and org-id"}
404 - {"version": "2.0.3","message":"There is not any information for the provided ndis-rego and org-id"}
400 - {"version": "2.0.3","message":"There is no access token from NDIS."}
422 - {"version": "2.0.3","Create Service Booking failed, please double check the entry parameters."}
400 - {"version": "2.0.3","QuickClaim Database write was not successful"}

```
### POST /serviceBookingForNdisNumber
This endpoint creates a ServiceBooking on NDIS. It gets all required information for creating a ServiceBooking in the body as shown below.

``` bash
var options = { 
    method: 'POST',
    url: 'https://api.quickclaim.io/public/serviceBookingForNdisNumber',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
    body:
        { 
            "ndisNumber" : 430000001,
            "ndisRego": 450000001,
            "startDate": "2021-11-06",
            "endDate": "2021-11-08",    
            "categoryCode": "04",
            "supportItem": "04_153_0104_6_1",
            "rate": 5.00,
            "quantity": 6.00,
            "note": "This is a new note"
        }
};

```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "data": {
        "serviceBookingNumber": "1234567"
    }
}
400 - {"version": "2.0.3","message":"Service info should be provided in body."}
422 - {"version": "2.0.3","message":"Category Code is invalid"}
404 - {"version": "2.0.3","message":"There is not any information for the provided planId, participantId and org-id in plan table."}
404 - {"version": "2.0.3","message":"There is not any information for the provided participantId and org-id"}
404 - {"version": "2.0.3","message":"There is not any information for the provided ndis-rego and org-id"}
400 - {"version": "2.0.3","message":"There is no access token from NDIS."}
422 - {"version": "2.0.3","Create Service Booking failed, please double check the entry parameters."}
400 - {"version": "2.0.3","QuickClaim Database write was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### PUT /updateServiceBooking
This endpoint updates a ServiceBooking allocation. It gets serviceBookingId, rate and quantity(unit) in the body. Then it updates the ServiceBooking on NDIS.

``` bash
var options = { 
    method: 'PUT',
    url: 'https://api.quickclaim.io/public/updateServiceBooking',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
    body:
        {     
            "serviceBookingId": 34,
            "rate": 9,
            "quantity": 10
        }   
};
```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "message": "Update was successful"
}
400 - {"version": "2.0.3","message":"You should provide serviceBookingId, rate and quantity in the body."}
404 - {"version": "2.0.3","message":"There is not any information for the provided serviceBookingId and org-id"}
422 - {"version": "2.0.3","message":"Only service booking that are defined at category level can be updated"}
404 - {"version": "2.0.3","message":"There is not any information for the provided ndis-rego and org-id"}
400 - {"version": "2.0.3","There is no access token from NDIS"}
400 - {"version": "2.0.3","NDIS call to update Service Booking failed"}
400 - {"version": "2.0.3","API call to refresh Service Booking failed"}
400 - {"version": "2.0.3","QuickClaim Database write was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### PUT /extendServiceBooking   
This endpoint extends the endDate of a ServiceBooking. It gets serviceBookingId and the new endDate in the body to update in NDIS.

``` bash
var options = { 
    method: 'PUT',
    url: 'https://api.quickclaim.io/public/extendServiceBooking',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
    body:
        {     
            "serviceBookingId": 43
            "endDate": "2021-10-18"
        }   
};
```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "message": "End date has been updated successfully"
}
400 - {"version": "2.0.3","message":"You should provide serviceBookingId and new endDate in the body."}
404 - {"version": "2.0.3","message":"There is not any information for the provided serviceBookingId and org-id"}
404 - {"version": "2.0.3","message":"There is not any information for the provided ndis-rego and org-id"}
400 - {"version": "2.0.3","There is no access token from NDIS"}
400 - {"version": "2.0.3","NDIS call to update Service Booking failed"}
422 - {"version": "2.0.3","endDate should be later than current endDate"}
400 - {"version": "2.0.3","QuickClaim Database write was not successful"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### DELETE /cancelServiceBooking
This endpoint is to cancel/delete a ServiceBooking and release the unused funds. It gets serviceBookingId as path parameter. 
If the ServiceBooking has never been used, it gets DELETED. All the remaining funds will be released.
If it has been used before and the endDate is in future, it gets CANCELLED. All the remaining funds will be released. Then the endDate is changed to today's date. The CANCELLED ServiceBookings are NOT editable anymore.

``` bash
var options = { 
    method: 'DELETE',
    url: 'https://api.quickclaim.io/public/cancelServiceBooking/44',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};
```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    "message": "The serviceBooking is cancelled and the fund is released successfully."
}
200 - {
    "version": "2.0.3",
    "message": "The serviceBooking is deleted successfully."
}
404 - {"version": "2.0.3","message":"There is not any information for the provided serviceBookingId and org-id."}
422 - {"version": "2.0.3","message":"The serviceBooking has been deleted before."}
404 - {"version": "2.0.3","message":"There is not any information for the provided ndis-rego and org-id"}
400 - {"version": "2.0.3","There is no access token from NDIS"}
400 - {"version": "2.0.3","NDIS call to delete Service Booking failed"}
400 - {"version": "2.0.3","QuickClaim Database write was not successful"}
422 - {"version": "2.0.3","The serviceBooking endDate is in the past and cannot be cancelled."}
422 - {"version": "2.0.3","The serviceBooking is at the category level and cannot be cancelled."}
400 - {"version": "2.0.3","NDIS call to cancel Service Booking failed."}
400 - {"version": "2.0.3","Update in Database was not Successful."}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```

## NDIS Claims

::: NOTE

*NDIS Payment 3.0 released with some structural changes. We are reviewing the Payments APIs currently and some of the API definitions aren't available!!!*
### GET /transaction
This a shared endpoint between Claims/Invoices. It returns all transactions recorded in quickclaim.

*[NEW json schema TBA]*

### GET /participantClaim/{ndisNumber}
This endpoint returns latest 100 claims for the given Participant.
Because of NDIS limitations it doesn't have the detail information of a claim. You can get the detail info of a claim by calling the **get /claimResult/{claimNumber}** API.

Request sample:
``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/participantClaims/430000001',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response samples :
``` bash
200 - {
    "version": "2.0.4",
    "totalRecords": 100,
    "data": [
        {
            "claimNumber": 12345,
            "claimReference": "123456",
            "ndisNumber": 430000001,
            "participantName": "Monica Geller",
            "claimStatus": "0",
            "ndisInvoiceNumber": "",
            "supportCategory": "SOCIAL_COMMUNITY_CIVIC",
            "submitDate": "2021-04-27",
            "startDate": "2021-04-23",
            "endDate": "2021-04-23",
            "serviceBookingNumber": 1234567,
            "providerAbn": 12345678910,
            "exemptionReason": ""
        },
        {
            "claimNumber": 123456,
            "claimReference": "123456",
            "ndisNumber": 430000001,
            "participantName": "Monica Geller",
            "claimStatus": "0",
            "ndisInvoiceNumber": "",
            "supportCategory": "SOCIAL_COMMUNITY_CIVIC",
            "submitDate": "2021-04-27",
            "startDate": "2021-04-23",
            "endDate": "2021-04-23",
            "serviceBookingNumber": 12345678,
            "providerAbn": 12345678910,
            "exemptionReason": ""
        }
    ]
}
```

### GET /claimResult/{claimNumber}
This endpoint returns all response fields of the given claimNumber.

*[NEW json schema TBA]*

### POST /claim
This endpoint sends a single Payment Request to NDIS. NDIS won't return the result immediately and to get the result you need to use **GET /claimResult** endpoint.
You need to know the serviceBookingNumber to make this call. If you don't you can use **POST /batchClaim**.

*[NEW json schema TBA]*

### PUT /cancelClaim
This endpoint cancels a successful payment on NDIS portal.

*[NEW json schema TBA]*

### POST /batchClaim
This endpoint sends one or multiple Payment Requests to NDIS. NDIS won't return the result immediately and to get the result you need to use **GET /batchResult** endpoint.

*[NEW json schema TBA]*

### GET /batchResult/{batchNumber}
This endpoint returns the result of a batchClaim.

*[NEW json schema TBA]*

## Invoices (Plan/Self managed or private)
Invoice APIs are part of the Enterprise licensing and available to Enterprise users on the app Wiki page. 
Based on the implemented **Workflows** and **Action Plans** for Invoicing different APIs are to be used.
The general API to get all the Invoice transactions is as below. 
### GET /transaction
Definition above under NDIS Claims section.

## QR codes
To make NDIS interactions faster, more secure and manageable we have adopted QR Payment technology and implemented it on top the NDIS APIs. These APIs are using QR code in their call. QR Codes are either a **cardNumber** or a **token**. Below is the API set to issue and scan QR codes.
### GET /plan/{cardNumber}
This endpoint returns the Participant's NDIS Plan data based on the given card number. Personal information of the Participant are stored in **qc.card** database and is not exposed to the caller of this API. If the Participant has given consent to you within the **qc.card** app you can get the Plan data using this call.

Request sample:
``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/planByQr/1234567891234567',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response samples :
``` bash
200 - {     "version": "2.0.3",
        data: {
            participant:{
              ndisNumber: 430000001,
              participantName: 'Michael Jordan'
            },
            plan: [
                {
                    "planNumber": 1234567,
                    "startDate": "2019-08-22",
                    "endDate": "2020-08-22"
                },
                {
                    "planNumber": 12345678,
                    "startDate": "2020-08-23",
                    "endDate": "2021-08-23"
                }
            ]    
        }  
    }
    
403 - {"version": "2.0.3","message":"cardNumber should be provided in the path /plan/{cardNumber}"}
422 - {"version": "2.0.3","message":"No rego is registered for the provided orgId or rego is not ready to be used."}
404 - {"version": "2.0.3","message":"There are not all required information for the participant."}
400 - {"version": "2.0.3","message":"There is no access token from NDIS"}
400 - {"version": "2.0.3","message":"NDIS call to get participant plan failed"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```
### GET /serviceBooking/{cardNumber}
This endpoint returns all ServiceBookings of the given card number.

Request sample:

``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/serviceBooking',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }
};

```
Response body sample:
``` bash
200 - {
    "version": "2.0.3",
    data: {
            participant:{
              ndisNumber: 430000001,
              participantName: 'Michael Jordan'
            },
            serviceBookings:[
            {"serviceBookingNumber": 1234567,
            "startDate": "2021-09-11",
            "endDate": "2021-09-15",
            "status": "APPR",
            "virtualStatus": "Inactive",
            "supportCategory": "04-Assistance with social and community participation",
            "supportCategoryApi": "SOCIAL_COMMUNITY_CIVIC",
            "supportItem": "",
            "supportItemName": "",
            "unit": 1,
            "rate": 20,
            "amount": 20,
            "remaining": 20,
            "daysLeft": 97,
            "note":""
            },
            {"serviceBookingNumber": 12345678,
            "startDate": "2021-06-11",
            "endDate": "2021-08-15",
            "status": "APPR",
            "virtualStatus": "Inactive",
            "supportCategory": "Assistance with social and community participation",
            "supportCategoryApi": "SOCIAL_COMMUNITY_CIVIC",
            "supportItem": "04_153_0104_6_1",
            "supportItemName": "Group Based Activities In The Community - Ratio 1:5 - Complex - Saturday",
            "unit": 1,
            "rate": 20.1,
            "amount": 20.1,
            "remaining": 15.7,
            "daysLeft": 35,
            "note":""}
          ]
        }
}
403 - {"version": "2.0.3","message":"cardNumber should be provided in the path /serviceBooking/{cardNumber}"}
422 - {"version": "2.0.3","message":"No rego is registered for the provided orgId or rego is not ready to be used."}
404 - {"version": "2.0.3","message":"There are not all required information for the participant."}
400 - {"version": "2.0.3","message":"There is no access token from NDIS"}
400 - {"version": "2.0.3","message":"NDIS call to get service booking failed"}
500 - {"version": "2.0.3","message":"API name or method is not valid."}
```

### GET /participantClaim/{cardNumber}
This endpoint returns latest 100 claims for the given Participant.
Because of NDIS limitations it doesn't have the detail information of a claim. You can get the detail info of a claim by calling the **get /claimResult/{claimNumber}** API.

*[NEW json schema TBA]*

## NDIS pricing
### GET /ndisCatalogue
This endpoint returns all the products in the NDIS catalogue. No header and authentication is required for this endpoint.
``` bash
var options = { 
    method: 'GET',
    url: 'https://api.quickclaim.io/public/ndisCatalogue',
    headers: 
        { 
          org-id: 19,
          x-api-key: "YOUR TOKEN"
        }    
};

```
Response samples :
``` bash
200 - {
    "version": "2.0.3",
    "data": [
        {
            "id": 1,
            "supportItem": "01_002_0107_1_1",
            "supportName": "Assistance With Self-Care Activities - Standard - Weekday Night",
            "categoryCode": "01",
            "supportCategory": "Assistance with daily life",
            "supportCategoryApi": "DAILY_ACTIVITIES",
            "registrationGroupNumber": "107",
            "registrationGroupName": "Daily Personal Activities",
            "unit": "H",
            "quote": "N",
            "rate_NT_SA_TAS_WA": null,
            "rate_ACT_NSW_QLD_VIC": null,
            "rate_National_Non_Remote": 62.17,
            "rate_National_Remote": 87.04,
            "rate_National_Very_Remote": 93.26,
            "nonFaceToFace": "Y",
            "providerTravel": "Y",
            "shortNoticeCancellations": "Y",
            "ndiaRequestedReports": "N",
            "irregularSilSupports": "N",
            "createDate": 1614057405,
            "modifyDate": 1614057405,
            "isDeleted": 0
        
        },
        {
            "id": 2,
            "supportItem": "01_002_0107_1_1_T",
            "supportName": "Assistance With Self-Care Activities - Standard - Weekday Night - TTP",
            "categoryCode": "01",
            "supportCategory": "Assistance with daily life",
            "supportCategoryApi": "DAILY_ACTIVITIES",
            "registrationGroupNumber": "107",
            "registrationGroupName": "Daily Personal Activities",
            "unit": "H",
            "quote": "N",
            "rate_NT_SA_TAS_WA": null,
            "rate_ACT_NSW_QLD_VIC": null,
            "rate_National_Non_Remote": 65.9,
            "rate_National_Remote": 92.26,
            "rate_National_Very_Remote": 98.85,
            "nonFaceToFace": "Y",
            "providerTravel": "Y",
            "shortNoticeCancellations": "Y",
            "ndiaRequestedReports": "N",
            "irregularSilSupports": "N",
            "createDate": 1614057405,
            "modifyDate": 1614057405,
            "isDeleted": 0
        
        },
        {
            "id": 3,
            "supportItem": "01_003_0107_1_1",
            "supportName": "Assistance From Live-In Carer",
            "categoryCode": "01",
            "supportCategory": "Assistance with daily life",
            "supportCategoryApi": "DAILY_ACTIVITIES",
            "registrationGroupNumber": "107",
            "registrationGroupName": "Daily Personal Activities",
            "unit": "H",
            "quote": "Y",
            "rate_NT_SA_TAS_WA": null,
            "rate_ACT_NSW_QLD_VIC": null,
            "rate_National_Non_Remote": null,
            "rate_National_Remote": null,
            "rate_National_Very_Remote": null,
            "nonFaceToFace": "N",
            "providerTravel": "N",
            "shortNoticeCancellations": "N",
            "ndiaRequestedReports": "N",
            "irregularSilSupports": "N",
            "createDate": 1614057405,
            "modifyDate": 1614057405,
            "isDeleted": 0
        },
        .
        .
        .
    ]
}
400 - {"version": "2.0.3","message":"Database query to get NDIS product was not successful"}
```
