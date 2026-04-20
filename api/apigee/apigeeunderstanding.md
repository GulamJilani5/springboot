# Apigee Proxy Flow for Vehicle Insurance App

### External Service:

- Our applicaiton wants to fetch data(details) from this service.
- url: `https://mockapi.io/claims`

### Apigee Proxy:

- Acts as a middleman between our app and the external service.
- URL: `https://your-org-test.apigee.net/v1/claims`

### Our Application

- We need to fetch the claim details from the external service(**e.g.** using `restTemplate` etc.)
- When endpoint `/api/claims/{claimId}` is called (e.g., via **UI**):
- Request does **not** go directly to **External Service** (`https://mockapi.io/claims.Instead`), instead it goes  
   to the **Apigee Proxy** `(https://your-org-test.apigee.net/v1/claims/{claimId})`.
- Apigee verifies the **API keys**, **Basic Auth**, **OAuth 2.0**, **JWT**, etc. in the request headers.
- If **Apigee** found valid, **Apigee** `routes` the request to the External Eervice (`https://mockapi.io/claims`).
- Now **External Service** returns claim details to **Apigee**.
- Finally **Apigee** `forwards` the claim details to our application.
