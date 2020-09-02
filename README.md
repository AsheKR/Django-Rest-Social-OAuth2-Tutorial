# Django Rest Framework 에서 Social Login 으로 방황하는 당신을 위한 가이드

해당 라이브러리를 사용하면 간단하게 Social Login 과 통합할 수 있습니다.

https://github.com/RealmTeam/django-rest-framework-social-oauth2

## 공통 세팅

1. 설치

```shell script
pip install django-rest-framework-social-oauth2
```

2. settings.py 에 INSTALLED_APPS 에 추가

```python
INSTALLED_APPS = (
    '...',
    'oauth2_provider',
    'social_django',
    'rest_framework_social_oauth2',
)
```

3. urls.py 에 로그인 URL 등록

```python
urlpatterns = patterns(
    '...',
    (r'^auth/', include('rest_framework_social_oauth2.urls')),
)
```

4. 인증 설정 추가

```python
# 1. REST FRAMEWORK Authentication 설정 추가
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'oauth2_provider.contrib.rest_framework.OAuth2Authentication',
        'rest_framework_social_oauth2.authentication.SocialAuthentication',
    ),
}

# 2. Django 기본 Authentication 설정 추가
AUTHENTICATION_BACKENDS = (
    'social_core.backends.google.GoogleOAuth2',
    'rest_framework_social_oauth2.backends.DjangoOAuth2',
    'django.contrib.auth.backends.ModelBackend',
)
```

## Sample Backends

각 Social Login 별로 Tutorial 을 작성해 놓은 문서입니다.

- [Google](google-rest-social-oauth2-tutorial/README.md)