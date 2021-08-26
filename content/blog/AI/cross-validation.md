---
title: Cross Validation
date: 2021-08-26 23:08:86
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 cross validation에 대해서 알아보도록 하겠습니다!✨ cross validation은 평상시 데이터가 부족할 때 사용한다고만 알고 있었는데, 이번에 제대로 알아보고자 공부해보았습니다😎

## What is Cross validation?

&nbsp; &nbsp; 보통 큰 데이터 셋이 있다면 실제 모델을 평가하기 위해서 데이터를 훈련용, 검증용, 테스트용 이렇게 세 가지로 분리하는 것이 일반적입니다. 훈련용 데이터로는 **데이터 훈련**을, 검증용 데이터로는 **모델의 성능을 조절**하게 됩니다. 모델이 **과적합이 되고 있는지 판단**하거나 **훈련용 데이터로 훈련을 시킨 모델의 정확도**를 **검증용 데이터를 사용하여 검증**하면서 **하이퍼 파라미터를 조정**(**hyper parameter tuning**)을 하게 됩니다.

&nbsp; &nbsp; 이런 과정이 끝나게 된다면 **검증용 데이터에 대해서 일정 부분 최적화가 진행**되었기 때문에 **검증용 데이터로 모델을 평가하는 것이 적합하지 않습니다**. 이에 **테스트 데이터를 통해** 모델의 진짜 **성능을 평가**하게 됩니다.

평상시 대회에서는, test data의 label은 모르는 상태에서 data의 label을 맞춰야 하므로 주어진 **training dataset을 training set과 validation set을 나눠야**합니다. 하지만 _training dataset이 작아서 traning set과 validation set으로 나누지 못하는 경우_ **cross validation을 사용**합니다. 오늘 알아볼 cross validation에는 **K-Fold cross validation**, **Startified-K-Fold cross validation**이 있습니다.

## K-Fold cross validation

&nbsp; &nbsp; **K-Fold cross validation**은 전체 데이터 셋을 **training set과 test set**으로 나눈 뒤, **training set을 다시 training set과 validation set**으로 나누는데 이 때, **k개의 fold**로 나누게 됩니다. **k개 의 fold가 k번 동안 차례대로 validation set이 되고 나머지 fold는 training set**이 됩니다. 아래의 그림은 방금한 설명을 도식화 한 것입니다.

<p align="center">
    < How to implement cross validation? >
</p>
<p align="center">
   <img src="assets\2021-08-26\1.png"/>
</p>

&nbsp; &nbsp; 해당 그림을 보게되면 **ALL Data(모든 데이터)를 Training data와 Test data**로 나눈 것을 볼 수 있습니다. 이후 **5-Fold cross validation을 사용**했는데 **5-Fold 이기 때문에 5번 동안 5개의 Fold가 한번씩 validation set이 되며 진행**됩니다(나머지 **4개의 fold는 학습데이터**가 됩니다.). 이렇게 학습하면서 검증을 하게 되며 학습이 끝난 이후에 **Test data를 통해 마지막 evaluation**을 거치게 됩니다.

## Code

&nbsp; &nbsp; **사이킷런을 사용**하면 **K-Fold cross validation을 손쉽게 구현**할 수 있습니다. 아래의 코드와 같이 사용할 수 있습니다.

```python
from sklearn.model_selection import KFold

X=np.array([
    [ 1, 2, 3, 4],
    [11,12,13,14],
    [21,22,23,24],
    [31,32,33,34],
    [41,42,43,44],
    [51,52,53,54],
    [61,62,63,64],
    [71,72,73,74]
])

y=np.array([0,0,0,0,0,0,0,0])

splits = KFold(n_splits=5, random_state=0, shuffle=False)

for fold, (train_idx,val_idx) in enumerate(splits.split(np.arange(len(X)))):
	X_train, X_validate = X[train_idx], X[val_idx]
  y_train, y_validate = y[train_idx], y[val_idx]
```

