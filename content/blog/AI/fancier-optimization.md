---
title: Fancier Optimization
date: 2021-08-31 19:08:12
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 **Optimization**에 대해서 알아보겠습니다!✌ Deep learning에서는 forward와 back propagation을 반복하며 학습을 진행하게 됩니다. Loss function을 정의하여 forward해서 나온 결과 값과 label값을 비교하여 Loss가 얼마인지 계산하게 됩니다. 구한 **Loss값을 통해 우리는 수 많은 weight에 대해 편미분** 하고 빼줌으로써 각각의 weight를 update 시키죠! 이 방법은 우리가 평상시에 사용하고 있는 gradient descent 방법입니다. 오늘은 이 gradient descent를 base하는, 더 좋은 optimizer를 알아보도록 하겠습니다. 이번 포스팅에 대한 Category는 다음과 같습니다.

## Category

1. SGD
2. The Problem of SGD
3. Momentum
4. Adagrad
5. RMSprop
6. Adam

## SGD

&nbsp; &nbsp; 우리가 평상시에 사용하는 Gradient Descent는 아래와 같은 수식을 통해 parameter를 update하게 됩니다.

$w_{t+1} = w_t-\alpha \triangledown L(w_t)$

&nbsp; &nbsp; 위의 수식은 basic하고 classic한 SGD의 형태입니다. $Loss$를 $w_t$에 대해서 편미분하고 이를 learning rate에 곱하여 $w_t$에 빼주게되면 update된 $w_{t+1}$이 나오게 됩니다. 하지만 **SGD는 optimizor로써 다양한 문제**를 갖게 되는데 어떤 문제를 갖고, 왜 발생하는지에 대해 알아보도록 하겠습니다.

## The Problem of SGD

&nbsp; &nbsp; SGD는 아래와 같은 문제를 갖게 됩니다.

① **zigzag를 그리며 수렴**하여 **convergence speed가 느립니다**.

② **saddle point**에 빠질 수 있습니다.

③ **local minima**에 빠질 수 있습니다.

&nbsp; &nbsp; 먼저 ①에 대해서 살펴보겠습니다. 우리가 어떤 parameter $W_1$과 $W_2$에 대해서 **최적화를 진행**한다면 $z$축을 Loss라 했을 때 아래와 같은 그래프를 생각해볼 수 있습니다.

<p align="center">
   <img src="assets\2021-08-31\1.png"/>
</p>

&nbsp; &nbsp; 위의 그림에 나와있는 두 개의 parameter인 $\theta_1$과 $\theta_2$를 각각 $W_1$과 $W_2$에 mapping된다고 가정하고 설명하겠습니다. 위의 그림은 **두 개의 parameter** $W_1$과 $W_2$에 대한 $Loss$그래프 입니다. 우리는 이 두 개의 파라미터에 대해서 $Loss$가 **minimum**일 때의 $W_1$과 $W_2$ 값을 알고 싶어합니다. $Loss$가 **minimum이 되는 곳에 가기 위해서 gradient descent을 통해** 천천히 다가가겠죠?😉

&nbsp; &nbsp; 우선, **초기 상태**의 $W_1$과 $W_2$의 값이 아래 그림에 있는 **하얀색 점**이라고 생각해봅시다.

<p align="center">
   <img src="assets\2021-08-31\2.png"/>
</p>

&nbsp; &nbsp; 그렇다면 우리는 저 하얀색 점에서부터 차근차근 내려가서 **Loss가 minimum일 때인** 빨간색 지점까지 gradient descent를 통해서 가야할 겁니다. 우리가 생각하는 **ideal optimization의 방향**은 아래의 그림과 같습니다.

<p align="center">
   <img src="assets\2021-08-31\3.png"/>
</p>

&nbsp; &nbsp; 하얀색 점이 $W_1$과 $W_2$의 초기 값이라면 당연히 gradient descent를 사용해서 위의 그림처럼 **곧바로 수렴**하면 좋겠죠? 하지만 **gradient descent는 실제로 이렇게 동작하지 않습니다**. 왜냐하면 **Loss함수가 각각의 가중치들의 변화에 반응하는 정도(가중치마다 적용되는 gradient)가 다르기 때문**입니다.

