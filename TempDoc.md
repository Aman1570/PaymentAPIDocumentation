# TelyPay Payment API Documentation V1
## Introduction
Welcome to TelyPay's Payment API documentation. This guide is intended for developers who are looking to integrate TelyPay's payment gateway into their applications. TelyPay offers a straightforward, robust and secure method for processing transactions.

The API base URL is: https://payment.telypay.com

## Pre-requisites
Before you start integration, you need to have your API key and Merchant ID which is available once you register as a business at https://invoice.telypay.com. After registration, you can copy your API key and Merchant ID from the developer menu.

## EndPoints
We are providing different endpoints to communicate with our payment gateway.
|  | Endpoint | URL |
| --------------- | ----------------- | ----------------- |
| 1  | Payment Request | ''/v1/payment/request'' |
| 2  | Payment Refund | ''/v1/refund/request'' |
| 3  | Get Transaction by Id | ''/v1/transaction'' |
| 4  | Get Transsaction List | ''/v1/Transaction/list'' |

### 1. Payment Request API
To initiate a payment request, you need to call below endpoint. If request initiate successflly, in the response we will provide `redirectUrl` to which you need to redirect the user to proceed to transaction, where user will provide the card information. When user will complete the the transaction, we will POST you the respons of transaction on your provided `callback` url automatically.

##### URL
__`POST:`__ `/v1/payment/request`

##### Parameters

| Parameter | Type | Description | Sample Value |
| --------------- | ----------------- | ----------------- | ----------------- |
| 'ApiKey' | header | Registered business api key | F0E3200X-28Z0-1DG3-9002-K65270W8259A | 
| Content-Type | header | Content type | `application/json` |
| merchantId | body | Registered business Merchant Id | 999366903 |
| orderId | body | Unique order id | 2254857641 |
| orderDescription | body | Order description | - |
| amount | body | Order amount in OMR | 2.456 |
| callback | body | Callback url where we will POST transaction information | http://XYZCompany.com/callback |


#### Sample Request (curl)
```
curl --location --globoff '<<API base URL>>/payment/request' \
--header 'ApiKey: <your-api-key>' \
--header 'Content-Type: application/json' \
--data '{    
  "merchantId": XXXXXXXXXX,
  "orderId":"your unique order ref",
  "orderDescription":"This is a description",
  "amount": 1.5,
  "callback": "https://yourcompany.com/callback"
}'
```

#### Sample Request (C#)

If you are integrating in C#, You need to install __RestSharp__ nuget packege in you project.

```
string _body = @"{" 
+ @"    ""merchantId"": XXXXXXXXXX,
+ @"    ""orderId"": ""12345678"",
+ @"    ""orderDescription"": ""This is a description"",
+ @"    ""amount"": 2.55,
+ @"    ""callback"": ""https://yourcompany.com/callback""
+ @"}";

var options = new RestClientOptions("<<API base URL>>")
{
  MaxTimeout = -1,
};
var client = new RestClient(options);
var request = new RestRequest("/v1/payment/request", Method.Post);
request.AddHeader("ApiKey", "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
request.AddHeader("Content-Type", "application/json");
var body = _body;
request.AddStringBody(body, DataFormat.Json);
RestResponse response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```


#### Payment Request Response
```
{
    "message": "Success",
    "result": 200,
    "body": {
        "tranRef": "TST2314101601117",
        "orderId": "your unique order ref",
        "orderDescription": "Ths is a description",
        "amount": "1.500",
        "callback": "https://yourcompany.com/callback",
        "redirectUrl": "https://secure-oman.paytabs.com/payment/wr/5E99A88F82E4116AC8D9E3762BBB199033920195EC0083435DAA8123",
        "trace": "PMNT0505.646A6D95.000036QQ"
    }
}
```

Once you got the response, you need to redirect the user on `redirectUrl` as received in response, which is actualy the payment page, once redirected, we will __POST__ the transaction response (model as below) to your `callback` Url. Your call back url must be a POST url.

