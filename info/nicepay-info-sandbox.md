## Sandbox

Sandbox and Live have a difference in domain and Client ID.   
Please use it after checking whether the issued Client ID is for Sandbox or Live.   
Sandbox responds with TEST data, and no actual approval occurs.  

### Advantages of sandbox

- Immediate test and development are possible.  
- It is possible to test comfortably because actual payment does not occur.  
- A test system that does not affect the live environment.  

### Using Sandbox and Live domains

| Service              | Domain                    | IP Address                      | Direction       |
|--------------------|---------------------------|------------------------------------|----------|
| Create Hosted Payment (live)   | pay.nicepay.co.kr/v1/checkout | 121.133.126.85/27                  | OUTBOUND |
| Create Hosted Payment (sandbox)  | stg-pay.nicepay.co.kr/v1/checkout | - | OUTBOUND |
| API (live)  | api.nicepay.co.kr         | 121.133.126.83/27                  | OUTBOUND |
| API (sandbox) | sandbox-api.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| Webhook  | -  | 121.133.126.86 <br> 121.133.126.87 | INBOUND  |

```bash
// Test key
// stg-pay.nicepay.co.kr/v1/checkout

Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3
```

```bash
// Test key
// pay.nicepay.co.kr/v1/checkout

Client : R1_94eb3a4a30264fdba82ce0d05b465012
Secret : 12cde12449c64497a86104759b8306b6
Authorization : Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=
```

```bash
// Test key
// api.nicepay.co.kr

Client : R1_94eb3a4a30264fdba82ce0d05b465012
Secret : 12cde12449c64497a86104759b8306b6
Authorization : Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=
```

```bash
// Test key
// pay.nicepay.co.kr/v1/access-token

Client : R1_77988f331a1a411b8e215d07e2dddf5c
Secret : be0d7e00bf434ba1bbec7f827662ab30
Authorization : Basic UjFfNzc5ODhmMzMxYTFhNDExYjhlMjE1ZDA3ZTJkZGRmNWM6YmUwZDdlMDBiZjQzNGJhMWJiZWM3ZjgyNzY2MmFiMzA=
```

```bash
// Test key
// sandbox-api.nicepay.co.kr

Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3
```

#### ⚠️ IMPORTANT  
> Request parameters are the same for both live and sandbox mode.  

<br>

### Using Sandbox API's

```bash
GET/POST {URL} HTTP/1.1 
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```