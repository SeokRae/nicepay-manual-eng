## API Authentication

### Basic authentication
To access API, `credentials` is required for `HTTP Authorization header`.

<br>

#### HTTP header Basic authentication scheme
```bash
Authorization: Basic <credentials>
```

#### Credentials Generation Algorithm
```bash
Base64(`client-key:secret-key`)
```

<br>

#### Credentials Generation example

First step
```bash
clientKey = 'af0d116236df437f831483ee9c500bc4'
secretKey = '433a8421be754b34989048cf148a5ffc'
>> `af0d116236df437f831483ee9c500bc4:433a8421be754b34989048cf148a5ffc`
```

Second step
Encode the plain text to `Base64` to complete the `Credentials`

```bash
// Base64('{clientId}:{secretKey}') 
Base64('af0d116236df437f831483ee9c500bc4:433a8421be754b34989048cf148a5ffc')
>> `YWYwZDExNjIzNmRmNDM3ZjgzMTQ4M2VlOWM1MDBiYzQ6NDMzYTg0MjFiZTc1NGIzNDk4OTA0OGNmMTQ4YTVmZmM=`
```  

Set `Credentials` to `HTTP header` for HTTP authentication.
```bash
Authorization: Basic YWYwZDExNjIzNmRmNDM3ZjgzMTQ4M2VlOWM1MDBiYzQ6NDMzYTg0MjFiZTc1NGIzNDk4OTA0OGNmMTQ4YTVmZmM= 
```

<br><br>

### Bearer token
This method uses the `OAuth` based `Bearer` authentication scheme for API access control.   
To access Token, `Token API` must be called.  

<br>

#### HTTP header Bearer authentication scheme
```bash
Authorization: Bearer <token>
```

#### Bearer Token API call example
- Call `Access token` API and then use for authentication.

```shell
curl -X POST "https://api.nicepay.co.kr/v1/access-token" \
-H "Content-Type: application/json" \
-H "Authorization: Basic YWYwZDExNjIzNmRm..."
```

<br>

#### Access token API response example
```bash
{
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "accessToken": "6d0a7caa1b7358c8aa06ef3706e01bb1feb2c65dacc7147b258dfdd6191b5279",
  "tokenType": "Bearer",
  "expireAt": "2021-07-31T00:58:02.000+0900",
  "now": "2021-07-20T15:28:26.882+0900"
}
```

<br>

#### HTTP header Bearer token setting

```bash
Authorization: Bearer 6d0a7caa1b7358c8aa06ef3706e01bb1feb2c65dacc7147b258dfdd6191b5279
```
> The issued token is valid for 30 minutes and renewal of the issued token is not supported.  
> When a token expires, a new token needs to be generated.  
> If there is a request within the valid time of the previously issued token, the existing token will be returned.  