&nbsp; &nbsp; **각각의 가중치들은 서로 다른 gradient**를 갖게 됩니다. 당연히 $w_1$~$w_n$(모든 $weight$)가 서로 같은 gradient를 가질 순 없겠죠?? 그 이유는 forward pass할 때, **모든 weight들이 서로 다른 input**($x$값)을 갖게되는데 **back propagation을 진행하면서 chain rule에 따라** ${\vartheta (h(x)=wx+b)  \over \vartheta w} = x$가 **곱해지기 때문**입니다. 또한, chain rule에 의해 앞에있는 layer들의 gradient들도 곱해지기 때문에 **모든 weight들의 gradient는 다양한 값**을 갖게 됩니다. 여기서 **weight들 중 높은 gradient를 갖는 weight와 낮은 gradient를 갖는 weight**가 생기게 되는 것입니다. 그렇다면 gradient 값이 많이 차이나는 두 개의 weight를 최적화시켜야 한다면 어떤 일이 일어날까요?

&nbsp; &nbsp; 만약 Loss가 $W_1$보다 $W_2$**의 변동에 더 민감하게 반응**한다면 아래와 같은 현상이 일어나게 됩니다.

<p align="center">
   <img src="assets\2021-08-31\4.png"/>
</p>

&nbsp; &nbsp; 위의 그림을 좀 더 과장되게 그려보면 아래와 같이 나타나게 됩니다.

<p align="center">
   <img src="assets\2021-08-31\5.png"/>
</p>

&nbsp; &nbsp; 수직방향의 weight가 $W_2$, 수평방향의 weight가 $W_1$이라고 해봅시다. 위의 그림을 보게 되면 Loss가 수직 방향의 가중치($W_2$) 변화에 훨씬 더 민감한 것(**gradient가 높은 것**)을 볼 수 있습니다. 가운데 지점(smile 표정)이 Loss함수의 아래로 convex한 부분이라면 3차원의 Loss함수를 생각해봤을 때 $W_2$변화에 대해 **3차원 Loss함수가 너무 민감하게 반응(gradient가 높다)**하고 $W_1$**변화에는 둔감(gradient가 낮다)하기 때문에** **convex부분으로 가는 벡터의 크기가 현저히 달라지게 됩니다**. 이에 **zigzag**를 그리며 optimal한 곳으로 가기 때문에 **convergence speed가 너무 느린 것**을 알 수 있습니다.

&nbsp; &nbsp; 위에서 본 것과 같이 **Loss함수는 한 방향으로는 엄청나게 민감할 수 있고 또 다른 방향으로는 덜 민감**할 수 있습니다. 하지만 위에서 설명했던 것은 2개의 파라미터가 존재할 때이며 실제로는 **parameter가 수천개**가 될 수 있습니다. 이런 문제는 **고차원 공간에서 훨씬 더 빈번하게 발생**하게 됩니다. 만약 가중치가 수천개, 수천억개가 된다면 **이동할 수 있는 방향이 너무나도 많고 이동할 수 있는 방향의 정도가 다양**합니다. 이런 방향 중 **불균형한 방향이 존재**한다면 위와 같이 **더 심한 zigzag**를 그리며 수렴하거나 수렴하지 않을 수 있습니다(대부분 잘 동작하지 않겠죠??😊)

&nbsp; &nbsp; 또한 ②, ③에서 적혀있는 것처럼 **saddle point**와 **local minima**에 빠질 수 있습니다. 아래의 그림은 **saddle point**와 **local minima**를 표현한 그림입니다.

< Local minima >

<p align="center">
   <img src="assets\2021-08-31\6.png"/>
</p>

< Saddle point >

<p align="center">
   <img src="assets\2021-08-31\7.png"/>
</p>

&nbsp; &nbsp; **vanilla SGD**를 위의 상황에 적용한다면 **학습이 진행 되지 않습니다**. 왜냐하면 local minima와 saddle point에서의 **gradient는 0이기 때문**입니다. 이에 우리는 **momentum**이라는 term을 SGD에 대입시켜 위와 같은 상황을 극복할 수 있습니다.

## Momentum

&nbsp; &nbsp; Momentum을 사용한 SGD는 아래의 수식과 같습니다.

$$
v_{t+1} = \rho v_t + \triangledown f(w_t)
$$

$$
w_{t+1}=w_t-\alpha v_{t+1}
$$

