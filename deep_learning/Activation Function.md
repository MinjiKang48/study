# Activation Function

- 입력을 받아 활성, 비활성을 결정하는데 사용되는 함수
  - 신경망의 출력을 결정하는 식
- NN에서 뉴런(node)에 연산 값을 계속 전달해주는 방식으로 가중치를 훈련, 예측 진행
- 각각의 함수는 네트워크의 각 뉴런에 연결되어 있으며, 각 뉴런의 입력이 모델의 예측과 관련되어 있는지 여부에 따라 활성화됨
  - 활성화 : 신경망은 입력값에서 필요한 정보를 학습
- 훈련 과정에서 계산량이 많고, backpropagation에서도 사용해야 하므로 연산에 대한 효율성은 중요
- 입력 값에 따라 출력 값의 모양이 어떠한가에 따라 linear과 non-linear로 나눌 수 있다.
- linear function
  - 1차 함수
  - 1개의 직선 모양
  - 문제의 결과가 직선을 따르는 경향
- non-linear function
  - 2개 이상의 직선 또는 곡선의 모양
  - Sigmoid, ReLU
  - 2개를 분류하는 문제
    - Vanishing Gradient Problem
      - Sigmoid 잘 사용하지 않음
      - ReLU와 그 변형된 활성화 함수 주로 이용
  - 3개 이상을 분류할 때
    - Softmax와 그 변형된 활성화 함수 주로 이용
- 목표 정확도와 학습 시간 등을 고려하여 선택하고 혼합 사용 가능



- NN에서는 activation function으로 linear function가 아닌 non-linear function을 사용해야 함
  - hidden layer를 여러겹의 layer로 구성하고 모두 linear function을 사용
    - 단층으로 구성한 것과 동일하게 구성 가능 : 무의미
  - 따라서 1개 이상의 non-linear function을 사용해야 함



#### Sigmoid function

- 출력 범위
  - [0, 1] 사이
  - 양수로 커질수록 1에 가까워지고 음수로 갈수록 0에 가까워짐
  - sigmoid를 미분한 식은 x축 0에 가까울수록 결과값이 커진다.
    - training이 잘 된다는 의미
    - 0에서 멀어지면 training이 잘 안된다는 의미



#### Hyperbolic tangent function

- RNN에서 봤던 activation function
- 출력범위
  - [-1, 1]
- Sigmoid function과 매우 유사한 식



#### Softmax function

- max를 soft하게 뽑아줌
- Discrete Probability Distribution
  - 이산 확률 분포

- multi-classification의 activation function으로 주로 사용
- 자신의 weight 합뿐만 아니라, 다른 출력 노드들의 weight 합도 고려함
- 범주 수만큼의 차원을 갖는 입력벡터를 받아서 확률로 변환해 준다.
- 출력 범위
  - [0.0, 1.0] 실수
  - 노드의 출력을 모두 합한 값이 항상 1이 됨
- 모든 출력 값의 상대적인 크기를 고려한 값을 출력하기 때문에, multi-classification NN에 적합
- 2개의 주요 단계
  - 이미지가 어떤 레이블에 속하는지 근거를 계산하는 과정
  - 근거들을 각 레이블에 대한 확률로 변환하는 것
- 벡터의 각 원소를 정규화하기 위해 softmax 함수는 입력 값을 모두 지수 값으로 바꾼다.
- 지수 함수를 사용하면 가중치를 더 커지게 하는 효과를 얻을 수 있음
  - 이렇게 되면 좋은 점이 뭐지?????
- 한 클래스의 근거가 작을 때 이 클래스의 확률도 더 낮아지게 된다
- 가중치의 합이 1이 되도록 정규화하여 확률분로를 만들어 준다.
- 장점
  - 예측이 잘 이루어지면 1에 가까운 출력은 하나만 있고 다른 출력은 0에 가까워진다.
    - 예측이 잘 이루어지지 않으면 여러 레이블이 비슷한 확률을 가지게 될 수 있음



#### ReLU (Rectified Linear Unit)

- Sigmoid function은 gradient vanishing problem를 발생시킴
- 따라서 ReLU를 activation function으로 사용
  - Sigmoid보다 오차가 훨씬 더 잘 전달된다.
- 입력이 음수면 0을 출력하고, 양수이면 입력값을 그대로 출력하는 함수
- Sigmoid 함수의 경우
  - 입력값이 아무리 커도 NN 노드의 출력은 1을 넘지 못함
  - ReLU 함수는 이런 제한이 없음
- ReLU 함수의 backpropagation algorithm을 구현하려면 ReLU함수의 도함수가 필요
