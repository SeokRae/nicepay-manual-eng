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
| payment window (live)   | pay.nicepay.co.kr         | 121.133.126.85/27                  | OUTBOUND |
| payment window (sandbox)  | sandbox-pay.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| API (live)  | api.nicepay.co.kr         | 121.133.126.83/27                  | OUTBOUND |
| API (sandbox) | sandbox-api.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| Webhook  | -  | 121.133.126.86 <br> 121.133.126.87 | INBOUND  |


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