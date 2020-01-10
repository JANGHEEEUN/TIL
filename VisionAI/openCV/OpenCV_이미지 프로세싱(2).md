# OpenCV

## 2. 이미지 프로세싱

### 1. 관심영역(ROI) 지정

> roi: 관심영역 (Region Of Interest)

#### 1-(1) 좌표로 ROI 지정

- 관심영역 지정

  ```python
  import cv2
  import numpy as np
  import matplotlib.pyplot as plt 
  
  img = cv2.imread('img/girl.jpg')
  # roi 좌표
  x=320; y=150;w=50;h=50
  # roi 지정
  roi = img[y:y+h; x:x+w]
  
  #roi 전체에 사각형 그리기
  cv2.rectangle(roi, (0,0), (h-1, w-1), (0,255,0))
  cv2.imshow("IMG", img)
  
  cv2.waitKey(0)
  cv2.destroyAllWindows()
  ```

  <img src="./image/image-20200110130333627.png" alt="image-20200110130333627" style="zoom:50%;" />  

- 관심영역 복제 및 새창(roi) 띄우기

  ```python
  
  import cv2
  import numpy as np
  
  img = cv2.imread('img/sunseet.jpg')
  
  x=320; y=150; w=50; h=50
  roi = image[y:y+h, x:x+w]
  img2 = roi.copy()
  
  img[y:y+h, x+w:x+w+w] = roi #태양 2개 만들기
  cv2.rectangle(img, (x,y), (x+w+w, y+h), (0,255,0)) #2개의 태양 영역에 사각형 표시
  
  cv2.imshow("img", img)
  cv2.imshow("roi", img2)
  
  cv2.waitKey(0)
  cv2.destroyAllWindows()
  ```

  <img src="./image/image-20200110160633280.png" alt="image-20200110160633280" style="zoom:50%;" /> 



### 1-(2) 마우스 이벤트 코드 없이 간단히 ROI 지정

- `ret = cv2.selectROI([win_name], img, [showCrossHair = True, fromCenter = False])`
  - `win_name`:ROI 선택을 진행할 창의 이름, str
  - `img`: ROI 선택을 진행할 이미지, numpy ndarray
  - `showCrossHair`: 선택 영역 중심에 십자 모양 표시 여부
  - `fromCenter`: 마우스 시작 지점을 영역의 중심으로 지정
  - `ret`: 선택한 좌표와 크기(x,y,w,h) 선택을 취소한 경우 모두 0 - ` c`키를 누르면 선택 취소 

- 마우스로 roi 지정 후 저장

  ```python
  import cv2,  numpy as np
  
  img  = cv2.imread('img/sunhset.jpg')
  
  x,y,w,h = cv2.selectROI('img', img, False)
  #사진을 'img'를 이름으로 갖는 창에 띄워 ROI 지정할 수 있도록 함
  
  if w and h:
      roi = image[y:y+h, x:x+w]
      cv2.imshow('cropped', roi) #선택 영역을 새 창으로 표시
      cv2.moveWindow('cropped',0,0) #'cropped'창을 그림의 (0,0), 즉 왼쪽 상단으로 이동
      cv2.imwrite('cropped2.jpg', roi) # roi 영역만 파일로 저장
  
  cv2.imshow('img', img)
  cv2.waitKey(0)
  cv2.destroyAllWindows()
  ```

  

### 2. 컬러 스페이스

#### 2-(1) RGB, RGBA, HSV

- **RGB(row, column, channel)**

- **RGBA(row, column, channel, alpha): 배경을 투명 처리하는 alpha 채널**

  알파 채널로 전경과 배경을 분리할 수도 있어 **마스크 채널**이라고도 부름

- `img = cv2.imread(file_name, [mode_flag])`: 파일로부터 이미지 읽기

  - mode_flag: 읽기모드 지정

    `cv2.IMREAD_COLOR`: 컬러(BGR)스케일로 읽기

    `cv2.IMREAD_UNCHANGED`: 알파 채널을 가지고 있다면 BGRA로 읽음 

  ```python
  import cv2
  import numpy as np
  import matplotlib.pyplot as plt
  
  # 기본 값 옵션
  img = cv2.imread('img/opencv_logo.png') 
  # IMREAD_COLOR 옵션                   
  bgr = cv2.imread('img/opencv_logo.png', cv2.IMREAD_COLOR)    
  # IMREAD_UNCHANGED 옵션
  bgra = cv2.imread('img/opencv_logo.png', cv2.IMREAD_UNCHANGED)
  
  plt.imshow(cv2.cvtColor(bgr, cv2.COLOR_BGR2RGB))
  plt.show()
  plt.imshow(cv2.cvtColor(bgra, cv2.COLOR_BGR2RGB))
  plt.show()
  plt.imshow(cv2.cvtColor(bgra[:,:,3], cv2.COLOR_BGR2RGB))# 알파 채널만 표시
  plt.show()
  ```

  <img src="./image/image-20200110224139076.png" alt="image-20200110224139076" style="zoom:80%;" /> <img src="./image/image-20200110224156553.png" alt="image-20200110224156553" style="zoom:80%;" /> <img src="image/image-20200110224238424.pnG" alt="image-20200110224238424" style="zoom:80%;" /> 

