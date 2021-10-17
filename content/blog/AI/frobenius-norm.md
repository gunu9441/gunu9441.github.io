---
title: Frobenius Norm
date: 2021-10-17 19:10:74
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

이번 시간에는 Regularization(정규화)의 하나의 기법인 **Frobenius Norm**에 대해서 알아보도록 하겠습니다!🙌 기본적으로 Regularization(정규화)은 **overfitting을 막기 위해 진행**됩니다. **Overfitting**이란 모델이 불필요하게 **training dataset의 noise까지 학습**하여 **새로운 데이터 셋**(test or validation set)**에 대해 좋은 성능을 발휘하지 못하는 현상**을 말합니다. Overfitting에 대해 자세한 설명이 필요하신 분은 (여기)[]를 참고하셔도 좋습니다! 아래는 이번 포스팅에 대한 Category입니다.

## Category

1. Basic Cost Function
2. L1 Regularization & L2 Regularization
3. Frobenius Norm
4. Weight Decay
5. The Principle of Regularization
6. Conclusion
7. Summary
8. Reference

## Basic Cost Function

Deep learning에서는 cost function을 정의하여 label과 prediction사이의 차이가 얼마나 나는지 측정을 하게 되는데 이를 아래와 같은 수식으로 표현합니다.

$$
Loss={1\over m}\sum_{i=1}^m L(\hat y^{(i)}, y^{(i)})
$$

위의 수식을 통해서 알 수 있듯이 각각의 input data에 대한 model의 prediction 값과 label간의 Loss를 측정하여 다 더해서 평균을 취한 것이 $Loss$인 것을 알 수 있습니다. 이 Loss function에 L1 norm과 L2 norm을 추가시켜 regularization을 구현할 수 있습니다.

## L1 Regularization & L2 Regularization

L1 Regularization과 L2 Regularization을 알아보기 전에 **"norm"**이란 무엇일까요? 선형대수에서의 norm은 **vector space상의 vector의 크기** 또는 **길이를 측정할 때 사용되는 개념**입니다. norm에 대한 일반적인 식은 아래와 같습니다.

$$
||x||_p=(\sum_{i=1}^n |x_i^p|)^{1\over p}
$$

위의 수식을 보게 되면 **절댓값을 씌워주고 더하므로 무조건 양수** 값이 나오게됩니다. 이에 길이나 크기를 측정하는데 사용할 수 있습니다. norm을 regularization에 사용하는데 **Logistic Regression에서 L2 norm(유클리드 노름)**을 사용한 **L2 regularization의 수식**부터 보도록 하겠습니다.

$$
{\lambda \over 2m}||w||_2^2
$$

위의 수식은 **기존의 Loss function에 L2 norm의 제곱을 더해준** 형태입니다. $||w||_2^2$의 수식은 아래와 같습니다.

$$
||w||_2^2=((\sum_{i=1}^n |w_i^2|)^{1\over 2})^2 = \sum_{i=1}^n |w_i^2|=w^Tw
$$

위의 수식을 보게되면 $||w||_2^2$은 **L2 norm의 제곱**을 의미하며 이것은 결국 **모든 가중치의 제곱합**이라는 것을 알 수 있습니다. 우리는 이것을 **L2 Regularization**이라고 부릅니다. L2 Regularization은 **가장 흔한 regularization 기법**입니다.

L1 Regularization은 **L1 norm을 사용해서 구현**하게 되며 아래의 수식에서 볼 수 있습니다.

$$
{\lambda \over 2m} ||w||_1={\lambda \over 2m}{\sum_{i=1}^n}|w_i|
$$

