# RNN (Recurrent Neural Network)

[reference] https://colah.github.io/posts/2015-08-Understanding-LSTMs/

- sequence model
  - input - output : sequence unit
  - position index
- plain structure
  - x -> RNN(hidden state, activation function(non-linear: tanh)) -> y
  - hidden node가 방향을 가진 엣지로 연결돼 순환구조를 이루는 NN
  - 음성, 문자 등 순차적으로 등장하는 데이터 처리에 적합
  - sequence - to  - sequence : 길이 관계 없음
  - forward -> backward : parameter 갱신
  - vanishing gradient problem 발생 (앞의 정보가 뒤로 갈수록 전달되지 않음 : 장기 의존성 문제) -> LSTM



## LSTM (Long Short Term Memory)

- RNN의 hidden state에 cell-state 추가

  - 컨베이어 벨트??
  - sigmoid -> tanh
  - input gate
    - 현재 정보 기억하기
    - 기억할 정보의 양

  - forget gate
    - 과거 정보 잊기
    - 삭제 과정을 거친 정보의 양
  - output gate
    - 기억할 것과 잊을 것을 정한다.

- 긴 시퀀스 처리시에 성능이 향상됨



## GRU (Gated Recurrent Unit)

- gate 제어 방식
- update gate, reset gate 존재
- 데이터 양이 적을 때는 GRU (매개변수가 적음)
- 데이터 양이 많을 때는 LSTM
- Reset Gate
  - sigmoid 함수 : 과거 정보를 적당히 리셋
- Update Gate
  - 과거와 현재의 정보와 최신화 비율 결정
  - LSTM의 input, forget gate
- Candidate
  - 현 시점의 정보 후보군 계산
  - 리셋 게이트의 결과 곱
- update gate 결과 + candidate 결과 : 현 시점의 은닉층 계산







#### RNN

- 시계열 데이터 (Sequential data)
  - 시간별로 구성된 값의 집합
    - 음성, 자연어, 센서 데이터, 주가 등
  - 기록 추세, 실시간 경고, 예측 모델링 분석
  - 무조건 RNN을 써야하는 것은 아님
    - Classification / Regression 문제를 풀 때, multi-layer perceptron, CNN을 사용해도 됨.
    - CNN의 성능이 월등히 좋을 때도 많다
  - MLP나 CNN은 입력데이터를 처음 한 번만 입력 받고 이후의 연산 과정에서 지난 데이터를 추가적으로 고려할 일이 없다
  - **RNN은 이전 입력의 연산 결과가 현재 입력 데이터와 함께 고려된다.**
- RNN 기본 구조
  - 현재 입력(자극)
  - 과거 기억
  - 현재 기억
- Vanishing Gradient Problem이 발생
  - LSTM
  - GRU
  - RNN with ReLU
- LSTM (단기 기억 + 장기 기억)
  1. 과거의 단기 기억과 현재 입력 값으로부터 장기기억 중 지워내고 싶은 현재의 forget 게이트를 계산
  2. 과거의 단기 기억과 현재 입력 값으로부터 input 게이트 계산, 과거의 단기 기억과 현재 입력 값으로 현재의 장기 기억 후보 계산
  3. 
     1. 과거 장기기억에 forget 게이트를 곱해 불필요한 정보 삭제 
     2. 현재 장기기억 후보 값에 input 게이트를 곱해 중요한 정보 추가 
     3. 1 + 2 현재 장기기억 Cell state를 업데이트
  4. 과거의 단기 기억과 현재 입력 값으로부터 output 게이트를 계산하고 현재 장기기억에 output 게이트를 곱해서 hidden state 업데이트
- GRU (LSTM과 비슷한 성능이지만 파라미터가 적다)
  - Update gate
    - 갱신 비율
      - 현재 기억을 만들기 위한 과거 기억과 현재 입력의 비율 (균형)
  - Reset gate
    - 재조정 비율
      - 현재 기억 후보자를 만들기 위해 과거 기억을 재조정(reset)하는 비율
  - Memory gate
    - 현재 기억 후보
      - 현재 기억의 후보를 만들기 위해 현재 입력과 재조정된 과거 기억 조합
  - Final Memory
    - 현재 기억
      - 갱신 비율(update)에 따른 과거의 기억과 현재 기억 후보의 조합