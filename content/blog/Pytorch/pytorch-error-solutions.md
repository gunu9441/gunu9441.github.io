---
title: Pytorch Error Solutions
date: 2021-09-08 15:09:40
category: Pytorch
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 해당 포스트에 pytorch로 코드를 돌리면서 생긴 Runtime Error를 기록해보려고 합니다!😊 여러분들도 똑같은 Runtime Error를 겪고 계시다면 참고해보는 것도 좋을 것 같네요. 아래는 이번 포스트의 Category입니다.

## Category

1. Runtime Error Solutions
2. Reference

## Runtime Error Solutions

1. `Input type (torch.cuda.DoubleTensor) and weight type (torch.cuda.FloatTensor) should be the same`  
   (related to **input type**)

   **cuda에 올라간 Input type과 weight type이 같아야 한다**고 나와 있습니다. 이럴 땐 input type을 float로 바꿔주면 됩니다. `.float()`를 사용해서 float type으로 바꿔줄 수 있습니다.

   ```python
   # previous code
   #images, labels = images.to(device), labels.to(device)

   # solution code
   images, labels = images.float().to(device), labels.to(device)
   ```

2. `Expected object of scalar type Long but got scalar type Float for argument #2 'target' in call to \_thnn_nll_loss_forward`  
   (related to **input type**)

   `Long type`이 받아져야하는데 `Float type`이 받아졌다고 error가 나오고 있습니다. 이럴 땐 아래와 같이 나타내주면 됩니다.

   ```python
   # previous code
   #images, labels = images.float().to(device), labels.float().to(device)

   # solution code
   images, labels = images.float().to(device), labels.to(device)
   ```

추후에 해결된 error들을 계속 포스트에 올리도록 하겠습니다.  
다음에 봐요~!😎

## Reference

- .float(): [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=chrhdhkd&logNo=221469768189](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=chrhdhkd&logNo=221469768189)
- Expected object of scalar type Long but got scalar type Float for argument #2 ‘target’: [https://discuss.pytorch.org/t/expected-object-of-scalar-type-long-but-got-scalar-type-float-for-argument-2-target/33102](https://discuss.pytorch.org/t/expected-object-of-scalar-type-long-but-got-scalar-type-float-for-argument-2-target/33102)
