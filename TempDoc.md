# TelyPay Payment API Documentation
## Introduction
Welcome to TelyPay's Payment API documentation. This guide is intended for developers who are looking to integrate TelyPay's payment gateway into their applications. TelyPay offers a straightforward, robust and secure method for processing transactions.

The API base URL is: https://payment.telypay.com

## Pre-requisites
Before you start integration, you need to have your API key and Merchant ID which is available once you register as a business at https://invoice.telypay.com. After registration, you can copy your API key and Merchant ID from the developer menu.


### 1. Payment Request (POST)
To initiate a payment request, details of API are below:

| URL | Type | Header | Body |
| --------------- | ----------------- | ----------------- | ----------------- |
| /payment/request | POST | - __ApiKey__ (registered business api key) <br> - __Content-Type__ (application/json) | - __merchantId__ (registered business merchant Id) <br> - __orderId__ (Unique order id) <br> - __orderDescription__ (Order Description) <br> - __amount__ (Order amount (OMR)) <br> - __callback__ (Your callback url where we will POST transaction information) |

#### Sample Request
```
curl --location --globoff 'BaseURL/payment/request' \
--header 'ApiKey: <your-api-key>' \
--header 'Content-Type: application/json' \
--data '{    
  "merchantId": 0000000000,
  "orderId":"your unique order ref",
  "orderDescription":"This is a description",
  "amount": 1.5,
  "callback": "https://yourcompany.com/callback"
}'
```

#### Sample Response
```
{
    "message": "Success",
    "result": 200,
    "body": {
        "tranRef": "TST2314101601117",
        "orderId": "your unique order ref",
        "orderDescription": "Ths is a description",
        "amount": "1.500",
        "callback": "https://google.com](https://yourcompany.com/callback",
        "redirectUrl": "https://secure-oman.paytabs.com/payment/wr/5E99A88F82E4116AC8D9E3762BBB199033920195EC0083435DAA8123",
        "trace": "PMNT0505.646A6D95.000036QQ"
    }
}
```

You need to redirect the user on __redirectUrl__ as received in response which is actualy the payment page, once redirected, we will __POST__ the transaction information (model as below) to your callback Url

#### Transaction Sample
```
{
    "TranRef": "TST2313501591268",
    "TranType": "Sale",
    "OrderId": "your unique order ref",
    "Description": "This is a description",
    "Currency": "OMR",
    "Amount": "1.5",
    "Trace": "PMNT0505.646A6D95.000036QQ",
    "Date": "2023-05-21T14:30:00Z",
    "PaymentStatus": "Authorised"
}
```

#### Error Response Model
```
{
    "message": "Duplicated Request",
    "result": 409,
    "body": null
}
```

### 2. Refund Request (POST)
To initiate a refund, use the following API call:

```
curl --location --globoff 'BaseURL/refund/request' \
--header 'ApiKey: <your-api-key>' \
--header 'Content-Type: application/json' \
--data '{
    "transRef":"TST2314101600392",
    "amount": 1
}'
```

We will create a new transaction with type "Refund"
and we will send back the new transaction model as well.

#### Successful Refund Response
```
{
    "message": "Success",
    "result": 200,
    "body": {
        "tranRef": "TST2314101600398",
        "tranType": "Refund",
        "orderId": "22435634534",
        "description": "This is a description",
        "currency": "OMR",
        "amount": "8.500",
        "trace": "PMNT0506.646A734C.000036D4",
        "date": "2023-05-21T19:38:52.4499764",
        "paymentStatus": "Authorised"
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

## Test Card
You can use this card to test the payment gateway

| Card Number | CVV | Month | Year |
| --------------- | --------------- | --------------- | --------------- |
| 4111 1111 1111 1111 | 123 | Any | Any |



Happy Coding :)