&nbsp; &nbsp; **Momentum을 추가한 SGD**는 기존의 $velocity$를 유지합니다. **vanilla SGD는 오로지 gradient 방향**으로만 이동하였는데 **Momentum을 추가한 SGD**(이후부터는 M-SGD로 표기)는 gradient를 계산할 때 velocity를 이용하여 **기존의 방향을 어느정도 유지한 채**로 구하게 됩니다. 즉, 현재 미니 배치의 **gradient 방향만을 고려하는 것이 아닌 이전의 velocity를 같이 고려**하게 되는 것입니다(참고로 mini-batch를 사용할 경우, **noise가 낀 학습데이터를 batch_size로 학습**하기 때문에 살짝씩 튀면서 학습되며 수렴합니다.).

&nbsp; &nbsp; M-SGD는 하이퍼 파라미터 $\rho$(rho)가 **추가적으로 사용**되고 이것은 **momentum을 얼마나 반영**할 것인지, 기존의 velocity를 얼마나 유지할 것인지 결정하게 됩니다. 보통 $\rho$는 **0.9나 0.99와 같은 높은 값**으로 맞춰주게 됩니다. 이렇게 하여 **gradient vector 그대로의 방향이 아닌 velocity vector의 방향도 고려**하여 나아가게 되는 것입니다.

&nbsp; &nbsp; 이 **momentum term을 사용**하므로써 **local minima와 saddle point에서 벗어날 수** 있습니다. 또한, **zigzag로 수렴하는 현상도 완화**시킬 수 있습니다.이전의 velocity를 계속해서 더해줌으로써 **velocity가 증가**하게 되어 **현재의 gradient값이 작아도 velocity를 더해주기 때문에 특정 dimension(weight)에 둔감했던 loss가 빠르게 줄어들게 됩니다**.

&nbsp; &nbsp; 하지만 momentum을 추가한 SGD가 좁고 깊은 global minima를 지나치면 안된다고 생각할 수 있습니다. 하지만 **좁고 깊은 minima는 overfitting을 유발**하게 된다고 합니다. **training set를 더 늘린다면 이런 좁고 깊은 민감한 minima는 사라지게** 되기에 training data의 변화에 **좀 더 강인한 평평한 minima**를 얻길 원합니다. **평평한 minima가 좀 더 일반화**를 잘 할 수도 있으며 testing data에서도 더 좋은 결과를 얻을 수 있을거라고 합니다. 오히려 좁고 깊은 minima를 무시하는 것이 error가 아니라 **momentum의 장점**이 된다는 것입니다.

## AdaGrad

&nbsp; &nbsp; SGD에 momentum term을 추가시키는 것 말고도 **AdaGrad라는 최적화 방법**이 존재합니다. AdaGrad는 M-SGD가 사용하는 momentum term 대신에 **grad squared term을 사용**하게 됩니다. AdaGrad의 구현 코드는 아래와 같습니다.

```python
grad_squared = 0
while True:
	dx = compute_gradient(x)
	grad_squared += dx * dx
	x = x - learning_rate * dx / (np.sqrt(grad_squared) + 1e-7)
```

&nbsp; &nbsp; `grad_squared` term은 학습 중에 계산되는 **gradient에 제곱을 하여 계속 더해**주게 됩니다. weight를 update를 할 때는 앞서 계산한 `grad_squared` term으로 나눠주게 됩니다.

&nbsp; &nbsp; 이 연산을 통해 **gradient가 항상 높은 차원**(weight)은 `grad_squared` term이 크므로 이 값을 나눠주게 되면 **속도가 떨어지게 되고**, **gradient가 항상 낮은 차원**(another weight)은 `grad_squared` term이 작아 이 값을 나눠주게 되면 **속도가 붙게 됩니다**.

&nbsp; &nbsp; 하지만 학습 횟수가 늘어나게 되면 `grad_squared` term이 커지게 되는데 이 값을 나눠주게 되면 **step size가 서서히 작아지게** 됩니다. 이 현상은 **loss함수가 convex**한 경우, **minmum에 근접하게 될 때 서서히 속도를 줄여서 수렴할 수 있기 때문에 좋은 특징**이 됩니다. 하지만 **non-convex 한 경우**에서는 문제가 될 수 있습니다. **saddle point에 걸려 AdaGrad가 멈춰버릴 수 있기 때문**입니다. 이에 AdaGrad의 변형인 **RMSProp**이 등장하게 됩니다.

## RMSProp

&nbsp; &nbsp; RMSProp에서는 AdaGrad의 `grad_squared`를 그대로 사용하게 됩니다. 하지만 조금 변형된 `grad_squared` term을 사용하는데 RMSProp의 코드는 아래와 같습니다.

