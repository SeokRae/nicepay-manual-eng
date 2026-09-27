## Basic and Bearer authentication

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

This example uses the public Sandbox key from [Test key information](./nicepay-info-sandbox.md#test-key-information).

First step: join the client key and the secret key with a colon (`:`).

```text
S1_ce1bb1ebebc44fe1a3f7cec976c83ea7:13e969a77a0545799242ccc3915243d3
```

Second step: encode that text in Base64 to get the `Credentials`. In a shell:

```bash
echo -n 'S1_ce1bb1ebebc44fe1a3f7cec976c83ea7:13e969a77a0545799242ccc3915243d3' | base64
```

Output:

```text
UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=
```

Keep `-n`. Without it, `echo` adds a newline that becomes part of the encoded value, and the request fails with HTTP `401` and [`U104`](../code/nicepay-code.md#code-u104).

Set `Credentials` to `HTTP header` for HTTP authentication.
```bash
Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=
```

<br><br>

### Bearer token
This method sends a token in the `Bearer` authentication scheme. Get the token from the [Access token](../api/nicepay-api-access-token.md) API.  

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
-H "Authorization: Basic <credentials>"
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
> A token is valid until its `expireAt`, and it cannot be renewed. A request before then returns the same token with the same `expireAt`, so cache the token until `expireAt`, not for 30 minutes from when you received it. After `expireAt`, request a new token.  

<br><br>

### When authentication fails

On every API except Access token, a failed Basic or Bearer authentication returns HTTP `401` with `resultCode` [`U104`](../code/nicepay-code.md#code-u104). Check these points:

1. The header is `Authorization: Basic <credentials>` or `Authorization: Bearer <token>`.
2. The credentials were encoded without a trailing newline, see [Credentials Generation example](#credentials-generation-example).
3. The key belongs to the environment you call: Sandbox keys work only on `sandbox-api.nicepay.co.kr`, and Live keys only on `api.nicepay.co.kr`.
4. A Bearer token is used before its `expireAt`.

The [Access token](../api/nicepay-api-access-token.md) API reports the cause with HTTP `401`: [`U101`](../code/nicepay-code.md#code-u101) when the credentials are not valid Base64, [`U304`](../code/nicepay-code.md#code-u304) when there are no credentials or no colon between the two keys, [`U117`](../code/nicepay-code.md#code-u117) when one of the keys is empty, and [`U116`](../code/nicepay-code.md#code-u116) when no client matches the key pair. It returns HTTP `403` with [`U103`](../code/nicepay-code.md#code-u103) when the key cannot be used to issue a token.

