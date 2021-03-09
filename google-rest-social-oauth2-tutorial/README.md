# Google Rest Social OAuth2 적용 가이드

## Google Login

### 1. Key 와 Secret 발급을 위한 절차

https://console.cloud.google.com/apis/credentials

위 페이지로 가서 OAuth 2.0 키를 발급해야한다.

`+ 사용자 인증 정보 만들기` -> `OAuth 클라이언트 ID` 순서로 누른다.

<img width="957" alt="Screenshot_1" src="https://user-images.githubusercontent.com/42999044/110472735-1029a280-8121-11eb-9d49-14952b1d3efb.png">


만약 OAuth 를 프로젝트에서 처음 발급한다면 반드시 동의화면을 구성해야므로 `동의화면구성` 버튼을 누른다.

<img width="957" alt="Screenshot_2" src="https://user-images.githubusercontent.com/42999044/110472774-2172af00-8121-11eb-893b-fcff8a08837c.png">

동의 화면 구성인데, 외부를 누른다. ( 내부는 어차피 안눌린다. ) 이후 `만들기` 버튼을 누른다.

<img width="957" alt="Screenshot_3" src="https://user-images.githubusercontent.com/42999044/110472807-2e8f9e00-8121-11eb-86ce-5decb3ee9704.png">

앱 정보를 입력하는데 원하는 대로 입력한다. 필수값만 입력해주면 된다. `앱이름`, `사용자 지정 이메일`

<img width="957" alt="Screenshot_4" src="https://user-images.githubusercontent.com/42999044/110472827-33ece880-8121-11eb-898e-cf588a6d9910.png">

아래로 내린 후 `개발자 연락처 정보` 를 입력한 후 `저장 후 계속` 버튼을 누른다.

<img width="957" alt="Screenshot_7" src="https://user-images.githubusercontent.com/42999044/110472845-38b19c80-8121-11eb-8110-73ca34f0e3f7.png">

범위를 지정하는 것이 있는데 딱히 지정하지 않고 `저장 후 계속` 버튼을 누른다.

<img width="957" alt="Screenshot_5" src="https://user-images.githubusercontent.com/42999044/110472879-423b0480-8121-11eb-979a-8f11f5437aec.png">

동의화면 구성의 마지막인 테스트사용자를 추가흔ㄴ 것이 있는데, 아무것도 하지 않고 `저장 후 계속` 버튼을 누른다.

<img width="957" alt="Screenshot_6" src="https://user-images.githubusercontent.com/42999044/110472899-47984f00-8121-11eb-80b3-b92a96f7dc4b.png">

다시, 아래 링크로 돌아와서 `+ 사용자 인증 정보 만들기` -> `OAuth 클라이언트 ID` 순서로 누른다.

https://console.cloud.google.com/apis/credentials

<img width="957" alt="Screenshot_1" src="https://user-images.githubusercontent.com/42999044/110472935-50892080-8121-11eb-905e-fa68ac508ab2.png">

필수 값을 입력하고, 로컬에서 테스트하기 위해 리다이렉션 링크를 `https://localhost:8000` 을 넣어준다.

<img width="957" alt="Screenshot_2" src="https://user-images.githubusercontent.com/42999044/110472961-5979f200-8121-11eb-9eee-6612d111ca56.png">

`만들기` 버튼을 누르면 `클라이언트 ID`, `클라이언트 보안 비밀번호`가 발급된다.

<img width="957" alt="Screenshot_3" src="https://user-images.githubusercontent.com/42999044/110472979-5da60f80-8121-11eb-9778-8248c1431334.png">

위에서 발급한 `클라이언트 ID`, `클라이언트 보안 비밀번호` 가 Django 세팅에 필요한 값들이다. 잘 복붙해놓자.


### 2. Social Login 을 사용하기 위한 설정 추가

Python social Auth 문서의 Backend 에 내가 사용할 Backend 의 문서를 보게 되면 어떤 설정을 더 추가할 수 있는지 나온다.

https://python-social-auth.readthedocs.io/en/latest/backends/google.html#google-oauth2

```python
SOCIAL_AUTH_GOOGLE_OAUTH2_KEY = "<위에서 생성한 클라이언트 ID>"
SOCIAL_AUTH_GOOGLE_OAUTH2_SECRET = "<위에서 생성한 클라이언트 보안 비밀번호>"
```

### 3. Django Admin 에서 OAuth Application 설정

프론트에서 social 인증을 받고나서 backend 로 한번 더 인증을 받아야하는데 그에 대한 설정이다.

django admin 페이지를 들어간 후 `Applications` 로 들어간다.

<img width="957" alt="Screenshot_2" src="https://user-images.githubusercontent.com/42999044/110473032-6c8cc200-8121-11eb-8a41-d6ef221e8ecd.png">

이후 `Application +` 버튼을 누른다.

<img width="957" alt="Screenshot_3" src="https://user-images.githubusercontent.com/42999044/110473049-71517600-8121-11eb-939b-a06cb510bf54.png">

필수인 부분을 입력하고 `SAVE` 를 누른다.

- `Redirect urls`: 인증 완료 이후 Redirect 될 URL ( django rest framework 를 쓴다면 아무거나 상관없다. )
- `Client type`: `Confidential`
- `Application grant type`: `Authorization code`

<img width="957" alt="Screenshot_4" src="https://user-images.githubusercontent.com/42999044/110473075-76162a00-8121-11eb-8076-4965d09229d4.png">

다시 위의 사진에서, `client_id`, `client_secret` 이 프론트에서 사용해야 할 키 값들이다.

<img width="957" alt="Screenshot_5" src="https://user-images.githubusercontent.com/42999044/110473089-7adade00-8121-11eb-8533-974795b04c93.png">

### 4. 프론트에서는 어떻게 사용하나?

`app/templates/home.html` 에서 더 자세한 예가 나와있다.

```javascript
// 1. Social Login 으로 받은 정보 중 AccessToken 을 가져옴
let accessToken = googleUser.getAuthResponse(true).access_token;

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