- `out = cv2.cvtColor(img, flag)`색상 변경

  - `img`: numpy 배열 이미지

  - `flag`: 변환할 컬러 스페이스(총 274개)

    `cv2.COLOR_BGR2GRAY`: BGRR 컬러를 그레이 스케일로

    `cv2.COLOR_GRAY2BGR`: 그레이 스케일 이미지를 BGR 컬러 이미지로

    `cv2.COLOR_BGR2HSV: BGR 컬러 이미지를 HSV 컬러 이미지로

- **HSV** 

  - H:색조, S:채도, V:명도

    S: 색상의 순수한 정도, 255가 가장 순수한 색

    V: 색상의 명도, 255가 가장 밝음

    **H: 이 값만으로 색상을 확인 -> 색상 기반 작업에 효과적**

  - RGB 색상 범위에 맞는 H값

    RED: 165~180, 0~15

    GREEN: 45~75

    BLUE: 90~120

- BGR에서 HSV로 전환

  ```python
  import cv2
  import numpy as np
  
  red_bgr = np.array([[[0,0,255]]], dtype=np.unit8)
  red_hsv = cv2.cvtColor(red_bgr, cv2.COLOR.BGR2HSV)
  ```

  

#### 2-(2) 이미지 스레시홀딩*

- 바이너리 이미지
  - 검은색과 흰색만으로 표현한 것
  - 원하는 모야야을 좀 더 정확히 판단하게 해줌
  - (0,1) 또는 (0,255) 사용
- 그레이 스케일이미지
  - 0~255 사이의 픽셀로 표현
  - 연산 양을 줄일 때 사용

- **스레시홀딩**: 경계점을 기준으로 해 두 가지 분류로 나누는 것으로 바이너리 이미지를 만드는 가장 대표적 기법

  `ret, out = cv2.threshhold(img, threshold, value, type_flag)`

  - `threshold`: 경계 값

  - `value`: 경계 값 기준에 만족하는 픽셀에 적용할 값

  - `type_flag`: 스레시홀드 적용 방법 지정

    `cv2.THRESH_BINARY`: 픽셀  값이 경계 값을 넘으면 value 값을 지정, 아니면 0 지정

    `cv2.THRESH_BINARY_INV`: `cv2.THRESH_BINARY`의 반대

    `cv2.THRESH_TRUNC`: 픽셀 값이 경계 값을 넘으면 value 값 지정, 아니면 그대로 유지

    `cv2.THRESH_TOZERO`: 픽셀 값이 경계값 넘으면 유지, 아니면 0

    `cv2.THRESH_TOZERO_INV`: `cv2.THRERSH_TOZERO`의 반대

  -  `ret`: 스레시홀딩에 사용한 경계값

  ```python
  import cv2
  import matplotlib.pyplot as plt 
  
  img = cv2.imread('img/man_face.jpg', cv2.IMREAD_GRAYSCALE)
  
  imgs = []
  ret, t_bin = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY)
  ret, t_bininv = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY_INV)
  ret, t_truc = cv2.threshold(img, 127, 255, cv2.THRESH_TRUNC)
  ret, t_2zr = cv2.threshold(img, 127, 255, cv2.THRESH_TOZERO)
  ret, t_2zrinv = cv2.threshold(img, 127, 255, cv2.THRESH_TOZERO_INV)
  imgs.append(t_bin)
  imgs.append(t_bininv)
  imgs.append(t_truc)
  imgs.append(t_2zr)
  imgs.append(t_2zrinv)
  
  for i in imgs:
      plt.imshow(cv2.cvtColor(i, cv2.COLOR_GRAY2RGB))
      plt.show()
  ```

  ![image-20200110231655949](./image/image-20200110231655949.png) ![image-20200110231710730](./image/image-20200110231710730.png) 

  ![image-20200110231733277](./image/image-20200110231733277.png) ![image-20200110231748974](./image/image-20200110231748974.png)

  ![image-20200110231809613](./image/image-20200110231809613.png) 

  