```python
grad_squared = 0
while True:
	dx = compute_gradient(x)
	grad_squared = decay_rate * grad_squared + (1-decay_rate) * dx * dx
	x = x - learning_rate * dx / (np.sqrt(grad_squared) + 1e-7)
```

&nbsp; &nbsp; 위의 코드를 보게 되면 기존의 AdaGrad의 `grad_squared`와 다르다는 것을 볼 수 있습니다. **기존의 AdaGrad는 무작정 현재의 gradient를 제곱**해서 더했지만 **RMSProp는** `decay_rate`**을 추가**시켜 **지수 가중 이동 평균(Exponential weighted moving average)을 사용**하므로써 **얼마나 이전의 grad_squared 값을 반영**할 건지 결정할 수 있습니다. 또한, **현재 gradient의 제곱값을 (1-decay_rate)만큼 곱**해주어 반영하게 됩니다.

&nbsp; &nbsp; RMSProp에서는 **decay_rate를 보통 0.9나 0.99**를 사용하게 되며 현재 **gradient의 제곱을 계속해서 더하고 나눠준다는 점이 AdaGrad와 유사**합니다. 이를 통해 두 개의 optimizer는 **step의 속도를 감속**시키거나 **가속**시킬 수 있습니다.

&nbsp; &nbsp; 하지만 **RMSProp**은 step size가 점점 줄어드는, 즉 점점 **속도**가 줄어드는 문제를 해결하였습니다.

&nbsp; &nbsp; 지금까지 SGD와 M-SGD, AdaGrad, RMSProp에 대해서 알아보았습니다. Momentum은 $velocity$(momentum)를 **이용해서 step을 조절**하고 AdaGrad와 RMSProp은 `grad squared` term을 나눠주는 방식으로 **step을 조절**해주었습니다. 만약 Momentum에서 사용한 $velocity$의 개념과 RMSProp에서 사용했던 `grad squared` term을 조합하면 어떨까요?😉

## Adam

&nbsp; &nbsp; Momentum과 AdaGrad, RMSprop의 개념을 조합하여 만든 optimization algorithm이 바로 Adam입니다. Adam은 `first_moment`와 `second_moment`를 이용해서 **이전의 정보를 유지**시키는데 Adam의 `first_moment`는 Momentum에, `second_moment`는 AdaGrad와 RMSProp에 대응되는 개념입니다. Adam의 코드는 아래와 같습니다.

```python
first_moment = 0
second_moment = 0
while True:
	dx = compute_gradient(x)
	first_moment = beta1 * first_moment + (1-beta1) * dx
  second_moment = beta2 * second_moment + (1-beta2) * dx * dx
	x = x - learning_rate * first_moment / (np.sqrt(second_moment)+1e-7)
```

&nbsp; &nbsp; `first_moment`부터 살펴보게 되면 **Momentum때와는 다르게 가중합을 사용**하였습니다. Momentum의 식은 아래와 같습니다.

$$
v_{t+1} = \rho v_t + \triangledown f(w_t)
$$

&nbsp; &nbsp; 위의 수식을 보게되면 $velocity$는 exponential weighted moving average를 사용하지 않았는데 `first_moment`에서는 **gradient의 exponential weighted moving average를 사용**한 것을 볼 수 있습니다. 하지만 `first_moment`는 Momentum에서의 $velocity$와 의미적으로 같습니다.

&nbsp; &nbsp; `second_moment`는 AdaGrad와 RMSProp처럼 **gradients의 제곱을 이용**합니다. AdaGrad와 RMSProp와 같이 **sqrt(second_moment)를 나눠주게 되며 똑같은 역할**을 하게 됩니다(**큰 값을 갖게 되면 작은 step**으로, **작은 값을 갖게 되면 큰 step**으로 학습 진행).

&nbsp; &nbsp; 이에 Adam의 형태를 **momentum + grad squared term으로 구성**되어 있는 것으로 볼 수 있습니다. 하지만 현재의 Adam은 문제가 있습니다.

&nbsp; &nbsp; 초기에 `second_moment`**를 0으로 초기화**하여 epoch를 돌때마다 `second_moment`는 update를 거치게 됩니다. 하지만 **beta2**는 `decay_rate`로 0.9나 0.99의 값을 가지기 때문에 `second_moment`**는 0에 가까운 값을 초기 상태**에서 갖게됩니다. weight를 update 해줄 때 `second_moment`로 나누기 때문에 **초기의 step이 엄청나게 커지게** 됩니다. 초기의 step이 엄청나게 커진 것은 손실 함수자체가 가파르기 때문이 아니라 `second moment`를 0으로 초기화 시켜 발생한 **인공적인 현상이기에 문제**가됩니다.

