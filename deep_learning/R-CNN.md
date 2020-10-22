# R-CNN

"Region-based Convolutional Neural Networks"

- 2가지의 메인 아이디어

1. selective search를 이용하여 관리할 수 있는 bounding-box 객체 지역 후보("region of interest" or "RoI"; 객체의 위치) 의 수를 식별한다

2. 각각의 bounding-box를 분류하기 위해서 각 지역에서의 CNN features를 추출한다

   > *region of interest*	객체의 위치
   >
   > *region proposal*	객체의 위치를 찾는 일
   >
   > *region classification*	객체를 분류하는 일
   >
   > *selective search algorithm*	segementation 분야에 많이 쓰이는 알고리즘이며, 객체와 주변간의 색감(color), 질감(texture) 차이, 다른 객체에 애워쌓여있는지(enclosed) 여부 등을 파악해서 다양한 전략으로 물체의 위치를 파악할 수 있도록 하는 알고리즘
   >
   > cf) sliding window
   >
   > 

![Architecture of R-CNN](https://lilianweng.github.io/lil-log/assets/images/RCNN.png)

---

### Model Workflow

R-CNN의 동작 요약

1. 이미지를 분류하는 일을 하기위해 CNN network를 미리 학습한다. 
   - 예를 들어, VGG나 ResNet은 ImageNet 데이터셋에 대해서 학습되었다.
   - 분류하는 일은 N개의 클래스를 포함한다
2. selective search를 통해 카테고리 독립적인 관심 지역을 제안한다 (이미지당 2k개의 후보)
   - 그 지역들은 목표 객체를 포함하고 있을 것이고 다른 크기를 가질 것이다.
3. 지역 후보들은 CNN에서 요구하는 고정된 크기로 변환한다
4. K+1개의 클래스의 변환된 제안 지역에 대한 CNN을 계속해서 fine-tunning한다 
   - 추가적인 하나의 클래스는 배경을 의미한다. (관심의 대상이 아님)
   - fine-tune 단계에서, 훨씬 더 작은 learning rate를 사용해야 하고 mini-batch가 양수를 오버샘플링해야 한다
     - 대부분의 제안된 지역은 배경이기 때문이다.
5. 주어진 모든 이미지 지역에 대하여, 한 번의 forward propagation을 진행한 CNN은 feature vector를 생성한다. feature vector는 각 클래스에 대해 학습된 binary SVM에 의해 독립적으로 사용된다.
   - The positive samples are proposed regions with IoU (intersection over union) overlap threshold >= 0.3, and negative samples are irrelevant others.
6. 지역적 오류를 줄이기 위해서 regression model은 CNN features를 이용한 bounding box 보정 오프셋의 예측 검출 창을 수정하는 것을 학습한다.



---

### Bounding Box Regression	

 ground truth box 좌표 $g = (g_x, g_y, g_w, g_h)$와 일치하는 예측 bounding box 좌표 $p= (p_x, p_y, p_w, p_h)$(중심 좌표, 너비, 높이)가 주어지면, regressor는 두 중심 사이에서의 scale-invariant transformation과 너비와 높이 사이의 log-scale transformation을 학습하도록 구성된다.

> **Ground-truth** : 기상학에서 유래된 용어로 어느 한 장소에서 수집된 정보를 의미
>
> "지상 실측 정보" 인공위성과 같이 지구에서 멀리 떨어져서 지구를 관찰하였을 때 지구의 전체적인 관점을 보는 것에는 넓은 시야를 가질 수 있지만 실제 지면의 구조를 세밀하게 보는 것은 빛이 구름이나 대기를 통과하게 되면서 실제 모습이 왜곡되어 제대로 파악하는 것은 어렵다. 이러한 상황에서 지상 정보를 직접 측정한다면 보다 정확한 정보를 얻을 수 있다. 
>
> 기계학습의 관점에서 보았을 때 ground-truth는 학습하고자 하는 데이터의 원본 혹은 실제 값을 표현할 때 사용된다. 
>
> 객체를 잘 감지한 딥러닝 모델이라면 ground-truth와 가장 가까운 bounding-box를 표현한 예측값이 좋은 성능을 가진 딥러닝 모델이라고 할 수 있다. 수치로서 비교하기 위해 IoU(Intersection over Union) 공식을 사용. IoU 공식은 ground-truth의 bounding-box와 predicted bounding-box가 겹치는 부분의 너비에 두 bounding-box가 사진에서 차지하는 너비를 나눈 값이다.
>
> ![img](https://blog.kakaocdn.net/dn/cgll5K/btqCgReUjoQ/esNXDDSkbnUgheY5AT2U2k/img.png)

> **scale-invariant transformation**
>
> SIFT(Scale Invariant Feature Transform)
>
> : 이미지에서 지역적 특징을 검출하고 묘사하는 컴퓨터 비전에서 사용하는 feature 검출 알고리즘이다. 
>
> - 객체의 SIFT 핵심은 참조 이미지들에서 먼저 추출하고 데이터베이스에 저장하는 것이다.
> - 객체는 새로운 이미지에서 이 데이터베이스까지의 각 feature를 개별로 비교하고 feature vectors의 유클리드 거리를 기반으로 한 feature와 맞는 후보를 찾음으로써 새로운 이미지로 인식된다. 
>
> - 이미지와 크기와 회전에 불변하는 특징을 추출하는 알고리즘
> - 스케일이 축소된 일련의 이미지들(이미지 피라미드)에서 동일하게 검출되는 Scale Invariant한 Feature 추출
> - 코너성을 측정하기 위해서는 DoG(Differnece of Gaussian)을 이용하여 스케일별 Laplacian(파플라시안 : n차원 실수 공간의 직교 좌표에 대한 미분연산자, 라플라스 연산자라고도 한다 기호 $\Delta$로 표시한다.) 계산
> - 특징점이 풍부한 경우에 유리

> **log-scale transformation**

모든 변환 함수는 입력으로 $p$를 갖는다.

- $\hat{g_x} = p_wd_x(p)+p_x$

- $\hat{g_y}=p_hd_y(p)+p_y$

- $\hat{g_w} = p_w exp(d_w(p))$

- $\hat{g_h} = p_h exp(d_h(p))$

- ![bbox regression](https://lilianweng.github.io/lil-log/assets/images/RCNN-bbox-regression.png)

  - predicted bounding box와 ground truth bounding box 사이의 transformation

  이러한 변환을 적용하는 명백한 장점은 모든 bounding box 정정 함수이다. $i \; \in \{x,y,w,h\}$인  $d_i(p)$은 $[-\infin, \; +\infin]$의 어떠한 값을 가질 수 있다.

  여기서 배워야 할 점

  - $t_x = (g_x - p_x) / p_w$
  - $t_y = (g_y - p_y) / p_h$
  - $t_w = log(g_w / p_w)$
  - $t_h = log(g_h / p_h)$

표준 regression model은 정규화항이 있는 SSE loss를 최소화함으로써 문제를 해결할 수 있다.

​				$L_{reg} = \sum\limits_{i\in\{x,y,s,h\}}(t_i-d_i(p))^2+\lambda||w||^2$

이 식에서 정규화 항은 중요하다. R-CNN 논문은 cross validation을 통해 가장 좋은 $\lambda$를 선택했다. 또한 모든 predicted bounding boxes가 ground truth boxes와 일치하지 않는다는 것은 주목할 만한 점이다. 예를 들어, 둘의 접점이 없다면 bounding box regression을 분석하는 것은 의미가 없다. 여기, 최소 0.6 IoU를 갖는 ground truth box와 가까운 predicted box만이 bounding box regression model 학습을 위해 유지된다.



---

### Common Tricks

R-CNN과 다른 인식 모델에서 흔히 사용되는 몇몇 개의 트릭들이 있다.

*Non-Maximum Suppression*

같은 객체에서 여러 개의 bounding boxes를 찾을 수 있을 법하다. Non-max suppression은 같은 객체의 반복되는 인식을 피하는 것을 돕는다. 같은 객체의 카테고리와 일치하는 bounding boxes의 집합을 얻고 난 후에 confidence score로 모든 bounding boxes를 정렬한다. 낮은 confidence scores의 boxes는 삭제한다. 남아있는 bounding box가 있는 동안, 가장 높은 점수의 박스를 하나 선택하는 것을 반복한다. 이전에 선택한 박스이면서 높은 IoU(> 0.5)를 가진 남아있는 박스들은 스킵한다.

![Non-max suppression](https://lilianweng.github.io/lil-log/assets/images/non-max-suppression.png)

- 이미지에서 여러 개의 bounding boxes가 차를 인식한다. non-maximum suppression을 하고 난 후, 가장 좋은 박스만 남았다. 그리고 선택된 하나의 박스와 함께 다른 박스들은 크게 겹치기 때문에 나머지는 무시되었다. 

*Hard Negative Mining*

bounding boxes는 객체가 부정적인 examples라는 것을 제외하고 있다고 고려한다. 모든 부정적인 examples가 똑같이 식별하기 어려운 것은 아니다. 예를 들어, 만약 부정적인 examples가 순수하게 비어있는 배경이라면, 그것은 "easy negative"라고 할 수 있다. 그러나 만약 박스가 이상한 노이즈 텍스쳐나 부분 객체를 포함한다면 객체를 인식하기 힘들 것이고 그것을 "hard negative"라고 한다.

hard negative examples는 쉽게 분류되지 않는 것이라고 할 수 있다. 학습 루프 동안에 명백하게 false positive samples를 찾을 수 있고 분류 기능을 향상하기 위해서 training data에 포함시킬 수 있다. 



---

### Speed Bottleneck

R-CNN 학습 단계

​	R-CNN model 학습하는 것은 비싸고 느림을 알 수 있다. 

1. 모든 이미지에 대해서 2000 지역 후보를 제안하기 위해서 selective search를 실행한다.

2. 모든 이미지 지역에 대해서 CNN feature vector를 생성한다. (N개의 이미지 * 2000)

3. 모든 프로세스는 공유하지 않은 세 가지의 모델을 따로 포함한다. 

   1. 이미지 분류와 feature 추출을 위한 convolution neural network
   2. 타겟 객체를 식별하기 위한 top SVM 분류기
   3. 지역 bounding boxes를 더 좁히기 위한 regression model

   

