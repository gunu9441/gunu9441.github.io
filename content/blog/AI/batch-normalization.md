---
title: Batch Normalization
date: 2021-08-22 19:08:42
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 Batch Normalization에 대해서 알아보도록 하겠습니다!👏 이전 포스트에서 Xavier initialization이나 He initialization이 무엇인지, 어떤 원리를 통해 만들어 졌는지 알아보았습니다. 이에 우리는 **이 2개의 initialization 기법이 각각의 layer에서 나오는 각각의 activation value의 distribution을 같은 개형**으로, **좀 더 가우시안스럽게** 만들기 위해 사용했습니다. 하지만 이렇게 모든 레이어에서 나오는 **activation value들의 distribution을 gaussian처럼 유지**시키는 또 다른 아이디어에는 **Batch Normalization**이라는 기법이 있습니다. 아래는 이번 시간에 배울 Category입니다.

## Category

1. Internal Covariate Shift
2. Learnable Parameters $\gamma$ and $\alpha$
3. Effect of Batch Normalization
4. Batch Normalization operation
5. Code
6. Summary
7. Reference

## Internal Covariate Shift

&nbsp; &nbsp; 우리가 어떤 네트워크에 넣게 되는 input이 정규 분포의 형태를 가졌다고 생각해봅시다. 이 입력이 레이어를 통과할 때마다 아래의 그림처럼 **activation value의 distribution 흐트러지게 됩니다**.

<p align="center">
   <img src="assets\2021-08-23\1.png"/>
</p>

&nbsp; &nbsp; layer가 깊어질 수록 위의 그림처럼 activation value의 distribution이 점점 심하게 흐트러지게 되는데우리는 이 현상을 internal covariate shift라고 부르며 이를 해결하기 위해 batch normalization을 사용합니다. batch normalization을 사용하여 각 레이어의 activation value를 강제적으로 gaussian 분포로 바꿔보자는 것입니다. 즉, forward pass하는 동안 각각의 레이어로부터 나온 **Batch 단위 만큼의 activation들이 가우시안 분포**이길 바라는 것입니다.

&nbsp; &nbsp; Batch Normalization은 **레이어 안에 있는 각각의 뉴런들을(같은 feature끼리)** $\mu$(mean)와 $\sigma$를 통해 **normalization**합니다. 예를 들어 batch 당 $N$개의 학습 데이터를 갖고 있고 각각의 학습 데이터가 $D$차원이라 할 경우, batch당 input의 크기는 $N*D$가됩니다. 아래의 그림의 경우 $N$이 50, $D$는 150이 되며, 레이어에 **_150-dimensional input_**이 들어가는 것 입니다. **데이터의 같은 dimension끼리 normalization을 수행**하기 위해 아래의 그림처럼 같은 feature끼리 $\mu_{n}$와 $\sigma_n$를 구해줍니다. 이후, 구한 $\mu_{n}$와 $\sigma_n$를 통해 **feature 당 normalization을 진행**합니다.

<p align="center">
   <img src="assets\2021-08-23\2.png"/>
</p>
<p align="center">
    150-dimensional input
</p>

&nbsp; &nbsp; Batch normalization layer는 **fully-connected layer 이후나 Conv layer 이후**에 바로 넣어주게 되며 **activation function이 전**에 넣어줍니다. 만약 conv layer를 batch normalization 시킨다면 **batch normalization을 channel 단위로 수행**하여 줍니다. 이에, **activation map 마다 평균과 분산**을 구하여 normalization 시켜주는 것입니다.

## Learnable Parameters $\gamma$ and $\alpha$

&nbsp; &nbsp; 하지만 batch normalization은 단순히 normalization만을 수행하는 것은 아닙니다. 아래의 그림과 같은 수식을 통해 **normalize된 distribution을 sclaing 하거나 shifting**시킬 수 있습니다.

<p align="center">
   <img src="assets\2021-08-23\3.png"/>
</p>

&nbsp; &nbsp; **batch normalization을 통해 gaussian으로 normalize된 값**들을 $\gamma$로 **scaling**을, $\beta$로 **shifting**을 할 수 있습니다. $\gamma$와 $\beta$는 **learnable한 parameter**이며 **normalize된 distribution을 flexible**하게 만들어 줍니다. 이에 normalized 된 값을 다시 원상복구시키고 싶다면 원상복구 할 수 있게 됩니다. $\gamma$를 **분산으로**, $\beta$를 **평균으로** 설정하게 된다면 말이죠~!👍 이렇게 감마와 베타를 사용하면 **saturation을 얼마나 할 것인지 학습**할 수 있기 때문에 학습에 대한 유연성을 얻을 수 있습니다.

## Effect of Batch Normalization