&nbsp; &nbsp; `X`라는 독립 변수이며, `y`는 종속변수 입니다. `X`와 `y`를 `n_splits`개의 fold로 나누어 `n_splits`번 만큼 training과 validation이 진행 됩니다. 그림과 같은 방법으로 K-Fold를 구현할 수 있습니다.

&nbsp; &nbsp; K-Fold cross validation의 기본 코드을 살펴보았으니 K-Fold cross validation을 실제로 pytorch에서 어떤 구조로 구현되는지 살펴 보겠습니다.

```python
# k-fold
for fold, (train_ids, test_ids) in enumerate(kfold.split(dataset)):
# epoch
	for epoch in range(0, num_epochs):
# trainloader
		for i, data in enumerate(trainloader, 0):
```

&nbsp; &nbsp; `kfold`를 먼저 사용하여 첫 for문을 돌린 이후, 해당 `kfold`를 통해 정해진 training dataset을 통해 epoch를 돌립니다. 또한, dataloader를 사용하여 정해진 batch-size대로 data를 받아와서 정해진 epoch 만큼 k번 돌아가게 됩니다. 1개의 fold 당 `num_epochs`만큼 돌기 때문에 전체 epoch는 `num_epochs`\* `fold`가 됩니다.

&nbsp; &nbsp; 아래는 classification task에 cross validation을 적용한 code입니다.

