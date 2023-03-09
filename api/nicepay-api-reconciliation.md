# Reconciliation


## Transaction Search API <img src="https://img.shields.io/badge/-Beta version-red">

You can use the Transaction Search API to retrieve and cross-check transaction records for the desired period.

<br>

### Transaction Search API Example code

```bash
curl --request GET 'https://api.nicepay.co.kr/v1/transactions?date=20230228&limit=10' \
--header 'Authorization: Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=' \
--header 'Content-Type: application/json'
```

<br>

```bash
//Reponse

{
    "resultCode": "0000",
    "resultMsg": "Success",
    "pagination": {
        "count": 26,
        "hasMore": true,
        "nextSequence": 11
    },
    "transactions": [
        {
            "sequence": 1,
            "originalTid": "yeoshin01m01012302280815095301",
            "tid": "yeoshin01m01012302280815095301",
            "orderId": "1233321132323323",
            "currency": "KRW",
            "amount": 1004,
            "method": "card",
            "useEscrow": false,
            "transactionStatus": "cancelled",
            "transactionAt": "2023-02-28T00:00:00"
        },
        {
            "sequence": 2,
            "originalTid": "yeoshin01m01012302280834436052",
            "tid": "yeoshin01m01012302280834436052",
            "orderId": "12174575685",
            "currency": "KRW",
            "amount": 8377,
            "method": "card",
            "useEscrow": false,
            "transactionStatus": "cancelled",
            "transactionAt": "2023-02-28T00:00:00"
        },
        {
            "sequence": 3,
            "originalTid": "yeoshin02m01162302280839111365",
            "tid": "yeoshin02m01162302280839111365",
            "orderId": "furyOrderId311655",
            "currency": "KRW",
            "amount": 1004,
            "method": "card",
            "useEscrow": false,
            "transactionStatus": "cancelled",
            "transactionAt": "2023-02-28T00:00:00"
        }

        ...

    ]
}

```

<br>

> #### ⚠️ Important  
> In the Beta version, the search period is fixed, and there may be changes in parameters and response values.  

<br>

### Transaction Search API Request parameter

```bash
GET /v1/transactions  
HTTP/1.1  
Host: api.nicepay.co.kr //Host will be changed after beta
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

<br>

| Parameter        | Type    | Required | Bytes | Description                                                                      |
|:-----------------|:-------:|:--------:|:-----:|:---------------------------------------------------------------------------------|
| date             | String  | O        | 19    | Start date for transaction search (yyyyMMdd)  <br> ex) 20230228 |
| startingSequence | String  | -        |       | Starting point of the sequence. <br> The sequence is created based on the startDate.    |
| limit            | Integer | -        | -     | default 100 <br> max 500   |


### Transaction Search API Response parameter <img src="https://img.shields.io/badge/-Object-yellow"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter    |               | Type    | Required | Bytes | Description                                                                          |
|:-------------|:--------------|:-------:|:--------:|:-----:|:-------------------------------------------------------------------------------------|
| resultCode   |               | String  | O        | 4     | 0000 : success / other failure  |
| resultMsg    |               | String  | O        | 100   | Result message  |
| pagination   |               | Object  |          |       |                 |
|              | count         | Integer | O        |       | The number of transactions objects responded |
|              | nextSequence  | Integer | O        |       | Next sequence  |
|              | hasMore       | Boolean | O        |       | true:There is data on the next page <br> false:This is last page|
| transactions |               | Array   | O        |       |                                                                                      |
|              | sequence      | Integer | O        |       | The index of the retrieved transaction  |
|              | originlTid    | String  | O        | 30    | The original transaction key that succeeded in the initial approval. |
|              | tid           | String  | O        | 30    | NICEPAY transaction ID    |
|              | orderId       | String  | O        | 64    | Unique order number   |
|              | currency      | String  | O        | 3     | Approved currency<br>KRW: Korean Won, USD: USD, CNY: Yuan  |
|              | amount        | Integer | O        | 12    | payment amount    |
|              | method        | String  | O        | 10    | Payment method<br><br>card: credit card, <br>vbank: virtual account, <br>bank: account transfer, <br>cellphone: mobile phone  |
|              | useEscrow          | Boolean | O        |       | Escrow transaction status <br> true: Escrow transaction, false |
|              | transactionStatus  | String  | O        | 20    | Payment processing status <br><br> paid: payment completed<br> canceled: canceled<br> partialCancelled: partially canceled  |
|              | transactionAt | String  | O        | 19    | Time of transaction completed <br><br> The time of the `tid`, not the time of the `originTid`. <br> ISO 8601 format (yyyy-MM-dd'T'HH:mm:ss) <br> ex) 2011-12-03T10:15:30" |


<br><br>

## Settlement API <img src="https://img.shields.io/badge/-Beta version-red">

You can retrieve settlement records based on the specified date.

<br>

### Settlement API Example code

```bash
curl --request GET 'https://api.nicepay.co.kr/v1/settlements?limit=10&type=transactionDate&date=20230308' \
--header 'Authorization: Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=' \
--header 'Content-Type: application/json' 
```

<br>

```bash
//Reponse

