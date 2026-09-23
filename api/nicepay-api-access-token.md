## Access token

<br>

### Over-view
<a href="../image/payment-access-token.svg"><img alt="Sequence diagram of the Access token flow: the Merchant Server builds a Basic Authorization header from its Client and Secret key, calls POST /v1/access-token, and receives the accessToken" src="../image/payment-access-token.svg" width="800px"></a>

- You can use the Access token API when calling an API with Bearer token HTTP authentication.
- The token is valid for 30 minutes.   
- Therefore, after the initial token is generated, the same token will be returned for 30 minutes, and if a new token is requested after 30 minutes, a newly generated token will be issued.  

We recommend testing against the [Sandbox](../info/nicepay-info-sandbox.md) first, then switching to Live once verified. The example below calls Sandbox (`sandbox-api.nicepay.co.kr`) with the public Sandbox key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information). For Live, use `api.nicepay.co.kr` and your own Live key.

<br>

### Access token API Call example

```shell
curl --location --request POST 'https://sandbox-api.nicepay.co.kr/v1/access-token' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

```shell
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "accessToken": "dc51c8b13620519b8503bef6ee18f11370e8b349",
    "tokenType": "Bearer",
    "expireAt": "2023-02-28T13:23:17.655+0900",
    "now": "2023-02-28T12:53:17.657+0900"
}
```

<br>

### Access token API request parameter

```bash
POST /v1/access-token
HTTP/1.1
Host: api.nicepay.co.kr
Authorization: Basic <credentials>
Content-type: application/json;charset=utf-8
```

> This endpoint only accepts `Basic` credentials, an existing Bearer token cannot be used to request a new one.

Required: Yes = always send; No = optional; Conditional = send in the case stated in Description. Bytes = maximum length in bytes.

|   Parameter   |  Type  | Required  | Bytes | Description  |
|:-------------:|:------:|:---------:|:----:|:-------------|
| `returnCharSet` | String |    No     |  10  | Response encoding <br> utf-8 or euc-kr <br> Default:utf-8 <br> Pass as a URL query parameter (e.g. `?returnCharSet=euc-kr`), not in the JSON body |

<br>

### Access token API response (Body)
```bash
Content-type: application/json
```

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

|  Parameter  |  Type  | Required  | Bytes | Description  |
|:-----------:|:------:|:---------:|:----:|:-------------|
| `resultCode`  | String |  Yes  |  4   | Response code<br>0000:success, other failures  |
|  `resultMsg`  | String |  Yes  | 100  | Result message  |
| `accessToken` | String |  Yes  |  40  | Access token  |
|  `tokenType`  | String |  Yes  |  10  | Authentication Scheme Type <br> 'Bearer' fixed  |
|  `expireAt`  | String |  Yes  |      | Token expiration time<br>ISO 8601 format |
|     `now`     | String |  Yes  |      | Current time<br> ISO 8601 format |