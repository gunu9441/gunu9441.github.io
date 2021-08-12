---
title: Linear Regression Model
date: 2021-08-12 20:08:33
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

## Total code

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim

torch.manual_seed(1)

x_train = torch.FloatTensor([[1],[2],[3]])
y_train = torch.FloatTensor([[2],[4],[6]])

W = torch.zeros(1, requires_grad = True)
b = torch.zeros(1, requires_grad = True)

optimizer = optim.SGD([W,b], lr=0.01)

nb_epochs = 1999

for epoch in range(nb_epochs + 1):
    hypothesis = x_train * W + b
    cost = torch.mean((hypothesis-y_train)**2)

    optimizer.zero_grad()
    cost.backward()
    optimizer.step()

    if epoch % 100 ==0:
        print('Epoch {:4d}/{} W: {:.3f}, b: {:.3f} Cost: {:.6f}'.format(epoch, nb_epochs,W.item(), b.item(), cost.item()))
```

## Structure

1. data 선언
2. model parameter 초기화
3. optimizer
4. hypothesis
5. cost 함수 선언
6. cost 함수로 update

   optimizer.zero_grad() → 가중치 초기화

   cost.backward() → cost함수의 편미분 구하기

   optimizer.step() → 구한 편미분으로 weight를 update

## Numpy 구현

```python
from google.colab import drive
import pandas as pd
import numpy as np

filename = '/content/drive/MyDrive/AI/1.Linear_Regression/ex1data1.txt'
df = pd.read_csv(filename, header = None)
df.head()

def hypothesis(theta, X):
    return theta[0] + theta[1]*X
# borad casting 지원=> theta[1] * X
def cost_calc(m,theta, X, y):
    return 1/(2*m) * np.sum((hypothesis(theta, X) - y)**2)

m = len(df)
def gradient_descent(theta, X, y, epoch, alpha, m):
    cost = []
    i = 0
    while i < epoch:
        hypo = hypothesis(theta, X)
        theta[0] -= alpha*(sum(hypo-y)/m)
        theta[1] -=(alpha * np.sum((hypo-y)*X))/m
        cost.append(cost_calc(m, theta, X, y))
        i += 1
    return theta, cost

def predict(theta, X, y, epoch, alpha):
    theta, cost = gradient_descent(theta, X, y, epoch, alpha, m)
    return hypothesis(theta, X), cost, theta

theta = [0,0]
y_predict, cost, theta = predict(theta, df[0], df[1], 2000, 0.01)

%matplotlib inline
import matplotlib.pyplot as plt
plt.figure()
plt.scatter(df[0], df[1], label = 'Original y')
plt.scatter(df[0], y_predict, label = 'predicted y')
plt.legend(loc = "upper left")
plt.xlabel("input feature")
plt.ylabel("Original and Predicted Output")
plt.show()

plt.figure()
plt.scatter(range(0, len(cost)), cost)
plt.show()
```

predict의 cost는 matplotlib에서 cost가 어떻게 변하는지 보여주기 위해서 붙혀넣는 list이며, theta는 weight와 bias를 나타내고 hypothesis(theta, X)는 prediction을 의미합니다.

## Pandas

위에서 numpy를 통해 구현하면서 data preprocessing 부분을 pandas로 처리하였습니다. 판다스에 대한 설명은 해당 포스트를 참고하시면 좋습니다.
[https://www.notion.so/Pandas-9863ad0661844d75bc08cab7c2c5ab66](https://www.notion.so/Pandas-9863ad0661844d75bc08cab7c2c5ab66)

## matplotlib

figure()→scatter()→legend()→xlabel()→ylabel()→show()

```python
%matplotlib inline
import matplotlib.pyplot as plt
plt.figure()
plt.scatter(df[0], df[1], label = 'Original y')
plt.scatter(df[0], y_predict, label = 'predicted y')
plt.legend(loc = "upper left")
plt.xlabel("input feature")
plt.ylabel("Original and Predicted Output")
plt.show()
```

## Question

1. torch.Tensor.item()은 무엇인가?

   <p align="center">
   <img src="assets\2021-08-12\1.png"/>
   </p>

cost를 보게되면 type이 torch.Tensor이고 cost를 print시키게 되면 어떤 값 하나와, grad_fn이라는 값이 나오게 된다. 여기서 item()을 사용해주게되면 값 만이 튀어나오게 됩니다.