#### Transaction Response
```
{
    "BusinessId": "626B3C9E-298C-4E2D-A8A5-CE39542CD419"
    "TranRef": "TST2313501591268",
    "TranType": "Sale",
    "OrderId": "your unique order ref",
    "Description": "This is a description",
    "Currency": "OMR",
    "Amount": "1.5",
    "Callback": "https://yourcompany.com/callback"
    "Trace": "PMNT0505.646A6D95.000036QQ",
    "Date": "2023-05-21T14:30:00Z",
    "PaymentStatus": "A",
    "PaymentMessage": "Authorised",
    "PrevTranRef": null
}
```

All possible `PaymentStatus` are explaind at the end of document.


#### Error Response Model
```
{
    "message": "Duplicated Request",
    "result": 409,
    "body": null
}
```

### 2. Refund Request API
To initiate a refund API details are below. We will create a new transaction with type "Refund"
and with tha same model of transaction a response will be sent back.

| URL | Type | Header | Body |
| --------------- | ----------------- | ----------------- | ----------------- |
| /v1/refund/request | POST | - __ApiKey__ (registered business api key) <br> - __Content-Type__ (application/json) | - __merchantId__ (registered business merchant Id) <br> - __transRef__ (Unique transaction referance which need to refund) <br> - __amount__ (Amount that need to refund in OMR) |

#### Sample Request (curl)
```
curl --location --globoff '<<API base URL>>/refund/request' \
--header 'ApiKey: <your-api-key>' \
--header 'Content-Type: application/json' \
--data '{
    "merchantId": XXXXXXXXXX,
    "transRef":"TST2313501591268",
    "amount": 1
}'
```

#### Sample Request (C#)

If you are integrating in C#, You need to install __RestSharp__ nuget packege in you project.
```
string _body = @"{" 
+ @"    ""merchantId"": XXXXXXXXXX,
+ @"    ""transRef"": ""TST2314101600392"",
+ @"    ""amount"": 2.55
+ @"}";

var options = new RestClientOptions("<<API base URL>>")
{
  MaxTimeout = -1,
};
var client = new RestClient(options);
var request = new RestRequest("/v1/Refund/request", Method.Post);
request.AddHeader("ApiKey", "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
request.AddHeader("Content-Type", "application/json");
var body = _body
request.AddStringBody(body, DataFormat.Json);
RestResponse response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

#### Successful Refund Response
```
{
    "message": "Success",
    "result": 200,
    "body": {
        "BusinessId": "626B3C9E-298C-4E2D-A8A5-CE39542CD419"
        "TranRef": "TST2314101600398",
        "TranType": "Refund",
        "OrderId": "22435634534",
        "Description": "This is a description",
        "Currency": "OMR",
        "Amount": "1.5",
        "Callback": "https://yourcompany.com/callback"
        "Trace": "PMNT0506.646A734C.000036D4",
        "Date": "2023-05-21T16:45:00Z",
        "PaymentStatus": "A",
        "PaymentMessage": "Authorised",
        "PrevTranRef": "TST2313501591268"
    }
}
```

#### Error Response Model
```
{
    "message": "Unable to process your request",
    "result": 422,
    "body": null
}
```

## Payment Staus
 Below are the possbile statuses for any transaction i.e., `Request`, `Refund`

| PaymentStatus | Description |
| --------------| -------------- |
| A | Authorised |
| H | Hold (Authorised but on hold for further anti-fraud review) |
| P | Pending (for refunds) |
| V | Voided |
| E | Error |
| D | Declined |
| X | Expired |

## Test Card
We are providing a test VISA Card as below to test the integration with TelyPay payment gateway.

| Card Number | CVV | Month | Year |
| --------------- | --------------- | --------------- | --------------- |
| 4111 1111 1111 1111 | 123 | Any | Any |



Happy Coding :)
