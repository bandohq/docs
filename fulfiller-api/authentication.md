---
description: Bando's fulfillment API authorization and limits.
---

# Authentication

## Using the API Token

Our API is publicly accesible. But as an integration partner, you require authentication tokens so you can make more requests per hour when you are authenticated.

{% hint style="info" %}
To authenticate your request, you will need to provide an authentication token. For the moment you can get a token by contacting us at [api@bando.cool](mailto:api@bando.cool).
{% endhint %}

You can authenticate your request by sending the token in the `Authorization` header of your request.&#x20;

For example, in the following request, replace `<bando_api_token>` with a reference to your token:

{% tabs %}
{% tab title="cURL" %}
```shell
curl --request GET \
--url "https://api.bando.cool/coolness" \
--header "Authorization: Bearer <bando_api_token>" \
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const axios = require('axios');

let config = {
  method: 'get',
  maxBodyLength: Infinity,
  url: 'https://api.bando.cool/coolness',
  headers: { 
    'Authorization': 'Bearer <bando_api_token>'
  }
};

axios.request(config)
.then((response) => {
  console.log(JSON.stringify(response.data));
})
.catch((error) => {
  console.log(error);
});
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.bando.cool/coolness"
token = "<bando_api_token>"

payload = {}
headers = {
  'Authorization': 'Bearer {{token}}'
}

response = requests.request("GET", url, headers=headers, data=payload)

print(response.text)
```
{% endtab %}

{% tab title="Ruby" %}
<pre class="language-ruby"><code class="lang-ruby"><strong>require "uri"
</strong>require "net/http"

url = URI("https://api.bando.cool/coolness")
token = "&#x3C;bando_api_token>"
http = Net::HTTP.new(url.host, url.port);
request = Net::HTTP::Get.new(url)
request["Authorization"] = "Bearer {{token}}"

response = http.request(request)
puts response.read_body
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  url := "https://api.bando.cool/coolness"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, url, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Authorization", "Bearer <bando_api_token>")

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(string(body))
}
```


{% endtab %}

{% tab title="Rust" %}
```rust
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = reqwest::Client::builder()
        .build()?;

    let mut headers = reqwest::header::HeaderMap::new();
    headers.insert("Authorization", "Bearer <bando_api_token>".parse()?);

    let request = client.request(reqwest::Method::GET, "https://api.bando.cool/coolness")
        .headers(headers);

    let response = request.send().await?;
    let body = response.text().await?;

    println!("{}", body);

    Ok(())
}
```


{% endtab %}
{% endtabs %}

When you hit a rate limit you will start receiving a 429 Too Many Requests response. \
Authenticating with invalid credentials will initially return a 401 Unauthorized response.

After detecting several requests with invalid credentials within a short period, the API will temporarily reject all authentication attempts for that user (including ones with valid credentials) with a 403 Forbidden response.

## Rate Limits

The API has a rate limit of 100 requests per hour. If you exceed this limit, you will receive a 429 Too Many Requests response.
