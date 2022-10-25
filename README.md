
## Notes
1. userinfo endpoint需要单独配置(将sas 配置为 resource server)
2. 用什么redirect_uri无所谓
3. sas可以独立存在。
4. 你甚至可以用openid这个scope去申请access_token， 然后调用resource server的api

## 401的原因
1. sas中`ProviderSettings.builder().issuer(authUrl).build()` - (对应jwt中`iss`) 与resource server中的`issuer-uri` 不匹配。


## /userinfo

```bash
user: `user/password`

export client_id=messaging-client
export redirect_uri=http://svc.com:8080/login/oauth2/code/messaging-client-oidc

browser http://auth.com:9090/oauth2/authorize?response_type=code&client_id=$client_id&redirect_uri=$redirect_uri&scope=openid

export code=mo7DoeekfJvhn0D8FgwkA4F0ZZZCL3jUWdlH_WcL9H4efZT4Rdn3Us8wi5EqtDZgRzWxg7nSAb5V9PmhcMQiZw5QgSjdoSdeXnjN7uNBHwoNGjqPwku094WqG_4SlSZ8

export access_token=$(curl -s -umessaging-client:secret \
-d "grant_type=authorization_code&redirect_uri=$redirect_uri&code=$code" \
http://auth.com:9090/oauth2/token | jq --raw-output '.access_token')


curl -i -H "Authorization: Bearer $access_token"  http://auth.com:9090/userinfo
```


## token
```json
{
  "access_token": "eyJraWQiOiI4YjI1NGIyZS1jYWY1LTQ5OGUtODIzZi05ZmYzM2U0NmMzMGEiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1c2VyIiwiYXVkIjoibWVzc2FnaW5nLWNsaWVudCIsIm5iZiI6MTY2NjY1ODkzOSwic2NvcGUiOlsib3BlbmlkIl0sImlzcyI6Imh0dHA6XC9cL2F1dGguY29tIiwiZXhwIjoxNjY2NjU5MjM5LCJpYXQiOjE2NjY2NTg5Mzl9.TONnHA7G0Yh1GjDiYNT4AF1VSZJEFuF-A4V_UUGKLYKAt8sqyTy9q4f4s5CnTRyTdv5vRNDE-Ujp4F7TnQNIZMsn5QYV_DQk1LAUd8qwljroZ23jibPzEvKijdkwQ8YECHf1ny3vkq0JOnNwDNMQM1sMHns7MnoJKxn7hPmlyZBaqRJwndFxlFCI5TdWQaq0tYiPqpvl7sa1I-oDz6dpL6Jaa2j85GNS5KFwiQHL0qVPSrJgiaM8cQAvqIaWNyYTmOElFtOtz3G3t-LYPvwRcv0HEUbCI3Rsbh9OACaQS51gmgfDaHHn73LMDrx_a86g7urDWsJHpH7tHH_gmseUbg",
  "refresh_token": "LtdJZRK0deIEMmQ_T309JsKvbBibTvnfcT1B2YbKjToPO9JyQV7m6qZgzBCBhcMh6M9-hNcMVGoMkxR8vdp7-AqQNSHfC_hwIutBlfZuVMzxRgpmCg5k9jRnD27lrz5h",
  "scope": "openid",
  "id_token": "eyJraWQiOiI4YjI1NGIyZS1jYWY1LTQ5OGUtODIzZi05ZmYzM2U0NmMzMGEiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1c2VyIiwiYXVkIjoibWVzc2FnaW5nLWNsaWVudCIsImF6cCI6Im1lc3NhZ2luZy1jbGllbnQiLCJpc3MiOiJodHRwOlwvXC9hdXRoLmNvbSIsImV4cCI6MTY2NjY2MDczOSwiaWF0IjoxNjY2NjU4OTM5fQ.cew4VZfQFEAFIt4Z6F719MOYIqMIapyRvk4nu73bz3PbWTFber3lc9jBJ7wYuNy26909y98lfR89g4OfnAUczhaqCn1nF_1vwKwubYxpGyrcweLBfL-oAuHJ9L7x2t5C_27wbXooHwlSj_lJOMoi930hp-5C21tl9thrYI05kg_dWVAER7nH9X5E6L-rNvOiFJoVxAe0Yh2gDjKKQkzd6Ykz2tClIYH_876umRK8cKDBaqg_qGZFoFk-ptm178_7s4Ark_n0teu7THwI6Z6iPDNOJ7WXh-mm4LV3Lq8SLud6ETbgLAG1Uz-HtjquqH8hpiCQpQVTLgagvu3H82opRw",
  "token_type": "Bearer",
  "expires_in": 299
}

```


### access_token
```json
{
  "sub": "user",
  "aud": "messaging-client",
  "nbf": 1666658939,
  "scope": [
    "openid"
  ],
  "iss": "http://auth.com",
  "exp": 1666659239,
  "iat": 1666658939
}
```

### id_token
```json
{
  "sub": "user",
  "aud": "messaging-client",
  "azp": "messaging-client",
  "iss": "http://auth.com",
  "exp": 1666660739,
  "iat": 1666658939
}
```

## /hello

```bash
user: `user/password`

export client_id=messaging-client
export redirect_uri=http://svc.com:8080/authorized

browser http://auth.com:9090/oauth2/authorize?response_type=code&client_id=$client_id&redirect_uri=$redirect_uri&scope=openid

export code=mo7DoeekfJvhn0D8FgwkA4F0ZZZCL3jUWdlH_WcL9H4efZT4Rdn3Us8wi5EqtDZgRzWxg7nSAb5V9PmhcMQiZw5QgSjdoSdeXnjN7uNBHwoNGjqPwku094WqG_4SlSZ8

export access_token=$(curl -s -umessaging-client:secret \
-d "grant_type=authorization_code&redirect_uri=$redirect_uri&code=$code" \
http://auth.com:9090/oauth2/token | jq --raw-output '.access_token') && echo $access_token


curl -i -H "Authorization: Bearer $access_token"  http://svc.com:8080/hello
```




