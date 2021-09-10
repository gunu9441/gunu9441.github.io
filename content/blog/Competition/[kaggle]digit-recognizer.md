---
title: [Kaggle]Digit Recognizer
date: 2021-09-10 23:09:64
category: Competition
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 Kaggle 입문자들이 Kaggle을 시작할 때 많이 해보는 Digit-Recognizer에 대해 이야기 해보려고 합니다!😊 많은 사람들이 Kaggle을 시작할 때 이 competition에 참가해보고 많은 notebook들을 보면서 딥러닝이나 머신러닝이 무엇이고 어떻게 해야하는지 알게되는데요! Digit-Recognizer은 **MNIST data를 분류**해보는 입문자용 competition입니다. 이 대회를 하면서 **구현 관점에서** 알게되거나 느낀점을 바탕으로 해당 포스트를 작성하고자 합니다. 아래는 이번 포스팅의 Category입니다!

## Category

1. Code Description
2. History
3. Summary
4. Reference

## Code Description

## History

**① version_1**

사용한 Architecture는 다음과 같습니다.

4`conv`- `batch` - `relu` , 1`pooling`, 3`fc layers`

&nbsp; &nbsp; 또한, **He initilizationm**과 **batch normalization**을 적용하여 구현을 하였습니다. **4개의 convolution layer**와 **batchnorm**, **relu를 적용**하였고 **1번의 pooling을 사용**했습니다. 또한 **3개의 fc layer를 사용**하고 **2개의 dropout을 적용**했습니다. 또한 **early-stopping 기법을 사용**했습니다.

&nbsp; &nbsp; 또한 **early stopping을 사용**하여 **model이 training data에 overfitting**되는 것을 막았습니다.

```python

#conv1->batch1->relu->conv2->batch2->relu->pool->conv3->batch3->relu->conv4->batch4->relu
#->fc1->relu->drop->fc2->relu->drop->fc3
class CNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(in_channels=1,  out_channels=16, kernel_size=3, stride=1)
        self.conv2 = nn.Conv2d(in_channels=16, out_channels=32, kernel_size=3, stride=1)
        self.conv3 = nn.Conv2d(in_channels=32, out_channels=64, kernel_size=3, stride=1)
        self.conv4 = nn.Conv2d(in_channels=64, out_channels=128, kernel_size=3, stride=1)

        self.fc1 = nn.Linear(128*8*8,1024)
        self.fc2 = nn.Linear(1024,512)
        self.fc3 = nn.Linear(512,10)

        self.batchnorm1 = nn.BatchNorm2d(16)
        self.batchnorm2 = nn.BatchNorm2d(32)
        self.batchnorm3 = nn.BatchNorm2d(64)
        self.batchnorm4 = nn.BatchNorm2d(128)
        self.drop = nn.Dropout2d(p=0.5)
        self.max_pool = nn.MaxPool2d(kernel_size=2, stride=2)
        self.relu = nn.ReLU()

        #initilization
        nn.init.kaiming_normal_(self.conv1.weight, mode='fan_in', nonlinearity='relu')
        nn.init.kaiming_normal_(self.conv2.weight, mode='fan_in', nonlinearity='relu')
        nn.init.kaiming_normal_(self.conv3.weight, mode='fan_in', nonlinearity='relu')
        nn.init.kaiming_normal_(self.conv4.weight, mode='fan_in', nonlinearity='relu')
        nn.init.kaiming_normal_(self.fc1.weight, mode='fan_in', nonlinearity='relu')
        nn.init.kaiming_normal_(self.fc2.weight, mode='fan_in', nonlinearity='relu')
        nn.init.kaiming_normal_(self.fc3.weight, mode='fan_in', nonlinearity='relu')

def forward(self,x):
        #x shape: 64*1*28*28
        out = self.conv1(x)
        out = self.batchnorm1(out)
        out = self.relu(out)
        #64*16*26*26

        out = self.conv2(out)
        out = self.batchnorm2(out)
        out = self.relu(out)
        #64*32*24*24

        out = self.max_pool(out)
        #64*32*12*12

        out = self.conv3(out)
        out = self.batchnorm3(out)
        out = self.relu(out)
        #64*64*10*10

        out = self.conv4(out)
        out = self.batchnorm4(out)
        out = self.relu(out)
        #64*128*8*8
        #flatten
        out = out.view(out.size(0),-1)
        #64*128*8*8

        out = self.fc1(out)
        out = self.relu(out)
        out = self.drop(out)

        out = self.fc2(out)
        out = self.relu(out)
        out = self.drop(out)

        out = self.fc3(out)
```

**① version_2**

1. data augmentation 추가

## Summary