&nbsp; &nbsp; `first_moment` 또한 작아서 상쇄될거라 생각할 수 도있지만 **상쇄되지 않고 초반 step이 엄청나게 커져 엉뚱한 곳으로 이동**하여 잘못될 수 도 있습니다. 이에 문제를 해결하고자 **보정항**(bias correction term)을 따로 추가하게 됩니다.

&nbsp; &nbsp; bias correction term을 추가한 Adam의 코드는 아래와 같습니다.

```python
first_moment = 0
second_moment = 0
for t in range(num_iterations):
	dx = compute_gradient(x)
	# Momentum
	first_moment = beta1 * first_moment + (1-beta1) * dx

	# AdaGrad, RMSProp
    second_moment = beta2 * second_moment + (1-beta2) * dx * dx

	# Bias correction
	first_unbias = first_moment / (1 - beta1 ** t)
	second_unbias = second_moment / (1 - beta2 ** t)

	# AdaGrad, RMSProp
	x = x - learning_rate * first_moment / (np.sqrt(second_moment)+1e-7)
```

&nbsp; &nbsp; `first_moments`와 `second_moments`를 update하고 난 후 현재 step에 적절한 bias correction term(first_unbias와 second_unbias)을 계산합니다. 이 bias correction term을 통해 초기 step에서 기존 Adam의 작은 `second_moment`로 나눠줄 때 **step이 엄청 커지는 문제를 해결**할 수 있습니다. `second_unbias`에 있는 `beta2 ** t`는 **초기 step에서 값이 0.9나 0.99**가 됩니다. 이 값을 1에 빼게 된다면 **0.1이나 0.01이 나오게 되는데** 이 값으로 `second_moment`를 나눠줌으로써 `second_moment`의 값이 **10배, 100배로 늘어나며 기존의 문제를 해결**할 수 있습니다. 초기 step에서 기존 Adam의 `second_moment`값이 작은게 문제였기 때문에 **이 값을 10배, 100배로 늘려줘서 보정을 한 것**입니다. `first_moment` 또한 **기존 Adam에서의 초기 step에서 매우 작은 값**을 가지고 있었지만 **늘려주게 되면서 보정**됩니다.

&nbsp; &nbsp; Adam은 **다양한 문제들에도 잘 작동**하여 모든 문제에서 **기본 알고리즘으로 Adam을 사용**한다고 합니다. 실제로 Adam은 `beta1`=0.9, `beta2`=0.999, `learning_rate`=1e-3 or 5e-4(1e-4~1e-3)가 **많은 모델의 첫 시작 포인트**로 좋습니다.

## Compare

<p align="center">
   <img src="assets\2021-08-31\8.png"/>
</p>

&nbsp; &nbsp; 위의 그림에서 검정색은 SGD, 파란색은 M-SGD, 빨간색은 RMSProp, 보라색은 Adam을 사용했을 때의 결과입니다. Momentum은 $velocity$를 이용해서 **step을 조절**하고 AdaGrad와 RMSProp은 **grad squared term을 나눠주는 방식**으로 step을 조절해줍니다. 이에 momentum의 경우, $velocity$ 덕분에 **overshoot한 이후에 다시 minima로** 돌아오게 됩니다. 하지만 RMSProp의 경우 gradient가 크면 작은 step으로 줄어들고, gradient가 작다면 좀 더 큰 step으로 줄어들기 때문에 **각 차원마다(각 weight마다) 상황에 맞도록 수렴**합니다.

&nbsp; &nbsp; Adam은 **momentum**과 **RMSProp**을 섞은 듯한 모습을 보여줍니다. Adam이 momentum처럼 **overshoot을 하긴 하지만 momentum 만큼 심하게 overshooting되지 않게 됩니다**. 왜냐하면 현재 gradient에 `(1-beta1)`을 곱해서 더해주며(가중합을 말하는겁니다😉) **현재 gradient가 높으면 작은 step**으로 가게끔 보정해주고 **낮으면 큰 step으로 가게끔 보정**(grad_squared & unbiased term)해주기 때문입니다. 이에. Adam은 RMSProp의 특징을 갖고있다고 말할 수 있습니다. 위에서 말했던 것처럼 **overshooting의 정도를 줄여주는데 grad_squared term과 unbiased term이 기여하기 때문**입니다. 이에 **Adam또한 각 차원(수많은 weight들)의 상황을 고려**해서 **step을 이동**한다고 말할 수 있습니다.

