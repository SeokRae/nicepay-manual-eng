## Client and Secret key

<br>

### Client key
The client key can be checked in the `Development Information` page from admin web.  
The generated client key is used when calling the Hosted Payment Page or making API authentication key.  

### Client Key Type
Depending on the method of calling the Hosted Payment Page, the client key can be issued.  

- Server Authentication: Hosted Payment Page request (authentication) and payment (approval) API calls are separated
- Client Authentication: Payment (approval) is automatically processed after requesting (authentication) the Hosted Payment Page

<br>

### Secret key
The generated secret key is used to create an API authentication key.

> #### ⚠️ Important  
> The Sandbox and Live `secret key` values are different.  
> If you convert from Sandbox to Live, you must update to the Live `secret key`.  
> Be careful not to expose the secret key.  