&nbsp; &nbsp; Batch normalization을 사용한다면 전에 설명했던 것 처럼, 각각의 layer를 통과한 output을 **Gaussian스럽게** 바꿀 수 있습니다. **강제적으로 Gaussian distribution에 근사하는 activation value들의 distribution**을 얻음으로써 **각각의 layer를 통과한 activation value들이 서로 비슷한 distribution을** 갖게 됩니다. 물론 모든 분포가 gaussian distribution을 따를 수는 없지만 **gaussian distribution에 근사하도록 normalize**시키게 됩니다. 이에 **internal covariate shift가 없어**지게 되는 것 입니다.

&nbsp; &nbsp; 또한 batch normalization은 **regularization의 역할 또한 수행**합니다. 레이어의 출력이 해당 데이터 뿐만아니라 **batch 안에 존재하는 모든 데이터 들의 영향**을 받게 됩니다. **각각의 레이어에 있는 동일한 feature들끼리 평균과 표준편차를 구하여 함께 normalize 되기 때문**입니다.

## Batch Normalization operation

&nbsp; &nbsp; Batch normlization은 **train할 때와 evaluate할 때 다른 동작**을 합니다. 먼저 train 할 때, 이루어지는 동작을 살펴보겠습니다.

&nbsp; &nbsp; **train을 할 때**, batch normalization은 $\gamma$와 $\beta$를 **backpropagation을 통해 학습**하게 됩니다. 아래의 그림처럼 layer안에 있는 node(feature) 하나당 하나의 $\gamma$와 $\beta$를 가지게 되는데 이는 하나의 node(feature) 당 $\mu$와 $\sigma$를 갖기 때문입니다.. 또한, train 과정에 있어서 normalize하는데 사용되는 $\mu$와 $\sigma$는 **batch 단위로 구해지게 됩니다**.

<p align="center">
   <img src="assets\2021-08-23\4.png"/>
</p>

&nbsp; &nbsp; **evaluation 과정**에서 사용되는 $\mu$와 $\sigma$는 아래의 그림처럼 **train 과정에 구했던** $\mu$와 $\sigma$들의 **각각의 평균 값이 사용**됩니다. **train 과정**에서는 들어오는 batch를 기준으로 $\mu$와 $\sigma$를 구해서 **매 step마다 값이 바뀌었습니다**. 하지만 **evaluation 과정에서는 train에서 구했던** $\mu$와 $\sigma$들 **각각의 평균 값을 사용하므로 고정된 값을 사용**합니다.

<p align="center">
   <img src="assets\2021-08-23\5.png"/>
</p>

&nbsp; &nbsp; evaluation(test) 할 때는 아래와 같은 과정이 진행되게 됩니다.

<p align="center">
   <img src="assets\2021-08-23\6.png"/>
</p>

## Code

&nbsp; &nbsp; batch normalization의 성능을 시험해 보기 위해 **batchnormalization, initialization을 적용한 모델**과 **initialization만을 적용한 모델**을 training 하여 비교해 보았습니다. 처음에 코딩을 했을 때, `model.train()`과 `model.eval()`을 사용하지 않았더니 generator가 아래와 같은 사진을 생성해 냈습니다.

<p align="center">
    < batchnorm과 initialization 적용한 model >
</p>
<p align="center">
   <img src="assets\2021-08-23\7.png"/>
</p>

<p align="center">
    < initialization만 적용한 model >
</p>
<p align="center">
   <img src="assets\2021-08-23\8.png"/>
</p>

&nbsp; &nbsp; batch normalization은 **train할 때와, test할 때의 동작이 달라** 이를 구분하기 위해서 `model.train()`과 `model.eval()`을 사용하게 됩니다. `model.train()`은 현재 모델이 **training과정을 진행 중**이라는 것을 알려주는 것으로 **batch normalize의 학습이 진행**됩니다. batch normalization의 **학습이 진행** 될 때는 $\mu$와 $\sigma$가 **해당 batch에 따라 다른 값**으로 설정되며 $\gamma$와 $\beta$가 **학습**됩니다. 하지만 `model.eval()`은 모델이 **현재 test 과정을 진행**하고 있다고 알려주기 때문에 지금까지 구했던 $\mu$와 $\sigma$들의 **평균을 사용**하여 **고정된** $\mu$와 $\sigma$를 사용합니다. 또한 **지금까지 학습한** $\gamma$와 $\beta$를 **사용**하여 결과를 구해줍니다. 아래의 사진은 위의 두 개의 model의 discriminator loss와 generator loss 입니다.

<p align="center">
    < Discriminator Loss > 
</p>
<p align="center">
   <img src="assets\2021-08-23\9.png"/>
</p>

<p align="center">
    < Generator Loss >
