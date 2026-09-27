## API Authentication

### Basic authentication
To access API, `credentials` is required for `HTTP Authorization header`.

<br>

#### HTTP header Basic authentication scheme
```bash
Authorization: Basic <credentials>
```

#### Credentials Generation Algorithm
```text
Base64(<client key>:<secret key>)
```

<br>

#### Credentials Generation example

First step: join the client key and the secret key with a colon (`:`).

```text
af0d116236df437f831483ee9c500bc4:433a8421be754b34989048cf148a5ffc
```

Second step: encode that text in Base64 to get the `Credentials`. In a shell:

```bash
echo -n 'af0d116236df437f831483ee9c500bc4:433a8421be754b34989048cf148a5ffc' | base64
```

Output:

```text
YWYwZDExNjIzNmRmNDM3ZjgzMTQ4M2VlOWM1MDBiYzQ6NDMzYTg0MjFiZTc1NGIzNDk4OTA0OGNmMTQ4YTVmZmM=
```

Keep `-n`. Without it, `echo` adds a newline that becomes part of the encoded value, and the request fails with HTTP `401` and [`U104`](../code/nicepay-code.md#api-response-code).

Set `Credentials` to `HTTP header` for HTTP authentication.
```bash
Authorization: Basic YWYwZDExNjIzNmRmNDM3ZjgzMTQ4M2VlOWM1MDBiYzQ6NDMzYTg0MjFiZTc1NGIzNDk4OTA0OGNmMTQ4YTVmZmM=
```

<br><br>

### Bearer token
This method uses the `OAuth` based `Bearer` authentication scheme for API access control.   
To access Token, the `Access token API` must be called.  

<br>

#### HTTP header Bearer authentication scheme
```bash
Authorization: Bearer <token>
```

#### Access token API call example
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
  "accessToken": "dc51c8b13620519b8503bef6ee18f11370e8b349",
  "tokenType": "Bearer",
  "expireAt": "2023-02-28T13:23:17.655+0900",
  "now": "2023-02-28T12:53:17.657+0900"
}
```

<br>

#### HTTP header Bearer token setting

```bash
Authorization: Bearer dc51c8b13620519b8503bef6ee18f11370e8b349
```
> The issued token is valid for 30 minutes and renewal of the issued token is not supported.  
> When a token expires, a new token needs to be generated.  
> If there is a request within the valid time of the previously issued token, the existing token will be returned.  