1.  `transforms.ToPILImage()`를 사용하는 이유는 **csv파일 형태로 데이터셋**을 받을 경우, 바로 `torch.Tensor`로 바꿔줄 수 없기 때문입니다. `transforms.ToTensor()`는 `numpy.ndarray`나 `PIL image`를 **pytorch tensor로 바꿔주기 때문**에 **csv에서 받은 dataframe**을 **PIL image로 바꿔주고 이 PIL image를 pytorch tensor로 바꿔주어야**합니다. 이에 아래와 같이 작성합니다.

    ```python
    from torchvision import transforms

    transfrom = transforms.compose([
    				transforms.ToPILImage(),
    				transforms.ToTensor()
    ])
    ```

2.  data를 어떻게 transform시킬지는 custom dataset을 초기화시킬 때, 인자로 넣어주는 것을 흔하게 볼 수 있었습니다. 아래의 그림처럼 말이죠!😎

    ```python
    class MNIST_data(Dataset):
    		def __init__(self,
    								 file_path,
    		             transform = transforms.Compose([transforms.ToPILImage(),
    														 transforms.ToTensor(),
    		                         transforms.Normalize(mean=(0.5,), std=(0.5,))])
    		              ):
    ```

3.  early stopping은 training set에 대한 오류가 줄어들지만 validation set에 대한 loss 올라가게 되면 멈추는 것을 의미합니다. **① version_1**에서 해당 기법을 추가시켰습니다.
4.  batchnorm이랑 dropout을 섞어쓰면 오히려 **성능이 저하**된다고 합니다😅
5.  `np.arange(1,len(test_data)+1)`을 통해 쉽게 csv의 column을 만들 수 있었습니다~!
6.  torch→numpy로 변환할 땐 `. numpy()`를, Dataframe→numpy로 변환할 땐 `.values` 대신에 `.to_numpy()`를 사용합니다. `.values`는 **deprecate**(비추천!)한다고 하더라구요!  
    또한, numpy를 torch.Tensor로 바꾸기 위해선 `torch.from_numpy()`를 사용합니다. 이 경우, **shape는 유지**한 채로 **type만 변환**됩니다!

    <p align="center">
    <img src="assets\2021-09-10\1.png"/>
    </p>

7.  matplolib이나 cv2의 경우, `numpy.ndarray`를 많이 사용합니다. 이 때, `numpy.ndarray`들의 format은 HWC인데 torch.Tensor의 경우 CHW의 format을 갖습니다. 따라서 HWC인 numpy.ndarray들을 CHW format을 갖고 있는 troch.Tensor로 바꿔주기 위해서 `torchvision.transfroms.ToTensor()`를 사용합니다. 따라서, shape를 바꿔주기 위해선 6.의 `torch.from_numpy()`를 사용하는 것이아니라 `ToTensor()`를 사용해야합니다.

    <p align="center">
        <img src="assets\2021-09-10\2.png"/>
    </p>

    또한 아래와 같은 방법으로 `torchvision.transfroms.ToTensor()`을 사용할 수 있습니다.

    <p align="center">
        <img src="assets\2021-09-10\3.png"/>
    </p>

8.  `plt.scatter()`을 사용하면 **점**으로, `plt.plot`을 사용하면 **그래프**로 그려집니다!
9.  `cv2.imread`는 이미지를 받아 `numpy.ndarray`로 반환합니다. 따라서 csv파일을 읽을 때, cv2로 image를 읽은 듯한 효과를 주기 위해선 pandas의 `read_csv`를 사용하여 DataFrame으로 이미지를 읽은 후 `to_numpy()`를 통해 `numpy.ndarray`로 바꿔주면 됩니다. 즉, 평상시에 이미지를 읽을 때 `numpy.ndarray`로 읽으므로 DataFrame을 받게되면 이것 또한 `numpy.ndarray`로 바꿔서 **data augmentation**(albumentation을 통해)을 수행하면 됩니다. albumentation을 사용할 경우, `numpy.ndarray`
10. Albumentation은 `cv2.imread`를 통해 image를 `numpy.ndarray`로 읽어 data augmentation을 수행합니다. `cv2.imread`를 통해 읽게 되면 `HWC format`을 가진 `numpy.ndarray`를 받게 되는데 Albumentation은 `HWC format`을 가진 `numpy.ndarray`를 그대로 받아 **data augmentation을 수행**합니다. 또한 `matplotlib.pyplot.imshow()`는 argument로 `numpy.ndarray`를 받는데 이 떄의 format 또한 `HWC format`이 됩니다.
    즉, `Albumentations`, `matplotlib`, `cv2`는 `HWC format`을 가지는 `numpy.ndarray`을 다룹니다. 따라서 torch.Tensor에서의 이미지는 `CHW format`이므로 순서를 바꿔줘야합니다. 이에`torchvision.transfroms.ToTensor()`을 사용해야한다고 말했었죠?!😉
