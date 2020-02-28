## Object Detection (사물 검출)

> 1. 이미지 속에 담긴 사물의 위치와 종류를 알아내는 기술
> 2. 입력 이미지에서 후보 영역을 추추란 후 CNN을 적용하여 해당 영역에 무엇이 있는지 예측

- 영역 자체가 크기 때문에 자율 주행에 사용하기엔 어려움

- CNN이 이미지만 대상으로 하는 것은 아님 - 엑셀 형태의 정형 데이터에도 적용 가능

  

### Image Segmentation (Object Segmentation)

- 



### Image Cationing

> 1. 딥러닝을 이용한 이미지 자막 생성 (by Goggle Oriol Vinyals)
> 2. 단계
>    	1. step1: 이미지를 처리해 추상화(CNN 사용)
>     	2. step2: 얻어진 이미지 정보를 이용해 문장 작성(RNN 활용)
>
> RNN: 재귀 뉴럴 네트워크 - 데이터가 퍼셉트론 내부에서 뱅뱅 돈다.
>
> > 무조건 데이터가 앞쪽 방향으로 가는 것이 아니라 맴돌다 갔다를 반복함
> >
> > 장점: 연속적으로 이어지는 데이터에서 다음 데이터가 무엇일지 예측에 능함
> >
> > ex) 주식 가격 예측



### VQA - Visaul Question Answering

- 이미지를 주고 어떤 상황인지, 무엇이 있는지를 예측



### TOEIC-BERT

- 앞의 RNN과 사뭇 다름 - BERT는 RNN이 맴맴 돌면서 앞으로 나가는 방식과 다르게 작동
- BERT의 활용하는 사람이 많아짐
- Bidirectional: 양방향의 - 기존의 RNN은 과거를 봄, 하지만 Bidirectional은 양방향을 보고 가운데에 뭐가 나올지 맞춤



### korBERT



### Image generation using DCGAN

- convolutional = 시각화 관련

- Generative Adverssarial Network: 생성해내는 다투고 있는 네트워크 - 내부에 인공신경망 2개가 병행해서 돌아감

  하나는 그럴듯한 데이터를 만들고 하나는 데이터가 진짜인지 가짜인지를 판단

  둘이 다퉈서 어느정도 두 신경망이 큰다면 두 신경망을 떼어내 활용

- 딥러닝에서 아예 새로운 패러다임을 만듦 - 그럴듯하게 이미지를 생성해내는 것은 다 GAN 모델을 사용함

- What can Gan do?

  > 전체 평면의 형태가 주어졌을 때 자동으로 설계안 생성



### Style Transfer

> 1. 특정한 화풍을 학습한 후 ~

- 이제 화풍을 넘어 사실적인 이미지를 생성

- VR AR에 활용하기 위해 개발하다가 발전함



101 넘어감



### Enhanced Deep Residual Networks for Single Image Super-Resolution

- 줄인 사진을 다시 늘려서 빈틈에 색을 채워넣어 다시 되돌리는 작업



### 딥러닝을 활용한 독순술

- 핸디캡이 있는 사람들을 위한 기술



### 딥러닝을 활용한 인공지능 앵커