하지만 **L1 Regularization을 사용**하게 되면 **0이 되는 weight가 많아지게 됩니다**. 어떤 사람들은 이 효과가 **모델을 압축시키는데 도움이 된다**고 말을 합니다. 왜냐하면 **몇몇의 parameter(weight)가 0**가 되기 때문에 **저장하는데 메모리가 적게 필요하기 때문**입니다. 하지만 모델을 sparse(희소)하게 만들기 위해서는 **L1 regularization이 도움이 되지 않기 때문**에 **모델을 압축하겠다는 목표가 있지 않는 이상 L1 regularization을 잘 사용하지 않는다**고합니다.

이에 네트워크를 훈련 시킬 때는 **L2 Regularization을 훨씬 더 많이 사용**합니다. 이 L2 Regulairzation을 사용할 때 $\lambda$라는 hyperparameter를 설정해주어야 합니다.

## Frobenius Regularization

Neural network에서 **frobenius regularization을 사용**하게 됩니다.

$$
Loss(w^{[i]},b^{[i]},...,w^{[L]},b^{[L]})={1 \over m}\sum_{i=1}^{m}L(\hat y^{(i)}, y^{(i)})+{\lambda \over 2m}\sum_{l=1}^L||w^{[l]}||_F^2
$$

위의 수식은 Frobenius norm을 통하여 regularization term을 추가시킨 cost function이며 Frobenius norm의 제곱을 풀어서 적게되면 아래와 같습니다.

$$
||w^{[l]}||^2_F=\sum_{i=1}^{n^{[l-1]}}\sum_{j=1}^{n^{[l]}}(w_{ij}^{[l]})^2,~~~~~w:(n^{[l-1]},n^{[l]})
$$

위에 나온 행렬의 norm(노름)을 우리는 **frobenius norm**이라고 부릅니다. 선형 대수학에서는 관례에 따라 **행렬의 L2 norm이라고 부르지 않고 frobenius norm**이라고 부릅니다. **Frobenius norm**과 위에서 배웠던 벡터의 **L2 norm**은 **element들을 전부 제곱시켜 더하기 때문에** 동작이 같다고 말할 수 있습니다. 단지 제곱시키고 더하는 **대상이 frobenius norm은 행렬**, **L2 norm은 벡터**라는 차이만 존재합니다. 참고로 $w$의 shape는 (이전 레이어의 뉴런 갯수, 현재 레이어의 뉴런 갯수)이므로 $(n^{[l-1]},n^{[l]})$입니다.

이제 frobenius norm을 사용하여 regularization을 적용한 cost function을 다시 살펴보도록 하겠습니다.

$$
Loss(w^{[i]},b^{[i]},...,w^{[L]},b^{[L]})={1 \over m}\sum_{i=1}^{m}L(\hat y^{(i)}, y^{(i)})+{\lambda \over 2m}\sum_{l=1}^L||w^{[l]}||^2
$$

위의 cost function을 $w^{[l]}$-에 대해 편미분하여 gradient descent를 사용하게되면 아래와 같이 update되게 됩니다.

$$
w^{[l]}=w^{[l]}-\alpha * {\vartheta Loss \over \vartheta w^{[l]}} = w^{[l]}-\alpha *(gradient_{origin}+{\lambda \over m}w^{[l]})
$$

기존의 vanilla gradient descent는 $w^{[l]}=w^{[l]}-\alpha *(gradient_{origin})$입니다. 하지만 위의 수식을 보게되면 cost function에 frobenius norm의 제곱이 추가함에 따라 기존의 gradient($gradient_{origin}$)에 ${\lambda \over m}w^{[l]}$이 더해져서 $w^{[l]}$이 update되는 것을 확인할 수 있습니다.

## Weight Decay

우리가 흔히 부르는 L2 regularization은 사실 frobenius regularization입니다. Frobenius regularization은 다른 말로 **weight decay**라고 부르게 됩니다.

$$
w^{[l]}=w^{[l]}-\alpha * {\vartheta Loss \over \vartheta w^{[l]}} = w^{[l]}-\alpha *(gradient_{origin}+{\lambda \over m}w^{[l]})
$$

