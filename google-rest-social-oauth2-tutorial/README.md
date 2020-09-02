# Google Rest Social OAuth2 적용 가이드

## Google Login

### 1. Key 와 Secret 발급을 위한 절차

https://console.cloud.google.com/apis/credentials

위 페이지로 가서 OAuth 2.0 키를 발급한다.

1. \+ CREATE CREDENTIALS -> OAuth client ID

2. 정보 입력

Application type -> Web Application

Name -> 자유롭게

Authorized Javascript origins -> 어느 도메인에서 로그인을 허용할지 ( 주의점이 있다면 127.0.0.1 을 사용하면 에러가 납니다. 대신 localhost 를 사용해주세요 )

Authorized redirect URIs -> 로그인 이후 redirect 되는 URI 를 어떤것만 허용할지 ( 주의점이 있다면 127.0.0.1 을 사용하면 에러가 납니다. 대신 localhost 를 사용해주세요 )

### 2. Social Login 을 사용하기 위한 설정 추가

Python social Auth 문서의 Backend 에 내가 사용할 Backend 의 문서를 보게 되면 어떤 설정을 더 추가해야하는 나옵니다.

https://python-social-auth.readthedocs.io/en/latest/backends/google.html#google-oauth2

```python
# 잊어버렸으면 https://console.cloud.google.com/apis/credentials 로 다시 들어가서 내가 생성한 Oauth2 계정을 수정한 다음 오른쪽에 나타나있는 ClientID, Client Secret 을 가져옵니다.
SOCIAL_AUTH_GOOGLE_OAUTH2_KEY = "<1 번에서 생성한 Client 의 ClientID>"
SOCIAL_AUTH_GOOGLE_OAUTH2_SECRET = "<1 번에서 생성한 Client 의 Secret>"

# Define SOCIAL_AUTH_GOOGLE_OAUTH2_SCOPE to get extra permissions from Google.
SOCIAL_AUTH_GOOGLE_OAUTH2_SCOPE = [
    'https://www.googleapis.com/auth/userinfo.email',
    'https://www.googleapis.com/auth/userinfo.profile',
]
```

### 3. Application 설정

1. Admin 페이지로 이동

2. DJANGO OAUTH TOOLKIT -> Applications 의 + Add 클릭

3. 정보 입력

Client type -> Confidential

Authorization grant type -> Authorization code

Name -> 자유롭게

4. Save

5. 이후에 ClientId, ClientSecret 을 프론트분에게 알려드립니다.

### 4. 프론트에서는 어떻게 보내야하나?

`app/templates/home.html` 에 더 자세한 예가 나와있습니다.

1. 프론트 에서 Google Login 시도

2. 프론트의 Google Login 이후 backend 의 login 으로 보내야합니다.

```javascript
// 1. Social Login 으로 받은 정보 중 AccessToken 을 가져옴
let accessToken = googleUser.getAuthResponse().access_token;

// 2. 보낼 형태를 맞춤
const data = {
    grant_type: 'convert_type',
    client_id: '<이전 단계에서 전달받은 clientId>',
    client_secret: '<이전 단계에서 전달받은 clientSecret>',
    backend: 'google-oauth2',
    token: accessToken,
}

// 3. 전송
axios.post('/auth/covert-token', data)
```