&nbsp; &nbsp; 따라서 Adam은 **momentum처럼 동작하는 동시에 RMSProp처럼 동작**합니다.

## Conclusion

&nbsp; &nbsp; 많은 optimization algorithm을 알아보았지만 Adam을 가장 많이 사용한다고 합니다. 지금까지 optimization algorithm들이 **training error를 줄이고 손실함수를 최소화시키기 위해 동작하는 것처럼 설명**했지만 사실 **우리의 목표는 training error를 줄이는 것이 아닙니다**. 우리의 목표는 한번도 보지 못한 데이터, 즉 **test(validation) dataset에 대한 성능을 끌어올리는 것이 목표**입니다. 이에 training error를 줄이는 것도 좋지만 우리가 정말 하고자 하는 것은 **test dataset에 대한 error를 줄이는 것**입니다. 이에 **모델이 학습될 수록 떨어지는 training error**와 **한번도 보지못한 데이터인 test datset의 error의 격차를 줄이는 것이 중요**합니다. 그렇다면 우리가 Loss에 대해 최적화를 모두 마친 상태에서 한번도 보지 못한 데이터인 test dataset에서의 성능을 올리기 위해서는 어떻게 해야할까요?

&nbsp; &nbsp; 첫번째 방법은 **Ensemble** 입니다. **여러개의 모델을 독립적으로 학습**시켜 **모델들의 결과의 평균**을 이용하는 방법입니다. **모델이 늘어날 수록 overfitting이 줄어들게 되며 성능이 향상**되게 되는데 보통 2%정도 상승하게 된다고 합니다. 그렇다면 단일모델에서의 validation set에 대한 성능을 올리려면 어떻게 해야할까요?

이 방법은 다음 포스팅 때 올리도록 하겠습니다!  
감사합니다😎

## Summary

1. SGD는 **Loss값이 각 parameter($weight$)의 변동에 따라 민감한 정도가 다르기 때문**에 **zigzag**를 그리면서 수렴하므로 convergence speed가 느려질 수 있으며, **saddle point와 local minima**에 빠질 수 있습니다. **고차원 공간(여러개의 weight)에서**는 **zigzag로 수렴**하는 현상, **saddle point, local minima**에 더 잘 빠질 수 있습니다.
2. SGD에 **momentum term**을 추가시키면 기존의 $velocity$를 $\rho$만큼 유지한채로 현재 gradient값을 더하여 update하기 때문에 **saddle point**와 **local minima**에서 빠져나올 수 있습니다.
3. M-SGD의 $\rho$값을 보통 **0.9**나 **0.99**로 설정합니다.
4. **좁고 깊은 minima(=overfitting)를** 원하는 것이 아니라 **평평한 minima**를 찾는 것을 optimizer는 목표로 합니다.
5. AdaGrad는 Neural Network를 학습시킬 때 잘 사용하지 않습니다.
6. RMSProp에서는 **decay_rate**를 보통 **0.9**나 **0.99**를 사용합니다.
7. Momentum은 $velocity$(momentum)를 이용해서 **step을 조절**하고 AdaGrad와 RMSProp은 **grad squared term**을 나눠주는 방식으로 **step을 조절**한다.
8. 초기 `seoncd_moment`가 너무 작아 **step이 너무 커지기 때문에 발산**할 수 있습니다. 이에 Adam에서는 **보정항**(bias correction term)을 사용합니다.
9. Adam은 `beta1`=0.9, `beta2`=0.999, learning_rate=1e-3 or 5e-4(1e-4~1e-3)가 많은 모델의 첫 시작 포인트로 좋습니다.
10. Optimizer를 사용할 때 Adam을 사용합시다!😎

# Reference

- SGD+Momentum: [https://hyunw.kim/blog/2017/11/01/Optimization.html](https://hyunw.kim/blog/2017/11/01/Optimization.html)
- Difference between SGD and GD: [https://datascience.stackexchange.com/questions/36450/what-is-the-difference-between-gradient-descent-and-stochastic-gradient-descent](https://datascience.stackexchange.com/questions/36450/what-is-the-difference-between-gradient-descent-and-stochastic-gradient-descent)
- GD vs BGD vs MSGD: [https://skyil.tistory.com/68](https://skyil.tistory.com/68)
- cs231n Lecture
