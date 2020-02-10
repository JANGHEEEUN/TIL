# Django

### 장고 프로젝트 생성 및 APP 생성

---

#### site_1/settings.py

- INSTALLED_APP

  **우리가 만든 앱인 `lotto`도 추가해 주어야 함**

  ```python
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'lotto'
  ]
  ```

  

- ROOT_URLCONF: URL configuration - url 설정을 담당하는 곳을 의미

  site_1/urls.py에서 url 설정 담당

  = 사용자에게서 흘러들어온 url을 누가 담당하는지

  ```python
  ROOT_URLCONF = 'site_1.urls'
  ```

  

- DATABASES: sqlite를 사용하도록 기본적으로 설정됨

  만약  AWS 등 다른 DB를 사용할 경우 이 부분을 고쳐주면 됨

  ```python
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.sqlite3',
          'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
      }
  }
  ```

  

- LANGUAGE_CODE / TIME_ZONE

  한국 시간을 사용하는 코드를 짤 때 서울 시간대로 세팅하도록 함

  ```python
  LANGUAGE_CODE = 'ko-kr'
  
  TIME_ZONE = 'Asia/Seoul'
  ```

  

- STATIC_ROOT 추가해주기

  STATIC_URL 밑에 넣어줌

  ```python
  STATIC_ROOT = os.path.join(BASE_DIR, 'static')
  ```



### site_1/urls.py

- urlpatterns

  ```python
  urlpatterns = [
      #127.0.0.1:8000/admin/ 과 같은 의미
      path('admin/', admin.site.urls),
      path('',)
  ]
  ```




### 장고 앱 생성

- startapp lotto

  site_1 아래 `lotto` 생성

  ```
  python manage.py startapp lotto
  ```

  ![image-20200210171328049](./Image\image-20200210171328049.png) 

  

