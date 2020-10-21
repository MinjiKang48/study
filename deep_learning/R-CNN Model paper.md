# Rich feature hierarchies for accurate object detection and semantic segmentation

## (R-CNN 논문 - 목적 : 객체 인식)

### 요약

canonical PASCAL VOC 데이터셋으로 측정한 객체 인식 성능은 지난 몇 년부터 유지되고 있다. 제일 좋은 성능을 가진 방법은 보통 여러 개의 높은 수준의 문맥을 갖는 낮은 수준의 이미지 features의 조합인 복잡한 앙상블 시스템이다. 이 논문에서, 간단하고 확장 가능한 알고리즘을 제안한다. 이전 VOC 2012에서 가장 좋은 결과에 비해 평균 정밀도(mean average precision; mAP) 30%이상이 향상되어 53.3%를 달성하는 알고리즘이다. 

> **mAP(mean Average Precision) for Object Detection**
>
> AP(Average Precision)은 Faster R-CNN, SSD 등 같은 객체 인식의 정확도를 측정하기 위한 인기있는 측정법이다. Average precision은 recall value 0~1 사이의 average precision 값을 계산한다. 복잡하게 들리지만 실제로는 예시 그림을 보면 굉장히 간단하다. 
>
> *Precision*	예측이 얼마나 정확한지 측정한다. 즉, 예측 정확도의 퍼센트이다. 
>
> *Recall*	positive를 얼마나 잘 찾는지를 측정한다. 예를 들어, top K개의 예측에서 가능한 positive cases의 80%를 찾을 수 있다.
>
> ![Image for post](https://miro.medium.com/max/534/1*EXa-_699fntpUoRjZeqAFQ.jpeg)
>
> *IoU(Intersection over union)*	2개의 바운더리 사이에서의 겹쳐짐을 측정한다. 이 측정 방법은 우리가 예측한 바운더리와 실제 객체 바운더리(the ground truth)가 얼마나 겹쳐져 있는지를 측정할 때 사용할 수 있다. 어떠한 데이터셋에서는, 예측이 true positive인지 false positive인지 분류하는 작업을 할 때 IoU 임곗값(0.5라고 하자)을 미리 선언한다.
>
> ![Image for post](https://miro.medium.com/max/800/1*FrmKLxCtkokDC3Yr1wc70w.png)
>
> **AP**
>
> average precision의 계산을 증명하기 위해서 과대평가된 예시를 만들어보자. 예를 들어, 전체 데이터셋은 5개의 사과만을 가지고 있다. 모든 이미지에서 사과를 위해 만들어진 모든 예측을 수집하고 예측 confidence level에 따라 내림차순으로 정렬하여 순위를 매긴다. 두 번째 열은 예측이 맞는지 아닌지를 나타낸다. 이 예시에서, IoU >= 0.5이면 예측이 맞다.
>
> ![Image for post](https://miro.medium.com/max/1668/1*9ordwhXD68cKCGzuJaH2Rg.png)
>
> rank 3을 가지고 precision과 recall을 먼저 계산한다.
>
> Precision은 TP(True Positive)의 비율 = 2/3 = 0.67이다. Recall은 가능한 positives 외의 TP 비율 = 2/5 = 0.4이다.
>
> Recall 값은 예측 랭킹이 내려가면 증가한다. 하지만 precision은 지그재그 패턴을 갖는다; false positives에서는 내려가고 true positives이면 다시 올라간다.
>
> 이러한 지그재그 패턴을 그래프로 그린다.
>
> ![Image for post](https://miro.medium.com/max/1504/1*VenTq4IgxjmIpOXWdFb-jg.png)
>
> Average Precision(AP)의 일반적인 정의는 위의 그래프에서 precision-recall 곡선 아래의 구역을 찾는 것이다.	$AP = \int_0^1p(r)dr$
>
> Precision과 recall은 항상 0과 1 사이다. 그러므로 AP는 0~1에 든다. 객체 인식을 위한 AP를 계산하기 전에 먼저 지그재그 패턴을 매끄럽게 만들 것이다.
>
> ![Image for post](https://miro.medium.com/max/1610/1*zqTL1KW1gwzion9jY8SjHA.png)
>
> 사진으로 보면, 각 recall 레벨에서 각각의 maximum precision값을 가진 precision 값을 recall 값의 오른쪽으로 대체한다.
>
> ![Image for post](https://miro.medium.com/max/1600/1*pmSxeb4EfdGnzT6Xa68GEQ.jpeg)
>
> [mAP_reference](https://medium.com/@jonathan_hui/map-mean-average-precision-for-object-detection-45c121a31173)

이 접근방법은 2가지의 이해로 이루어졌다. 1) 객체를 국한시키고 작게 쪼개기 위해서 세부적인 지역제안에 높은 역량을 가진 CNNs를 적용할 수 있다. 2) labeled training data가 부족할 때, 도메인별 세부적 fine-tune 보조적인 일을 위한 supervised pre-training, 

