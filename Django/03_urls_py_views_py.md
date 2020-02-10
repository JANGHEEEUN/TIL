# Django

### urls.py & views.py

---

- `lotto` 앱 메인 화면 바꾸기

  1. url이 들어왔을 때 담당하는 `site_1/urls.py`에 메인을 view.index로 뜨도록 설정
  
     path('', views.index)을 해주고 views가 어딘지 `site_1`은 알지 못하므로 import 해주어야 함

     ```python
     from lotto import views
     
     urlpatterns = [
         #127.0.0.1:8000/admin/ 과 같은 의미
         path('admin/', admin.site.urls),
         #127.0.0.1:8000/
         path('', views.index), #views라는 파이썬 파일 안에 있는 index라는 함수를 연결해주고 싶다
     ]
     ```
  
  2. `lotto/view.py`에 메인 화면에 띄우고 싶은 설정으로 변경
  
     ```python
     from django.shortcuts import render
     from django.http import HttpResponse
     
     # Create your views here.
     def index(request):
         return HttpResponse('<h1>Hello, world!</h1>')
     
     ```
  
  3. 변경사항 확인
  
     ![image-20200210172231506](./Image\image-20200210172231506.png) 



- 새로운 함수 `hello`

  1. `site_1/urls.py`에 `localhost:8000/hello`일 때 `view.hello`가 메인에 뜨도록 설정

     path('hello/',views.hello, name='hello_main'), 추가해주기!

     ```python
     urlpatterns = [
         path('admin/', admin.site.urls),
         path('', views.index),
         #127.0.0.1:8000/hello/
         path('hello/',views.hello, name='hello_main'), 
     ]
     ```

  2. `lotto/views.py`에 `hello`함수 설정

     ```python
     def hello(request):
         return HttpResponse("<h1 style='color:red;'>Hello, world!</h1>")
     ```

  3. 실행 결과

     주소 뒤에 hello를 붙여주면 글씨가 빨간색으로 변하는 것 확인 가능

     ![image-20200210172822496](./Image\image-20200210172822496.png) 