$$
= w^{[l]}-\alpha *gradient_{origin} - \alpha * {\lambda \over m}w^{[l]} = (1-{\alpha\lambda \over m})w^{[l]}-\alpha *gradient_{origin}
$$

위의 수식을 보게 되면 $(1-{\alpha\lambda \over m})w^{[l]}$를 볼 수 있습니다. $\alpha *gradient_{origin}$을 빼주기 전에 원래의 $w^{[l]}$을 감소시켰다고 볼 수 있습니다. 이에 처음부터$(1-{\alpha\lambda \over m})$(→1보다 작은 값을 곱해주게 되면 원래 값보다 당연히 작아지겠죠?!😊)을 통해 $w^{[l]}$을 감소시키므로 **L2 norm regularization과 frobenius norm regularization**을 **weight decay**라고 부른다고 합니다.

그렇다면 어떻게 L2 norm regularization과 frobenius norm regularization이 overfitting을 막을 수 있을까요??

## The Principle of Regularization

frobenius norm regularization을 통한 $w^{[l]}$을 update하는 수식을 다시 살펴봅시다.

$$
(1-{\alpha\lambda \over m})w^{[l]}-\alpha *gradient_{origin}
$$

위의 식을 보게 되면 $\lambda$가 커지면 커질 수록 $w^{[l]}$을 **0에 가깝게 설정**할 수 있다는 것을 알 수 있습니다. 많은 $w$들이 0에 가깝게 설정되면 어떤 일이 발생할까요?

당연히 $w^{[l]}$이 **0에 가까워지면** hidden layer안에 존재하는 node($w$가 시작되는 node)의 **영향력이 줄어들게 될 것**입니다. hidden layer안에 **다양한 node들의 영향력이 줄어들기 때문**에 보이는 것보다는 **더 작고 간단한 신경망**이 됩니다. 이에 overfitting될 수 있는 deep model을 **더 간단하게** 만들어주어 **overfitting이 되지 않을 수 있습**니다.

하지만 여기서 중요한점은 레이어 안에 있는 **node들을 완전히 0으로 만드는 것이 아니라** **0에 가까운 수로 만듬**으로써 **모든 node를 사용**하지만 **각각의 영향력을 줄이는 것을 목적**으로 한다고 생각해야 합니다.

하지만 너무 큰 $\lambda$를 사용하게 되면 학습할 수록 **많은 node가 0에 가까워져** 제대로 작동하지 않아 **underfitting(high bias)**될 수 있으니 적절한 $\lambda$값을 갖는 것이 중요합니다.

## Summary

1. $||w||_2^2=L2~norm^2=\sum_{i=1}^n|w_i|^2$
2. Frobenius norm은 관례상의 이유로 행렬의 L2 norm이라고 불리진 않습니다. Frobenius norm 자체가 행렬의 원소 제곱 합을 의미합니다. L2 norm은 당연히 벡터의 원소 제곱 합을 의미하겠죠?!😊
3. Frobenius norm regulairzation 에서 $\lambda$값을 크게 가져가면 $w$가 0에 가깝게 줄이므로써 좀 더 간단한 네트워크를 만들게 됩니다.

## Reference

- what is norm?: [https://www.youtube.com/watch?v=yoD5tQ1HQRU](https://www.youtube.com/watch?v=yoD5tQ1HQRU)
- Regularization(C2W1L04)
- Is it fine to use L2 regularization and dropout?\_1: [https://stats.stackexchange.com/questions/241001/deep-learning-use-l2-and-dropout-regularization-simultaneously](https://stats.stackexchange.com/questions/241001/deep-learning-use-l2-and-dropout-regularization-simultaneously)
- Is it fine to use L2 regularization and dropout?\_2: [https://www.quora.com/Is-using-dropout-and-L2-regularization-on-each-layer-of-a-neural-network-overkill-or-good-practice](https://www.quora.com/Is-using-dropout-and-L2-regularization-on-each-layer-of-a-neural-network-overkill-or-good-practice)
