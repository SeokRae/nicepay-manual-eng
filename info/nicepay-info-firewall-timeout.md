## Firewall Policy
Please check if the server's HTTP client supports `TLS 1.2` for safety.  
Allow the IP below in your firewall to call the `NicePay` API on the server.  

| Service              | Domain                    | IP Address                      | Direction       |
|--------------------|---------------------------|------------------------------------|----------|
| payment window (live)   | pay.nicepay.co.kr         | 121.133.126.85/27                  | OUTBOUND |
| payment window (sandbox)  | sandbox-pay.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| API (live)  | api.nicepay.co.kr         | 121.133.126.83/27                  | OUTBOUND |
| API (sandbox) | sandbox-api.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| Webhook  | -  | 121.133.126.86 <br> 121.133.126.87 | INBOUND  |

<br>

## Timeout Information

Information for handling timeout exceptions when configuring HTTP clients.  
If `Read-timeout` occurs, make a cancel request to prevent inconsistency in payment. Send it as a net cancel, with `isNetCancel` set to `true`: see [Cancel Request parameter (with sessionId)](../api/nicepay-api-cancel.md#cancel-request-parameter-with-sessionid) for how that differs from an ordinary cancellation.  

- Connection timeout : `5s`
- Receive(Read) timeout : `30s`