link: [https://github.com/gunu9441/AI/blob/main/7.CNN/Deep_CNN_MNIST_using_cross_validation.ipynb](https://github.com/gunu9441/AI/blob/main/7.CNN/Deep_CNN_MNIST_using_cross_validation.ipynb)

다음은 `SubsetRandomSampler`를 이해하고자 pytorch documentation에 있는 source를 가져온 것입니다. 아래의 코드에 대해 살펴보겠습니다.

```python
[docs]class SubsetRandomSampler(Sampler[int]):
    r"""Samples elements randomly from a given list of indices, without replacement.

    Args:
        indices (sequence): a sequence of indices
        generator (Generator): Generator used in sampling.
    """
    indices: Sequence[int]

    def __init__(self, indices: Sequence[int], generator=None) -> None:
        self.indices = indices
        self.generator = generator

    def __iter__(self) -> Iterator[int]:
        return (self.indices[i] for i in torch.randperm(len(self.indices), generator=self.generator))

    def __len__(self) -> int:
        return len(self.indices)
```

&nbsp; &nbsp; `indices` parameter에 a set of `index`가 들어 가므로 `__iter__`에 `len(self.indices)`에 a set of `index`의 갯수가 들어가게 됩니다. 이후, `torch.randperm()`의 parameter로 들어가게 되어 `len(indices)` 크기의 배열에 `len(indices)`미만의 숫자들이 중복 없이 정렬되게 됩니다. 이를 통해 0번째 index부터 `len(indices)-1`까지 차례대로 for문을 통해 가져와서 indices안에 있는 값들을 뽑아서 return하게 됩니다. 즉, random하게 a set of `index`안에 있는 `index`들을 뽑아내기 위해 `SubsetRandomSampler`를 사용하는 것입니다. 이 `SubsetRandomSampler` 덕분에 `torch.utils.data.Dataloader`를 사용할 때 `shuffle = True`**를 사용하지 않아도** 됩니다.

## Startified-K-Fold cross validation

&nbsp; &nbsp; **Startified-K-Fold cross validation**은 **하나의 fold안**으로 들어갈 **label들의 종류가 골고루** 들어갈 수 있도록 조정해주는 역할까지 수행합니다. `StartifiedKFold`의 사용방법은 아래와 같습니다.

```python
import numpy as np
from sklearn.model_selection import StratifiedKFold
from sklearn.model_selection import KFold

X=np.array([
    [ 1, 2, 3, 4],
    [11,12,13,14],
    [21,22,23,24],
    [31,32,33,34],
    [41,42,43,44],
    [51,52,53,54],
    [61,62,63,64],
    [71,72,73,74]
])

y=np.array([3,1,1,0,2,3,2,0])

stratifiedkfold = StratifiedKFold(n_splits=2,random_state=0,shuffle=False)
print(X.shape, y.shape)

print("\nStratifiedKFold**************")
for train_index, validate_index in stratifiedkfold.split(X,y):
    print("train index:", train_index, "validate index:", validate_index)
    X_train, X_validate = X[train_index], X[validate_index]
    y_train, y_validate = y[train_index], y[validate_index]
    print("train data")
    print(X_train, y_train)
    print("validate data")
    print(X_validate, y_validate)
```

다음의 코드의 결과는 아래와 같습니다.

<p align="center">
    < Result >
</p>
<p align="center">
   <img src="assets\2021-08-26\2.png"/>
</p>

&nbsp; &nbsp; 위의 결과를 보게되면 label 값이 골고루 들어갈 수 있도록 분배 된 것을 볼 수 있습니다.

## Summary

- `torch.utils.data.Dataloader`를 사용해도 모든 데이터를 batch_size로 나눠서 mini_batch로 만들어 사용하므로 **한 epoch안에서 반드시 모든 데이터를 사용**하게 됩니다.
- **k-Fold cross validation**은 **k번 동한 for문**을 돌며 **dataset을 k fold개 만큼 나누게** 됩니다. 또한, **k-1개의 fold를 사용하여 training set**를 만들고 **1개의 fold를 사용하여 validation set**을 만듭니다. **이 과정을 k번 동안 반복**하며 학습과 검증을 k번 진행합니다.
- `SubsetRandomSampler`를 사용하면 `torch.utils.data.Dataloader`의 `shuffle=True`를 사용하지 않아도 됩니다.
- `shuffle = True`를 사용해서 `KFold`를 사용할 경우, k-Fold로 **split하기 전에 먼저 전체 데이터를 한 번(!) shuffle을 수행**합니다. `random_state`는 `random seed와 같은 개념`입니다.

이것으로 이번 포스트를 마무리하겠습니다.

감사합니다😉👍

## Reference

---

- The description of `train_test_split` and `KFold()`: [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=gustn3964&logNo=221431914515](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=gustn3964&logNo=221431914515)
- The code of `StartifiedKFold` and `KFold` [https://swlock.blogspot.com/2019/01/scikit-learn-cross-validation-iterators.html?m=1](https://swlock.blogspot.com/2019/01/scikit-learn-cross-validation-iterators.html?m=1)
- Cross validation(Keras): [https://m.blog.naver.com/nywoo19/221970913783](https://m.blog.naver.com/nywoo19/221970913783)
- Python yield: [https://dojang.io/mod/page/view.php?id=2412](https://dojang.io/mod/page/view.php?id=2412)
- Cross validation sample github: [https://github.com/gucci-j/pytorch-imdb-cv/blob/master/src/load_data.py](https://github.com/gucci-j/pytorch-imdb-cv/blob/master/src/load_data.py)
- Pytorch cross validation: [https://www.machinecurve.com/index.php/2021/02/03/how-to-use-k-fold-cross-validation-with-pytorch/](https://www.machinecurve.com/index.php/2021/02/03/how-to-use-k-fold-cross-validation-with-pytorch/)
- Basic concept of dataset: [https://wikidocs.net/60021](https://wikidocs.net/60021)
- Pytorch K-fold: [https://medium.com/dataseries/k-fold-cross-validation-with-pytorch-and-sklearn-d094aa00105f](https://medium.com/dataseries/k-fold-cross-validation-with-pytorch-and-sklearn-d094aa00105f)
- randperm: [https://bigdatadiary0819.tistory.com/60](https://bigdatadiary0819.tistory.com/60)
- KFold `shuffle=True`: [https://stackoverflow.com/questions/12237127/how-to-use-shuffle-in-kfold-in-scikit-learn](https://stackoverflow.com/questions/12237127/how-to-use-shuffle-in-kfold-in-scikit-learn)