11. `np.repeat(ndarray, 반복 횟수, axis=)`을 통해 image차원을 늘려줄 수 있습니다. MNIST 데이터의 경우 1차원일 땐 이상한 색이 나오게 되지만 `image = np.repeat(image,3,axis=2)`을 사용해서 3차원으로 늘리게 되면 익숙한 하얀색 숫자가 나오게 됩니다. 아래의 그림들 처럼 출력되게 됩니다.

    <p align="center">
        <img src="assets\2021-09-10\4.png"/>
    </p>

    <p align="center">
        <img src="assets\2021-09-10\5.png"/>
    </p>

12. `import Albumentations.pytorch.transforms.ToTensor()`를 통해 pytorch tensor로 변환가능합니다.
13. 하나의 dataset객체 안에서 valid datset과 training dataset을 못나눔. dataloader를 통해서 뽑아줄 방법이 없음. 데이터를 나눠놔도 뽑아올 방법이없음...

## Todo

- [ ] albumentation으로 augmentation 전처리해보기
- [ ] vscode로 .py로 나눠서 모델 돌려보기
- [ ] Ray-tune으로 Hyperparameter tuning해보기
- [ ] 내장함수 찾아보기 **len**, **getitem**
- [ ] image(matplotlib과 Albumentation은 numpy 배열기준으로 HWC로 작동함 아마 cv2.imread도 HWC순서대로 읽는듯?)는 HWC순이고 pytorch tensor는 CHW인지 확인하기

## Reference

1. `transforms.ToPILImage()`를 왜 사용하는가?: [https://green-late7.tistory.com/56](https://green-late7.tistory.com/56)
2. numpy stack 방법(concatenate): [https://rfriend.tistory.com/352](https://rfriend.tistory.com/352)
3. Reference notebook: [https://www.kaggle.com/juiyangchang/cnn-with-pytorch-0-995-accuracy/output](https://www.kaggle.com/juiyangchang/cnn-with-pytorch-0-995-accuracy/output)
4. albumentation vs torchvision: [https://pseudo-lab.github.io/Tutorial-Book/chapters/object-detection/Ch3-preprocessing.html](https://pseudo-lab.github.io/Tutorial-Book/chapters/object-detection/Ch3-preprocessing.html)
5. kaggle csv파일 제출하기: [https://wikidocs.net/92861](https://wikidocs.net/92861)
6. model 저장 및 불러오기: [https://tutorials.pytorch.kr/beginner/saving_loading_models.html](https://tutorials.pytorch.kr/beginner/saving_loading_models.html)
7. early-stopping github code: [https://github.com/Bjarten/early-stopping-pytorch/blob/master/MNIST_Early_Stopping_example.ipynb](https://github.com/Bjarten/early-stopping-pytorch/blob/master/MNIST_Early_Stopping_example.ipynb)
8. score 올리는 법:[https://wegonnamakeit.tistory.com/9](https://wegonnamakeit.tistory.com/9)
9. `DataFrame.to_numpy()`: [https://stackoverflow.com/questions/13187778/convert-pandas-dataframe-to-numpy-array/](https://stackoverflow.com/questions/13187778/convert-pandas-dataframe-to-numpy-array/)
10. `plt.subplots(nrows=,ncols=,figsize=)`사용법: [https://matplotlib.org/stable/api/\_as_gen/matplotlib.pyplot.subplots.html](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.subplots.html)
11. `np.repeat()`: [https://numpy.org/doc/stable/reference/generated/numpy.repeat.html](https://numpy.org/doc/stable/reference/generated/numpy.repeat.html)
12. image augmentation using albumentation youtube: [https://www.youtube.com/watch?v=n_f6d4bPFME](https://www.youtube.com/watch?v=n_f6d4bPFME)
13. albumentation probability in One of Block and description about it: [https://albumentations.readthedocs.io/en/latest/probabilities.html](https://albumentations.readthedocs.io/en/latest/probabilities.html)
14. albumentation 간단 소개: [https://hoya012.github.io/blog/albumentation_tutorial/](https://hoya012.github.io/blog/albumentation_tutorial/)
15. albumentation documentation example: [https://albumentations.ai/docs/examples/example/#Import-the-required-libraries](https://albumentations.ai/docs/examples/example/#Import-the-required-libraries)
16. torchvision.transforms vs albumentation: [https://pseudo-lab.github.io/Tutorial-Book/chapters/object-detection/Ch3-preprocessing.html](https://pseudo-lab.github.io/Tutorial-Book/chapters/object-detection/Ch3-preprocessing.html)
17. albumentation 자세한 소개: [https://ichi.pro/ko/albumentation-sijaghagi-pytorch-yejeeseo-dib-leoning-imiji-hwagdae-gibeob-hoegdeug-78798591550456](https://ichi.pro/ko/albumentation-sijaghagi-pytorch-yejeeseo-dib-leoning-imiji-hwagdae-gibeob-hoegdeug-78798591550456)
18. albumentation.pytorch.ToTensorV2 documentation: [https://albumentations.ai/docs/api_reference/pytorch/transforms/](https://albumentations.ai/docs/api_reference/pytorch/transforms/)
