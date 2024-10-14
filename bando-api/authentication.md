# API Authentication

## Using a Token
Our API is publicly accesible. But as an integration partner, you require authentication tokens so you can make more requests per hour when you are authenticated.

To authenticate your request, you will need to provide an authentication token. For the moment you can get a token by contacting us at [api@bando.cool](mailto:api@bando.cool).

You can authenticate your request by sending the token in the Authorization header of your request. For example, in the following request, replace YOUR-TOKEN with a reference to your token:

```shell
curl --request GET \
--url "https://api.bando.cool/coolness" \
--header "Authorization: Bearer YOUR-TOKEN" \
```

Failed login limit
When you hit a rate limit you will start receiving a 429 Too Many Requests response. Authenticating with invalid credentials will initially return a 401 Unauthorized response.

After detecting several requests with invalid credentials within a short period, the API will temporarily reject all authentication attempts for that user (including ones with valid credentials) with a 403 Forbidden response.

## Rate Limits
The API has a rate limit of 100 requests per hour. 
If you exceed this limit, you will receive a 429 Too Many Requests response.
