# CNN(Convolutional Neural Network)

### Convolution

- filter(kernel)
  - 이미지 위에 stride(filter가 한 번에 얼마나 이동하는가?) 값만큼 filter를 이동시키면서 겹쳐지는 부분을 연산하는 방법
  - padding : zero-padding
    - padding이 1이라고 한다면 테두리에 0인 값들이 씌어진다.
  
- channel -> RGB : 3채널, 흑백 : 1채널

- Pooling
  - 이미지 사이즈 축소
  - sampling하는 단계??
  - fully connected 연산 대체
  - Max Pooling : 가장 큰 숫자 출력
  - Average Pooling : 평균 출력
  
- Feature extracting하는 convolution layer + extracted feature를 sub-sampling하는 pooling layer

- FC 학습시켜야 할 때는 3차원 사진 데이터를 1차원으로 평면화해야 한다.
  
  - 이미지 공간 정보 유지 : 학습 가능 모델
  
- CNN을 부분으로 나눠본다면, 
  - 이미지 특징을 추출하는 부분 + 클래스를 분류하는 부분
    - 이미지 특징을 추출하는 부분
      - convolution layer : 입력 데티어에 필터 적용, activation function 반영
      - pooling layer : optional
      - 이 layer들을 여러겹으로 쌓는다.
    - 이미지의 형태를 배열 형태로 만드는 flatten layer
    - 클래스 분류하는 부분
      - CNN의 마지막 부분 : 이미지 분류하는 FC layer + softmax  => output

- CNN 구조

  - layer 1: Convolution(feature 추출) - ReLU(activation function) - MaxPooling(sub-sampling)
  - layer 2: layer 1과 동일
  - layer 3: View(flatten하게 만드는 과정) - FC(Fully Connected)
  - 더 깊은 layer를 사용한다면 성능이 향상될까?? - N.O.

- CNN의 학습 대상은 **Filter**(hyperparameter) parameter

  - 입력 데이터가 여러 채널 -> 필터가 채널별 피처맵을 생성 -> 각 채널의 피처맵 합산
    - 최종  feature map
  - **filter, stride, padding을 조절하여 특징 추출 부분의 입력과 출력 크기를 계산하고 맞추는 작업이 중요하다!!**

  ### VGGNet

  - 3x3 convolution, Stride = 1, padding = 1
  - Basic CNN -> feature extracting -> sub sampling -> softmax classification -> fully connected
  - 19-layer
  - 사실 VGGNet은 network의 깊이가 어떤 영향을 주는지 연구하기 위해 설계되었음
  - 3x3 convolution kernel 중첩하여 사용하는 것이 성능이 제일 좋음
    - non-linear 함수 ReLU가 더 많이 들어가서, decision function이 더 잘 학습됨.
    - 학습해야 할 parameter 수가 감소한다.
  - 따라서, 큰 사이즈의 커널보다 작은 사이즈의 커널을 중첩하는 것이 좋음!!
  - VGGNet 구조는 단순하다
    - 최종단의 FC layer 3개라서 paramter가 너무 많다
      - network depth가 깊어질수록 error가 줄어든다.

  ### ResNet

  - deeper layer의 문제점
    - gradient vanishing
    - gradient exploding
    - degradation
  - palin CNN
    - 입력값 x -> 타켓값 y
    - H(x)를 매핑한다.
  - ResNet -> "skip connection"
    - H(x) = F(x) + x
    - F(x) = H(x) - x (residual)

  ### Inception

  - v1 ~ v4
  - v3 사용할 것
  - 48개 layer로 구성된 CNN
  - 이미지 분석 & 객체 탐지에 사용
  - GoogleNet에서 시작
  - 고민!!
    - 노드 간 연결을 줄이고, 행렬 연산은 Dense 연산 처리하고 싶음
  - 1x1 convolution이 Inception module에서 핵심 역할을 함
    1. 채널의 수 조절 가능
       1. 기존 convolution 연산은 지역 정보 + 채널 간의 정보를 하나의 값 도출
          1. 하나의 kernel이 2가지 역할을 수행
       2. 1x1은 채널을 조절하는 역할
          1. 최적화 과정에서 채널 간의 특징 추출
       3. 3x3은 이미지의 지역정보만 집중하여 특징 추출
       4. 역할 세분화하여 노드간의 연결을 줄임
    2. 1x1 convolution 연산하여 이미지 채널 수를 줄임
       1. 3x3, 5x5 convolution layer에서의 파라미터 절약
       2. 깊지만 파라미터 그리 크지 않음

  

  