{
    "resultCode": "0000",
    "resultMsg": "Success",
    "pagination": {
        "count": 19,
        "hasMore": true,
        "nextSequence": 11
    },
    "settlements": [
        {
            "sequence": 1,
            "originalTid": "yeoshin01m01012303081045096292",
            "tid": "yeoshin01m01012303081045096292",
            "orderId": "32377033887",
            "currency": "KRW",
            "settlementCurrency": "USD",
            "method": "card",
            "interestFee": 0,
            "useEscrow": false,
            "net": {
                "krw": 0,
                "usd": 0
            },
            "transactionStatus": "cancelled",
            "cardAcquiringStatus": false,
            "amount": 9827,
            "supplyAmt": 8934,
            "fee": 0,
            "vat": 0,
            "exchangeRate": 1319,
            "transactionAt": "2023-03-08T00:00:00",
            "paidOutDate": "20230313"
        },
        {
            "sequence": 2,
            "originalTid": "yeoshin01m01012303081048526992",
            "tid": "yeoshin01m01012303081048526992",
            "orderId": "59721917475",
            "currency": "KRW",
            "settlementCurrency": "USD",
            "method": "card",
            "interestFee": 0,
            "useEscrow": false,
            "net": {
                "krw": 0,
                "usd": 0
            },
            "transactionStatus": "cancelled",
            "cardAcquiringStatus": false,
            "amount": 4153,
            "supplyAmt": 3775,
            "fee": 0,
            "vat": 0,
            "exchangeRate": 1319,
            "transactionAt": "2023-03-08T00:00:00",
            "paidOutDate": "20230313"
        }

        ...
        
    ]
}

```

<br>

> #### ⚠️ Important  
> In the Beta version, the search period is fixed, and there may be changes in parameters and response values.  

<br>

### Settlement API Request parameter

```bash
GET /v1/settlements
HTTP/1.1  
Host: api.nicepay.co.kr //Host will be changed after beta
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

<br>

| Parameter        | Type    | Required | Bytes | Description                                                                      |
|:-----------------|:-------:|:--------:|:-----:|:---------------------------------------------------------------------------------|
| date             | String  | O        | 19    | Start date for transaction search <br> (yyyyMMdd)  <br> ex) 20230201 |
| type             | String  | O        | 30    | Starting point of the sequence. <br> The sequence is created based on the startDate.    |
| startingSequence | Integer | -        | 30    | Starting point of the sequence. <br> The sequence is created based on the startDate.    |
| limit            | Integer | -        | -     | default 100 <br> max 500   |

<br>

### Settlement API Response parameter

| Parameter  |                    | Type    | Required | Bytes | Description |
|------------|--------------------|---------|----------|-------|-------------|
| resultCode |                    | String  | O        | 4     | 0000 : success / other failure |
| resultMsg  |                    | String  | O        | 100   | Result message  |
| pagination |                    | Object  |          |       |                 |
|            | count              | Integer | O        |       | The number of transactions objects responded |
|            | nextSequence       | Integer | O        |       | Next sequence  |
|            | hasMore            | Boolean | O        |       | true:There is data on the next page <br> false:This is last page|
|settlements |                    | Array   | O        |       |                |
|            | sequence           | Integer | O        |       | The index of the retrieved transaction |
|            | originlTid         | String  | O        | 30    | NICEPAY original transaction ID               |
|            | tid                | String  | O        | 30    | NICEPAY transaction ID |
|            | orderId            | String  | O        | 64    | Unique order number  |
|            | currency           | String  | O        | 3     | Approved currency<br>KRW: Korean Won, USD: USD, CNY: Yuan  |
|            | settlementCurrency | String  | O        | 3     | Settlement currency<br>KRW: Korean Won, USD: USD  |
|            | amount             | Integer | O        | 12    | payment amount |
|            | interestFee        | Integer | O        | 14    | Interest-free fee |
|            | useEscrow          | Boolean | O        |       | Escrow transaction status <br> true: Escrow transaction, false |
|            | transactionStatus  | String  | O        | 20    | Payment processing status <br><br> paid: payment completed<br> canceled: canceled<br> partialCancelled: partially canceled   |
|            | cardAcquiringStatus  | String  | O        | 20    | Payment processing status <br><br> paid: payment completed<br> canceled: canceled<br> partialCancelled: partially canceled   |
|            | method             | String  | O        | 10    | Payment method<br><br>card: credit card <br>vbank: virtual account  <br>bank: account transfer  <br>cellphone: mobile phone |
|            | fee                | Integer | O        | 10    | fee     |
|            | supplyAmt          | Integer | O        | 14    | Supply amount of the payment amount       |
|            | vat                | Integer | O        | 14    | VAT of the payment amount     |
|            | exchangeRate       | Integer | O        | 14    | Exchange rate (European trems) <br><br> ex) US dollar to Korean won exchange rate  |
|            | net                | Array   | O        |       | net = amount - fee <br> The amount indicated in the settlement currency will actually be deposited |
|            | net.krw            | Integer | O        | 14    | Expected settlement amount in Korean won |
|            | net.usd            | Integer | O        | 14    | Expected settlement amount in dollars |
|            | transactionAt      | String  | O        | 19    | When transaction is complete |
|            | paidOutDate        | String  | O        | 19    | Scheduled settlement date based on Korea time |
|            | sendDate           | String  | O        | 19    | Scheduled settlement send date based on Korea time |