# TelyPay Payment API Documentation
## Introduction
Welcome to TelyPay's Payment API documentation. This guide is intended for developers who are looking to integrate TelyPay's payment gateway into their applications. TelyPay offers a straightforward, robust and secure method for processing transactions.

The API base URL is: https://payment.telypay.com

## Pre-requisites
Before you start integration, you need to have your API key and Meerchant ID which is available once you register as a business at https://invoice.telypay.com. After registration, you can copy your API key and Meerchant ID from the developer menu.

## API Endpoints
Below are the endpoints you can utilize.
 
| Enpoint                   | URL | Request Type | Header Params | Body Params |
| ------------------------- | --------------- | ----------------- | ----------------- | ----------------- |
| Initiate a payment request | /payment/request | POST | ApiKey | merchantId (fixed merchant Id of your registered bussiness), 
orderId (Unique order id), 
orderDescription (Order Description), amount, callback (Your URL where you will get transaction information) |


### 1. Payment Request (POST)
To initiate a payment request, use the following API call:

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

#### Successful Response Model
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
After you redirect your user to the redirectUrl which is the payment page, we will POST the transaction model to your callback Url

#### Transaction Model

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
##### e.g.1
```
{
    "message": "Duplicated Request",
    "result": 409,
    "body": null
}
```
##### e.g.2
```
{
    "message": "Unauthorised",
    "result": 401,
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
