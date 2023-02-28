## Client and Secret key

<br>

### Client key
The client key can be checked in the `Development Information` page from admin web.  
The generated client key is used when calling the payment window or making API authentication key.  

### Client Key Type
Depending on the method of calling the payment window, the client key can be issued.  

- Server Authentication: Payment window request (authentication) and payment (approval) API calls are separated
- Client Authentication: Payment (approval) is automatically processed after requesting (authentication) the payment window

<br>

### Secret key
The generated secret key is used to create an API authentication key.

### ⚠️ IMPORTANT
> The sandbox and the live `secret key` are may different.  
> If you want to convert sandbox to live, must change live mode `secret key`.  
> Be careful not to expose the secret key.  
