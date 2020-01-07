# OpenCV

## 1. 기본입출력

> **OpenCV**
>
> - 영상 처리와 컴퓨터 비전을 위한 오픈소스 라이브러리
> - C, C++, Python 등에서 사용 가능
> - 파이썬 버전 2.7과 3.x에 따른 지원에 차이가 거의 없음

- openCV 설치

  ```python
  !pip install opencv-python
  ```

  

### 1. 기본 입출력

#### 1-(1) 이미지, 비디오 입출력

- 새 창에 이미지 띄우기

  ```python
  import cv2
  
  img_file = 'img/girl.jpg'
  img = cv2.imread(img_file)
  
  cv2.imshow('IMG', img)
  cv2.waitKey()
  cv2.destroyAllWindows()
  ```

  - `cv.imread(file_name, [mode_flag])`:  파일로부터 이미지 읽기

    mode_flag = cv2.IMREAD_COLOR: 읽기모드 지정

    * cv2.IMREAD_COLOR: 컬러(BGR)스케일로 읽기 [default]
    * cv2.IMREAD_UNCHANGED: 파일 그대로 읽기
    * **cv2.IMREAD_GRAYSCALE: 그레이(흑백) 스케일로 읽기** 

  - `cv2.imshow(title, image파일)`: 이미지를 'title' 이름의 창에 띄움 

  - `cv2.waitKey(time)`

    time: 입력 대기 시간, 0은 무한대기

    사용자가 어떤 키를 입력했을 때 아스키 코드로 반환 (esc:27)

    time 인자값이 없을 경우 아무키나 입력해도 창이 닫힘

  - `cv2.destroyAllWindow()`: 화면의 모든 윈도우를 닫는 함수

- jupyter notebook에 이미지로 바로 나타내기

  ```python
  import matplotlib.pyplot as plt
  import cv2
  
  img = cv2.imread('img/girl_face.jpg')
  
  plt.axis('off') #축 off - 꺼주지 않으면 가로 세로에 축이 나옴
  plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
  #openCV는 RGB가 아닌 BGR을 사용 -> 그래서 BGR을 RGB로 변경해주어야 볼 수 있음
  plt.show()
  ```

- 그레이 스케일로 읽기

  ```python
  import matplotlib.pyplot as plt
  import cv2
  
  img_file = 'img/image.jpg'
  img = cv2.imread(img_file, cv2.IMREAD_GRAYSCALE) #그레이스케일
  
  plt.axis('off')
  plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
  plt.show()
  ```



#### 1-(2) 이미지 저장하기

- 경로에 파일 저장

  ```python
  import cv2
  
  img_file = 'img/image.jpg'
  cv2.imwrite('img/image_gray.jpg', img_file)
  ```
  - cv2.imwrite(file_path, img)`: 파일 경로에 이미지파일을 저장



#### 1-(3) 예제

1. 컬러이미지를 윈도우 이미지로 열기

2. 저장 후 아무 키나 눌러서 닫기

3. 흑백사진으로 열기. 아무 키나 눌러서 닫기

   ```python
   import cv2
   
   #컬러 이미지를 윈도우 이미지로 열기
   img = 'img/house.jpg'
   img1 = cv2.imread(img, cv2.IMREAD_COLOR)
   cv2.imshow('IMG', img)
   cv2.waitKey()
   cv2.destroyAllWindows()
   
   #저장
   cv2.imwrite('img/house_color.jpg', img1)
   
   #흑백사진으로 열기
   img2 = cv2.imread(img, cv2.IMREAD_GRAYSCALE)
   cv2.imshow('IMG', img2)
   cv2.waitKey()
   cv2.destroyAllWindows()
   ```

   

### 2. 동영상 및 카메라 프레임

#### 2-(1) 동영상 파일 읽기

```python
import cv2

video_file = 'img/big_buck.avi'

cap = cv2.VideoCapture(video_file) # 객체 생성

if cap.isOpened(): #객체 초기화 확인
    while True: #video 파일은 img가 연속된 것. 그래서 frame을 계속 읽어주기 위해 while true 설정
        ret, img = cap.read() 
        if ret:
            cv2.imshow(video_file, img) #프레임 표시 
            cv2.waitKey(10) #다음 프레임을 몇 초 뒤에 읽을 것인지 - 초를 늘리면 느려짐
        else:
            break
else:
    print('동영상을 열 수 없습니다.')
cap.release()
cv2.destroyAllWindows()
```



#### 2-(2) 프레임 스트림 프로퍼티 얻기

- `cv2.CAP_PROP_FRAME_WIDTH` : 프레임 폭

- `cv2.CAP_PROP_FRAME_HEIGHT` : 프레임 높이

- `cv2.CAP_PROP_FPS` : 프레임 초당 프레임 수 

- `cv2.CAP_PROP_POS_MSEC` : 동영상 파일의 프레임 위치(MS)

- `cv2.CAP_PROP_POS_AVI_RATIO` : 동영상 파일의 상대 위치 (0:시작 , 1:끝)

  ```python
  import cv2
  
  def print_capture_properties(*args): #*args: 함수에 들어오는 인자가 몇 개인지 모를 때
      capture = cv2.VideoCapture(*args)
      print('Frame Count:', int(capture.get(cv2.CAP_PROP_FRAME_COUNT)))
      print('Frame Width:', int(capture.get(cv2.CAP_PROP_FRAME_WIDTH)))
      print('Frame Height:', int(capture.get(cv2.CAP_PROP_FRAME_HEIGHT)))
      print('Frame Rate:', int(capture.get(cv2.CAP_PROP_FPS)))
  
  print_capture_properties("img/big_buck.avi")
  ```



#### 2-(3) 비디오 파일에서 n번째 프레임 가져오기

- 100번째 프레임

  ```python
  import cv2
  
  capture = cv2.VideoCapture('img/big_buck.avi')
  frame_count = int(capture.get(cv2.CAP_PROP_FRAME_COUNT))
  print('Frame Count:', frame_count)
  
  #100번째 프레임
  capture.set(cv2.CAP_PROP_POS_FRAMES, 100)
  capture, frame = capture.read()
  cv2.imshow('frame100', frame)
  
  cv2.waitKey()
  cv2.destroyAllWindows()
  ```

  - `cap.set(id, value)`: 프로퍼티 변경
  - `cap.get(id)`: 프로퍼티 확인
  - `cv2.CAP_PROP_POS_FRAMES`: 현재 프레임 개수



### 3. 그림 그리기

#### 3-(1) 선 그리기

- 직선 그리기
  - image: 그림 그릴 배경
  - start:

```python
import cv2
import matplotlib.pyplot as plt
import numpy as np

image = np.full((512, 512, 3), 255, np.uint8)
#가로 512 세로 512인 3개의 층을 만듦
image = cv2.line(image, (0,0), (255, 255), (0,255,0), 30, cv2.LINE_4)

plt.imshow(image)
plt.show
```

<img src="./image/image-20200107193044281.png" style="zoom:50%;" /> 

 

 