</p>
<p align="center">
   <img src="assets\2021-08-23\10.png"/>
</p>

&nbsp; &nbsp; 위의 결과를 볼 수 있듯이, batch norm을 적용한 model의 generator와 discriminator loss가 비정상적으로 높거나 낮은 것을 볼 수 있습니다. 이처럼 **batch normalization을 사용**할 때는 `model.train()`과 `model.eval()`을 **반드시 사용해야한다는 것을 알 수 있습니다!**

&nbsp; &nbsp; **이에 필연적으로** `model.train()`과 `model.eval()`을 **사용**해야 합니다. 아래의 결과는 `model.train()`과 `model.eval()`을 사용해서 구현한 두 개의 model이 만든 MNIST입니다.

<p align="center">
    < batchnorm과 initialization 적용한 model >
</p>
<p align="center">
   <img src="assets\2021-08-23\13.png"/>
</p><p align="center">
    < initialization만 적용한 model >
</p>
<p align="center">
   <img src="assets\2021-08-23\8.png"/>
</p>

&nbsp; &nbsp; 위의 결과를 보게 되면 두 경우에 다 결과가 잘 나온 것을 알 수 있습니다. **Batchnorm을 적용한 모델**은 batchnorm을 적용하지 않은 모델보다 **하얀색 점들이 덜 찍혀있는 것을 확인**할 수 있습니다. epoch가 경과함에 따라 generator가 내놓은 MNIST data들을 보게되면 **fake image(Generator가 만든 fake MNIST data) 좀 더 선명한 것**을 볼 수 있습니다. epoch에 지남에 따라 generator가 만든 fake image들은 [여기]()를 클릭하시면 볼 수 있습니다.또한 두 모델의 Discriminator Loss와 Generator Loss에도 차이가 존재합니다.

<p align="center">
    < Discriminator Loss > 
</p>
<p align="center">
   <img src="assets\2021-08-23\11.png"/>
</p>

<p align="center">
    < Generator Loss >
</p>
<p align="center">
   <img src="assets\2021-08-23\12.png"/>
</p>
&nbsp; &nbsp; Discriminator는 Generator가 만든 이미지를 진짜 이미지라고 믿도록 학습되기 때문에 Discriminator Loss가 올라가야 Geneartor가 정상적으로 학습이 되고 있는 것입니다. 이것을 생각하며 위의 그래프를 보게되면 batchnorm으로 구현된 모델이 더 높은 Discriminator Loss를 갖고 있으며 차후에도 더 높아질 수 있는 가능성을 갖고 있습니다. 또한 Generator Loss를 보게 되면 batch normalization을 사용한 model이 좀더 Loss가 낮은 것을 볼 수 있습니다. 이에 batch normalization은 GAN generator의 performance를 높여준다는 결론을 얻을 수 있습니다.

&nbsp; &nbsp;batch normalization을 적용한 모델이 구현된 코드는 아래의 사이트에 들어가시면 볼 수 있습니다😎

link: [Two experiements about batchnormalized model](https://github.com/gunu9441/AI/tree/main/8.GAN/Comparing_performance)

## Summary

1. batch normalization은 learnable한 parameter인 $\gamma$와 $\alpha$를 가지며 batch 단위로 들어온 input의 같은 dimension(feature)끼리 $\mu$와 $\sigma$를 구하여 같은 dimension(feature)안에서 normalize합니다.
2. $\gamma$와 $\alpha$를 통해 layer서 나온 값들을 scaling하고 shifting시킬 수 있습니다.
3. Batch normalization은 regularization의 역할 또한 수행합니다.
4. training 과정에서 구했던 $\mu$와 $\sigma$를 활용하여 evaluation step에서 사용할 고정된 $\mu$와 $\sigma$를 구하게 됩니다. training 과정에서 구했던 $\mu$와 $\sigma$들의 평균을 통해 고정된 $\mu$와 $\sigma$를 구합니다.
5. Batch normalization layer를 추가할 때는 `model.train()`과 `model.eval()`을 꼭!!! 사용해야한다.
6. Batch normalization을 적용하게 되면 **성능이 올라간다**!👍

오늘은 batchnormalization에 대해서 알아봤습니다!:)  
긴 글 읽어주셔서 감사합니다😎

## Reference

- Batch Normalization 설명 및 사진: [https://www.youtube.com/watch?v=HCEr5f-LfVE&list=PLQ28Nx3M4JrhkqBVIXg-i5_CVVoS1UzAv&index=17](https://www.youtube.com/watch?v=HCEr5f-LfVE&list=PLQ28Nx3M4JrhkqBVIXg-i5_CVVoS1UzAv&index=17)
- Batch Normalization 설명: csn231n Lecture
