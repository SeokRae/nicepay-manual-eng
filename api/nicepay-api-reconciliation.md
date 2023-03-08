# Reconciliation


## Transaction Search API <img src="https://img.shields.io/badge/-Beta version-red">

You can use the Transaction Search API to retrieve and cross-check transaction records for the desired period.

<br>

### Transaction Search API Example code

```bash
curl --request GET 'https://api.nicepay.co.kr/v1/transactions?startDate=2023-02-28T00:00:00&endDate=2023-02-28T00:00:00&limit=10' \
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
        "nextSequence": 4
    },
    "transactions": [
        {
            "sequence": "1",
            "originalTid": "yeoshin01m01012302281243584918",
            "tid": "yeoshin01m01012302281243584918",
            "orderId": "503cfe59-17d1-4133-bd76-5fee53bb6d61",
            "currency": "KRW",
            "amount": "1004",
            "method": "card",
            "useEscrow": "false",
            "transactionStatus": "cancelled",
            "cardAcquiringStatus": "false",
            "transactionAt": "2023-02-28T00:00:00"
        },
        {
            "sequence": "2",
            "originalTid": "yeoshin01m01012302281126204943",
            "tid": "yeoshin01m01012302281126204943",
            "orderId": "55113904588",
            "currency": "KRW",
            "amount": "4512",
            "method": "card",
            "useEscrow": "false",
            "transactionStatus": "cancelled",
            "cardAcquiringStatus": "false",
            "transactionAt": "2023-02-28T00:00:00"
        },
        {
            "sequence": "3",
            "originalTid": "yeoshin01m01012302281128386877",
            "tid": "yeoshin01m01012302281128386877",
            "orderId": "54565357369",
            "currency": "KRW",
            "amount": "1183",
            "method": "card",
            "useEscrow": "false",
            "transactionStatus": "cancelled",
            "cardAcquiringStatus": "false",
            "transactionAt": "2023-02-28T00:00:00"
        }
    ]
    ...
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
| startDate        | String  | O        | 19    | Start date for transaction search (yyyyMMdd)  <br> ex) 2023-02-28T00:00:00 |
| endDate          | String  | O        | 19    | End date for transaction search (yyyyMMdd) <br> ex) 2023-02-28T00:00:00 |
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
curl --request GET 'https://api.nicepay.co.kr/v1/settlements?date=2023-03-07T00:00:00&type=transactionDate' \
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
        "count": 13,
        "hasMore": false,
        "nextSequence": 0
    },
    "settlements": [
        {
            "sequence": "1",
            "originalTid": "yeoshin01m01012303070846212106",
            "tid": "yeoshin01m01012303070846212106",
            "orderId": "39397277124",
            "currency": "KRW",
            "settlementCurrency": "USD",
            "method": "card",
            "interestFee": "0",
            "useEscrow": "false",
            "net": {
                "krw": "0",
                "usd": "0"
            },
            "transactionStatus": "cancelled",
            "cardAcquiringStatus": "false",
            "amount": "7517",
            "supplyAmt": "6834",
            "fee": "0",
            "vat": "0",
            "exchangeRate": "1296.7",
            "transactionAt": "2023-03-07T00:00:00",
            "paidOutDate": "2023-03-12T00:00:00",
            "sendDate": "2023-03-12T00:00:00"
        },

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