---
title: GAN
date: 2021-08-12 20:08:33
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

이번 시간에는 GAN에 대해서 알아보도록 하겠습니다. GAN은 Generative Adversarial Networks를 의미하며 이것을 적대적 생성 신경망이라고 부릅니다. 본 포스트는 다음과 같은 Category로 이루어져 있습니다.

## Category

1. Discriminate Model & Generative Model
2. Probability Density Function
3. The Principle of GAN
4. Many Kinds of GAN
5. Extension of GAN

## Discriminate Model & Generative Model

먼저, Discriminate Model과 Generative Model의 차이에 대해서 알아보겠습니다. Discriminate Model을 쉽게 설명하자면 input data가 어느 class에 속하는지 classify하는 모델입니다. 예를 들어, 개나 고양이 사진을 주면서 개인지 고양이인지 class를 분류하는 것입니다.

반면, Generative Model은 데이터의 분포를 학습하여 새로운 이미지를 얻어낼 수 있는 모델을 말합니다. GAN의 경우, latent code를 주었을 때 그 code로 부터 이미지 데이터를 만들어 냅니다.

## Probability Density Function

---

Probability Density Function이란 확률 밀도 함수로 확률 변수의 분포를 나타내는 함수입니다. 예를 들어 주사위의 확률 밀도 함수 즉, pdf를 살펴보겠습니다. 주사위의 눈이 나오는 경우의 수는 총 6가지이기 때문에 x축에는 주사위의 눈, y축에는 해당 주사위 눈이 나올 확률을 그리면 아래와 같습니다

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled.png)

위의 함수를 Probability Mass function이며 pdf의 불연속적 형태라고 보시면 됩니다. PDF(Probability Density Function)는 연속적인 형태를 갖게 되는데 아래와 같은 분포를 PDF라고 합니다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%201.png)

다시 주사위의 눈에 대한 경우의 수를 나타내는 PMF로 돌아가서 이야기 해보겠습니다. 여기서 주사위의 눈의 종류는 6개이며 각각의 눈이 나올 확률은 $1\over 6$입니다. 따라서 위와 같은 unifrom distribution을 이루게 되는데 이제 이 x를 주사위의 눈이 아닌 이미지 차원으로 늘리게되면 이미지의 분포를 볼 수 있다. 이때, x는 이미지가 되며 x를 64*64*3의 고차원 벡터로 생각할 수 있게 됩니다.
이미지들을 x축으로 한 PDF가 아래와 같다고 생각해보겠습니다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%202.png)

만약 training dataset에 안경을 낀 남자가 적게 등장한다고 가정하고 그 안경을 낀 남자를 대표하는 고차원 벡터를 $x_1$의 벡터라 했을 때, 그 x1에해당하는 확률밀도 값은 상대적으로 작게 나옵니다. 이후, 안경을 끼지 않은 남자 및 안경을 낀 여자를 대표하는 고차원 벡터를 각각 $x_2$, $x_3$라하고 $x_3$의 확률이 $x_2$의 확률보다 작다고 할 때, 각각의 고차원 벡터가 대표하는 이미지의 갯수가 training data에서 차이가 난다는 이야기고 고차원 벡터(64*64*3) $x_3$에 해당하는 이미지가 고차원 벡터(64*64*3) $x_2$에 해당하는 이미지보다 많다는 것입니다. 즉, 여기서 고차원 벡터 x는 각각의 이미지에 mapping됩니다.

이렇게 training data에 존재하는 이미지에 mapping되는 고차원 벡터를 만들어 PDF를 만들 수 있습니다. 이렇게 만든 PDF에 근사하도록 분포를 만들려는 model이 바로 위에서 배웠던 generative model입니다. Generative model은 해당 generative model에 의해서 만들어진 이미지의 분포가 데이터 셋에 대한 PDF에 근사하도록 만드는 것이 목표입니다. 즉, Generative model의 목표는 실제 학습 데이터의 분포와 model이 만들어낸 분포 간의 차이를 줄여주어 실제 학습 데이터의 분포에 잘 근사하도록 만들고자 하는 것입니다.

## The Principle of GAN

GAN에는 두가지 모델이 존재합니다. 그 모델은 바로 discriminator와 generator라고 불리는 model입니다. 위에서 Discriminative Model과 Generative Model의 정의를 간단하게 살펴보았었는데 바로 GAN의 discriminator는 Discriminative Model과 대응되고 generator는 Generative Model에 대응됩니다. 하지만 GAN의 최종적인 목표는 Generator라고 불리는 Generative model을 학습시켜서 이미지를 제작하는 것입니다. 이 목표를 위해 discriminator를 먼저 학습하게 됩니다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%203.png)

Discriminator의 구조는 다음과 같이 동작합니다. real picture를 discriminator에게 주게 되면 D(x)값을 반환하게 되는데 이것은 진짜 사진인지 가짜 사진인지를 판단한 value입니다. 이에 D(x)는 0~1의 값을 가지게 되는데, 1이라면 진짜 사진, 0이라면 가짜사진이라고 판단하는 것입니다. 이와 같이 두 개의 결과 값을 갖기 때문에 Binary Classifier이며 Binary Cross Entropy를 cost function으로 사용하게 됩니다. 이렇게 위와 같이 real image를 주면서 Discriminator를 학습시키게 됩니다. 이 때 Discriminator의 input은 64*64*3 image를 받게되고 binary classification이기 때문에 1차원의 output을 갖게 됩니다. 이후, 이 1차원의 값을 받아 sigmoid를 거쳐 0.5를 기준으로 binary classification을 수행합니다.

다음으로 Generator가 어떻게 동작하고 어떻게 학습되는지 살펴보겠습니다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%204.png)

위의 그림을 보게 되면 latent code z가 Generator로 들어가서 G(z)가 연산되게 됩니다. Generator가 만든 이미지이기 때문에 G(z)는 당연히 fake image가 되겠죠?? 이 G(z)는 Generator가 만든 사진으로 이 사진이 Discriminator로 들어가게 되는데, 이 때 Discriminator는 이 사진이 진짜 사진(real image)인지 아니면 Generator가 만들어낸 가짜 사진인지 판별해 내게 되는데 우리는 이 값을 D(G(z))라고 합니다. 따라서 이 D(G(z))의 값도 역시 D(x)의 범위와 같게 0부터 1까지의 값을 갖게 됩니다. 우리는 Generator가 만든 사진을 Discriminator가 진짜라고 인식하게 만들어야 하기 때문에 D(G(z))의 값이 1이 되도록 학습하여야 합니다.

즉, Discriminator는 진짜 이미지를 보고서 진짜 이미지라고 판별할 수 있도록 학습이 되야되는 반면에, Generator는 Discriminator가 보고 진짜 이미지라고 판별할만한 G(z) 즉, Discriminator가 봤을 때 진짜 같은 fake image를 만들어야 하는 것입니다.

위의 개념들을 통해서 우리는 다음과 같은 loss function 즉, object function을 작성할 수 있습니다.

${min\atop G}{max\atop D}V(D,G)=E_{x~p_{data(x)}}[logD(x)]+E_{z~P_z(z)}[log(1-D(G(z)))]$

GAN의 object function은 위와 같이 min max problem으로 표현할 수 있으면 위의 식을 최대화하는 D와 최소화하는 G를 찾으면 됩니다. 우선 Disciminator가 존재하는 첫번째 항부터 살펴보겠습니다.

첫번째 항에서, $E_{x~p_{data(x)}}$는 real data distribution에서 sample x를 뽑아 Discriminator에게 넘겼을 때를 의미합니다. 예를 들어, 실제 학습데이터가 10만개의 사람이미지가 있다면 거기서 x값을 하나씩 뽑아온다고 생각하면 됩니다. real image dataset에서 real image를 discriminator가 받았기 때문에 D(x)의 값은 1이 되도록 학습이 되어야 되고 그 때의 logD(x)는 log1이 되므로 0이 됩니다. 만약 Discriminator가 학습이 잘 되지 않아 D(x)가 1이 되지 않고 0이 되었다면 log(0)의 값은 -무한대로 loss가 엄청나게 커지게 됩니다. 이에 Discriminator를 의미하는 D는 해당 식을 -무한대가 아닌 0으로 최대화시키는 방향으로 학습되어야 하기 때문에 위의 식을 최대화 하는 D를 찾아야 하는 것 입니다. 이제, Generator가 존재하는 두 번째 항을 보겠습니다.

두번째 항에서,$E_{z~P_z(z)}$는 Gaussian Distribution에서 latent code z를 하나 sampling하여 D(G(z))연산을 진행한 것 입니다. 즉 random하게 z를 뽑아 Generator를 거쳐 fake image를 생성한 후 이것을 Discriminator에게 판단해보라고 던져주어 Discriminator가 진짜인지 아닌지 0~1까지의 값으로 표현해준 것입니다. 이 때, Discriminator 관점에서 보게되면 Discriminator는 G(z)가 fake image이므로 D(G(z))값이 0을 반환하는 것을 목적으로 합니다. 이 때 log(1)이 되므로 0이 됩니다. 따라서 첫번째항과 두번 째 항을 discriminator 입장에서 보게 된다면 D가 위의 object function을 0으로 최대화시켜야하며 이를 목적으로 학습되어야 합니다.

하지만 Generator 입장에서 보게 되면 위의 식을 최소화 시켜주는 Generator를 찾아야 합니다. Generator가 존재하는 두번 째 항만 살펴보게 되면 Discriminator를 잘 속였다면 두번째 항의 D(G(z))의 값은 1이 나와야하며 두번 째 항의 값이 log(0)이 됩니다. log(0)은 -무한대이므로 G는 위의 식을 -무한대로 최소화 시켜줄 수 있어야 합니다.

이에 우리는 위의 object function을 최대화 시키는 D와 최소화 시키는 G를 찾아야 합니다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%205.png)

1. Discriminator를 먼저 binary classifier로 학습시킨다. 진짜이미지를 진짜이미지로 구분하고 가짜이미지를 가짜이미지로 구분하는 binary classifier.
2. genrative model을 통해서 만들어낸 이미지를 Discriminator에 넣어서 이것이 1이 되게 만들어 준다. 1은 real image라는 것이므로 generative model이 잘 학습될 수있다.

discriminator관점에서 먼저 보자.

위의 그림에서 식은 GAN의 loss함수(objective함수)이다. 이 목적함수를 discriminator가 최대화 하는 것이 목표가 된다. $E_{x~ p_{data(x)}}$ 는 확률 밀도함수를 의미하며 실제 데이터의 분포로 부터 sampling을 진행한다는 의미이다. 실제 학습데이터가 10만개의 사람이미지가 있다면 거기서 x값을 하나씩 뽑아온다고 생각하면된다.

Discrimnator는 진짜 x를 받았을 때는 1에 가까운 값을 내놔야하니까 이것을 수식적으로 logD(x)값을 최대화 하게끔 표현하면 된다.

D(x)는 0~1값을 내보내게 되는데 가장 최대값은 D(x)가 1d일 때 0으로 최댓값이 된다. D(x)가 0일 때는 -무한대로 최솟값이 된다.

z는 random한 vector가된다. z가 Generator의 입력값으로 들어가게 된다. 처음에 가우시안 분포(표준 정규분포)나 uniform distribution 에서 100차원짜리 벡터를 sampling하게 된다. random한 vector를 g한테 줬을 때, 가짜이미지를 생성해내고 (→G(z) )그 G(z)값을 D가 받았을 때 0에 가까운 값을 내놔야한다.

이것을 수식적으로 어떻게표현햇냐면 log(1-D(G(Z)))로 표현함.

log 1-x가 실제 x값이 0일때 최대 값이 되기 때문에 이 수식을 갖고 discriminator입장에서 최대화하도록 학습을 하게되면 진짜 ㅇㅣ미지에 가깝도록 학습하고 가짜이미지를 입력받게 되면 0에 가까운 값을 내놓을 수 있도록 학습하게 된다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%206.png)

Generator는 V(D,G)를 최소화하도록 진행해야한다. 식의 우항의 첫번째항은 관여할 수 없다. 왜냐하면 진짜이미지를 discriminator가 진짜로 학습하는데에는 Generator가 쓰이지 않았기 때문이다.

그래서 실제로 오른쪽 부분만 최소화를 진행하게 된다. 1-D(G(z))를 최소화 할 때 D(G(z))가 1일 때 최소가 된다.

그래서 Generator같은 경우는 Discriminator가 가짜이미지를 받았을 때 최대한 1에 가까운 값을 내놓도록 학습하게 된다.

정리

D(x)는 0에서 1값을 내보냄

log(D(x))의 쵀댓값:0 최솟값 -무한대

discriminator 입장: 진짜이미지를 진짜로, 가짜이미지를 가짜로 학습한다. loss가 0이 되는 방향으로 학습한다. 위의 objective function의 범위는 -무한대 부터 0인데 0이 되는 방향으로 학습해야하므로 최대화하는 방향으로 학습하게 된다. 첫번째항에서 real data distribution으로부터 sample x를 뽑아서 1이라고 말을 해야하기 때문에 logD(X)로 두게되면 이 값은 0이나오므로 학습방향과 맞다.이후, log(1-D(g(z))에서는 generative model이 만든 이미지 이기 때문에 D(g(Z))는 0이나와야하므로 log(1-D(g(z))를 하여 이 값이 0이되는 방향으로 학습하게 됩니다.

generative model입장에선 자신이 만든 이미지를 discriminator에게 넣었을 때 1이나와야 하므로 log(1-1)은 -무한대이기 떄문에 -무한대가 나오도록 학습한다. -무한대는 최소화된 값이 기떄문에 결국 generative model은 objective function을 최소화하는 것이 목표이다.

디스크리미네이터 입장에서보게되면 아 objectice function을 최대화해야한다. 디스크리미네이터가 진짜 데이터 x아 1을 내놓게되면 0이되므로 최대가되고 loss는 0이됨.

두번째항에선, 디스크리미니네이터가 z를 받았을 때 fake이니까 이것을 0이나오게 학습해야하므로 0이나와야 loss가 없는거지? 이렇게 두 항이 다 0이나와야 loss가 0이되고 최대화되는 것이다.

제너레이티브 모델 입장에선 G(z)가 real image로 학습시켜야하므로 discriminator를 속여서 fake image를 real image인척해야한다. 그래서 D(G(Z)))가 1이 나오도록 학습시켜야하므로 log0이 나오게 해야한다. 이에 이 object loss를 -무한대로 최소화 시켜야 한다.

실제로 image를 학습시킬 땐 -1~1사이로 normalization을 해줘서 진행하기때문에 진짜 이미지의 범위를 맞춰주기위해서 tanh사용함

하지만 tanh안써도됨. Generator가 학습하는 과정에서 -1보다 작은값, 1보다 큰 값을 내보내면 안된다는 것을 학습하기 때문에 쓰거나 안쓰는건 선택사항

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%207.png)

nn.BCELoss를 사용해서 D를 학습하게 된다. D(x)를 넣었을 땐 무조건 1이나와야하므로 criterion(D(X),1)을 넣어서 해주고, D(G(Z))를 넣었을 땐 가짜이미지 이므로 0이라고 학습해야한다. 그렇기 때문에 criterion (D(G(Z)),0)을 사용.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%208.png)

실제로 generator를 학습할 때는 discriminator를 고정시켜주어야 한다. 그래서 실제 D(G(Z))와 1의 차이값을 계산해서 backpropagation하되 discriminator에 있는 weight와 bias는 학습안하고 generator부분에서만 학습한다.

g_optimizer.step()을 하게되면 discrminator학습은 안되고 generator만학습

중요한 것은 backpropagation은 되되 discriminator의 weight와 bias는 generator가 학습할때는 학습이 되지 않는다.

- 코드

  ```python
  import os
  import torch
  import torchvision
  import torch.nn as nn
  from torchvision import transforms
  from torchvision.utils import save_image

  # Device configuration
  device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

  # Hyper-parameters
  latent_size = 64
  hidden_size = 256
  image_size = 784
  num_epochs = 200
  batch_size = 100
  sample_dir = 'samples'

  # Create a directory if not exists
  if not os.path.exists(sample_dir):
      os.makedirs(sample_dir)

  # Image processing
  # transform = transforms.Compose([
  #                 transforms.ToTensor(),
  #                 transforms.Normalize(mean=(0.5, 0.5, 0.5),   # 3 for RGB channels
  #                                      std=(0.5, 0.5, 0.5))])
  transform = transforms.Compose([
                  transforms.ToTensor(),
                  transforms.Normalize(mean=[0.5],   # 1 for greyscale channels
                                       std=[0.5])])

  # MNIST dataset
  mnist = torchvision.datasets.MNIST(root='../../data/',
                                     train=True,
                                     transform=transform,
                                     download=True)

  # Data loader
  data_loader = torch.utils.data.DataLoader(dataset=mnist,
                                            batch_size=batch_size,
                                            shuffle=True)

  # Discriminator
  D = nn.Sequential(
      nn.Linear(image_size, hidden_size),
      nn.LeakyReLU(0.2),
      nn.Linear(hidden_size, hidden_size),
      nn.LeakyReLU(0.2),
      nn.Linear(hidden_size, 1),
      nn.Sigmoid())

  # Generator
  G = nn.Sequential(
      nn.Linear(latent_size, hidden_size),
      nn.ReLU(),
      nn.Linear(hidden_size, hidden_size),
      nn.ReLU(),
      nn.Linear(hidden_size, image_size),
      nn.Tanh())

  # Device setting
  D = D.to(device)
  G = G.to(device)

  # Binary cross entropy loss and optimizer
  criterion = nn.BCELoss()
  d_optimizer = torch.optim.Adam(D.parameters(), lr=0.0002)
  g_optimizer = torch.optim.Adam(G.parameters(), lr=0.0002)

  def denorm(x):
      out = (x + 1) / 2
      return out.clamp(0, 1)

  def reset_grad():
      d_optimizer.zero_grad()
      g_optimizer.zero_grad()

  # Start training
  total_step = len(data_loader)
  for epoch in range(num_epochs):
      for i, (images, _) in enumerate(data_loader):
          images = images.reshape(batch_size, -1).to(device)

          # Create the labels which are later used as input for the BCE loss
          real_labels = torch.ones(batch_size, 1).to(device)
          fake_labels = torch.zeros(batch_size, 1).to(device)

          # ================================================================== #
          #                      Train the discriminator                       #
          # ================================================================== #

          # Compute BCE_Loss using real images where BCE_Loss(x, y): - y * log(D(x)) - (1-y) * log(1 - D(x))
          # Second term of the loss is always zero since real_labels == 1
          outputs = D(images)
          d_loss_real = criterion(outputs, real_labels)
          real_score = outputs

          # Compute BCELoss using fake images
          # First term of the loss is always zero since fake_labels == 0
          z = torch.randn(batch_size, latent_size).to(device)
          fake_images = G(z)
          outputs = D(fake_images)
          d_loss_fake = criterion(outputs, fake_labels)
          fake_score = outputs

          # Backprop and optimize
          d_loss = d_loss_real + d_loss_fake
          reset_grad()
          d_loss.backward()
          d_optimizer.step()

          # ================================================================== #
          #                        Train the generator                         #
          # ================================================================== #

          # Compute loss with fake images
          z = torch.randn(batch_size, latent_size).to(device)
          fake_images = G(z)
          outputs = D(fake_images)

          # We train G to maximize log(D(G(z)) instead of minimizing log(1-D(G(z)))
          # For the reason, see the last paragraph of section 3. https://arxiv.org/pdf/1406.2661.pdf
          g_loss = criterion(outputs, real_labels)

          # Backprop and optimize
          reset_grad()
          g_loss.backward()
          g_optimizer.step()

          if (i+1) % 200 == 0:
              print('Epoch [{}/{}], Step [{}/{}], d_loss: {:.4f}, g_loss: {:.4f}, D(x): {:.2f}, D(G(z)): {:.2f}'
                    .format(epoch, num_epochs, i+1, total_step, d_loss.item(), g_loss.item(),
                            real_score.mean().item(), fake_score.mean().item()))

      # Save real images
      if (epoch+1) == 1:
          images = images.reshape(images.size(0), 1, 28, 28)
          save_image(denorm(images), os.path.join(sample_dir, 'real_images.png'))

      # Save sampled images
      fake_images = fake_images.reshape(fake_images.size(0), 1, 28, 28)
      save_image(denorm(fake_images), os.path.join(sample_dir, 'fake_images-{}.png'.format(epoch+1)))

  # Save the model checkpoints
  torch.save(G.state_dict(), 'G.ckpt')
  torch.save(D.state_dict(), 'D.ckpt')
  ```

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%209.png)

처음 geneartive model은 형편없는 그림을 만들게 되는데 이 때 log(1-D(G(z)))에서 D(G((z))는 0이된다. log(1-x)그래프가 x=0일 때 gradient가 낮다고 한다. 이에 log(1-x)를 최소화하는것 대신, log(x)를 최대화 하는 방향으로 학습하게 된다. log(x)는 아래 그림과 같다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2010.png)

log(x)에서의 기울기는 거의 gradient는 무한대에 가깝다. 초반에 generator가 굉장히 안좋은 상황, discriminator가 가짜라고 확신하는 상황을 최대한 빨리 벗어나기 위해 사용한다.

heuristic 하게 나옴.

이 개념을 사용g하려면 위와 같이사용하면 된다. nn.BCELoss()여기에다가 y에 1d을 넣게되면 -log가 나오면서 Genrative model이 해당식을 최소화하는 objective function이 되는데 이것은 generative model이 log를 최대화 하는 것과 같다.

Question

discriminator가 1을 내뱉기위해서 generator를 어떻게 수정해야할지의 gradient 값이 backpropagation되는데 그거에 맞춰서 generator는 이렇게 생성해야하는구나라고하고 학습진행된다.

discriminator와 generator를 같이 tranining시키는 것인가?

맞다. discriminator를 한번 training시키고 generator 한번 ㅌtraninig시키고 minibatch dataset에대해서.

그래서 while (TRUE)를 통해 반복해서 학습

Generative model의 목표는 실제 데이터의 분포와 모델이 생성하는 데이터의 분포간의 차이를 줄여주는 것이 목표

처음 log에서 gradient값이너무크니까 널뛰기를 할것같은데 어떻게 방지?

lr조정 및 initialization 기법 사용

---

이미지 분포간의 차이를 계산하는 함수여러가지있는데 jenson shannon divergence가 있음

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2011.png)

이론적으로 왜 잘되는지?

최적화하는 것은 실제로 두 서로 다른 확률 분포간의 차이를 줄여주기 때문에 실제 generator가 진짜에 가까운잉미지를 만들수있는것이다.

DCGAN(Deep Convolutional GAN)

CNN을 사용을 해서 discriminator를 구현하고 deep convolutional neural network를 통해 generator를 만든 모델

아직까지도 선호많이됨

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2012.png)

수학적인건 따로 없고 기존에는 discriminator에서는 fc썼지만 여기서는 convolution layer를 사용했다.

generator에서는 deconvolution 혹은 transpose convolution을 사용해서 upsampling학습

DC간의 핵심은 pooling layer를 사용하지 않았다.

pooling layer를 사용하게되면 unpooling을 할때 blocky한 이미지가 생성되기 때문에 이걸 막기위해 pooling layer를 사용하지 않았다. 대신 stride size가 2이상인 convoution과 deconvolution을 사용

batch normalization사용

Adam optimizer사용

보통 64\*64를 생성할 때, convlayer를 4개정도 써서 만든느데 저 모멘텀 term을 사용하게 되면 학습이 실험적으로 잘 되고 있다. 요즘에서도 이렇게 고정시켜사용한다. lr = 0.0002 beta1=0.5 beta2 = 0.999

DCGAN에서 재미있는점은 generator에 input으로 들어가는 latent vector를 가지고 산술연산이 가능하다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2013.png)

안경낀 남자를 만든는 z 벡터값→>2차원이라면 0.5,0.2

안경낀 남자를 만드는 z벡터값 간에 산술적인 연산 가능

안경낀 남자 - 안경끼지 않은남자 > 안경 + 안경안낀 여자 = 안경낀여자

z벡터가 어떤 선형적인 관계를 갖는다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2014.png)

LSGAN(Least Squares GAN)

기존의 GAN은 discriminator를 속이기만하면됨

2번빼 그림에서 파란색그림은 discriminator의 decision boundary를 의미한다. 진짜가짜를 구분하는 decision boundary. 0.5를 기준으로 높으면진짜 낮으면가짜

아래쪽이 discriminator가 생각한 진짜데이터들

빨간색이 진짜데이터

파란색이 가짜 데이터

빨간색에 가까운 파란색은 굉장히 generator가 잘만든 image이다. 애ㅗ냐하면 값이 굉장이하까운곳에이ㅣㅆ어서

핑크색을보게되면 discriminator를 완벽하게 속인것 하지만 이것은 올바르지 않음. 빨간색에 가까울수록좋은 것이다.

discriminator가내뱉은 결과값이 0.9에가까운 값이다.

decision boundary근처에있는것은 0.5가되고 거기로부터 멀리떨어져있으니까

핑크색이좋지는않은게 빨간색이 진짜 데이터인데 그거랑 가까운게 좋은 것이다. discriminator를 속이긴했는데 좋지 못한걸 생성해낸것이다. 여기서 gradient vanishing문제가 생김

discriminator를 완벽하게 속였다고해도 그걸 실제데이터와 비슷하다고 보장할 수 없어서 least square gan에서는 3번째 그림을 보게되면 분홍색데이터가 위쪽 decision boundary쪽으로 끌어서 올라간다. 그래서 실제 데이터와 비슷하게 놓는다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2015.png)

sigmoid를 없앴따.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2016.png)

기존에 crossentropy를 사용했었는데 그걸 least square loss로 바꾸게 됨

왼쪽에서는 log(D(x))일 때 실제 데이터를 넣으니까 왼쪽항은 0 이나오고 오른쪾항은 실제데이터가아닌걸 판별하는거니까 log(1-0)해서 0이나오는데 이는 이상적인 경우이므로 loss가 0이나온다.

오른쪽에서는 실제데이터를넣으니까 (1-1)^2하게 되면 0이되고 오른쪽이 D(G(Z))는 unreal image니까 0^2하면 0이나오므로 이상적인경우 동일하게 0이나온다.4

generator에서 D(G(Z))가 3이나오면 이를 1로 밪춰주게 된다. 왜냐하면 3이면 discriminator를 완벽하게 속인거지만 sigmoid를 뺴었기 떄문에 loss가 1보다 커질 수 있게 된다. 이는 D의 한계를 0부터 1까지 제약하지 않으려고 sigmoid함수를 빼어서 D(G(z))를 사용했기 때문에 나온 결과이다. 이렇게하면 decision boundary쪽으로 데이터를 모을 수 있따. 또한 D의 loss에서 D(X)가 1에 가까워지고 D(G(Z))도 0에 가까워진다. 왜냐하면 loss를 0으로 만들기 위해 gradient descent가 진행되는데D(x)가 3이라면 loss가 커져서 loss를 0으로 만든는방향으로 가기 때문에

그래서 discriminator가 1보다 큰값을 만들어주게되면 확실히진짜라고 생각하는데 그럼에도 불과하고 generator는 1에 가깝도록 줄여주게된다. least square loss가 0에 가깝도록학습하니까(D(G(Z))r가 1에가깝도록만들어야하니가→그래야 loss가 0이됨)

1보다 작은 값이 나오든 큰 값이 나오든 실제로 decision boundary쪽으로 데이터를 몰게된다. 이것이 아까 3번쨰그림에서 분홍색을 decision boundary쪽으로 이동시킬 수 있는 개념이다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2017.png)

---

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2018.png)

discriminator에서 sigmoid를 없애고 crossentropyloss를 L2 loss로 바꾼것뿐인데 이렇게됨.

task는 사람이보고 잘됬는지 안됬는지 판단. inception score라는것도잇음

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2019.png)

Semi - supervised GAN

---

discriminator가 더이상 진짜 가짜를 구분하지 않고 어떤 class를 구분한다.

기존의 supervised learning에서는 MNIST는 클래스 갯수가 10개 이다.

지금같은 경우 10개의 class에 ㄹfake class를 추가시킨다.

discriminator같은 경우는 진짜 5 image를 받게되면 one hot vector로 5가 나오도록 softmax를 사용해서 modeling.

가짜 image를 생성 할 때는 generator한테 2가지를 넣어줌

하나는 latent vector를 넣고 하나는 class를 나타내는 onehotvector형태로 vector를 넣어줌.

이는 generator한테 너 2를 생성해봐라고 명령하는 것이다.

가짜 이미지 2를 생성하게되면 discriminator는 이것을 fake class로 구분해야하고 generator는 반대로 discriminator를 속여야하기 때문에 이 input으로 준 one hot vector랑 output이 똑같이 나올수있도록 학습한다.

discrminator를 보면 supervised learning인데 밑에 껄 보면 generator가 만든거라 unsupervised learning이된다.

이에 semi-supervised GAN이라고 부른다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2020.png)

mNIST는 너무 쉬우니까 케릭터 set으로 각각의 케릭터가 서로다른 포즈를 갖도록 해보자 각각의 onehotvector가 하나의 클래스를 의미.

z vector가 변하지 않고 one hot vector만 바꾸면 포즈만 변한다.

보라색 머리에 해당하는 z vector가 있을 텐데 이 z vector를 유지하고 one hot vector만 2로 바꾸게 되면 pose만 바뀌면 머리는 그대로인 케릭터가 나옴.

ACGAN(Auxiliary Classifier GAN)

---

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2021.png)

discriminator는 fake인지 real인지 결정하고, 진짜든 가짜든 one hot vector로 class를 결정한다. 여기서는 class 갯수가 정확히 10개가 된다. 왜냐하면 MNIST이기 때문에

discriminator같은 경우 multi-task learning을 한다. 이말은 즉, 두 가지 task를 동시에 잘해야하고discriminator 중간에 있는 convolution layer는 두가지 task를 모두 잘해야하기 때문에 multi-task learning이된다.

가장 마지막 단에 있는 fully connected layer만 하나는 sigmoid로 하나는 softmax로 모델링해서 독립적으로 학습되게 되다.

가짜 이미지를 갖고 학습을 할 때는 latent vector z와 one hot encoded된 class정보를 주게된다.

2에 해당하는 정보를 주게되고 Generative model을 돌려서 Discrminator에 넣고 돌림 discriminator는 가짜라고 예측을 해야하고 가짜임에도 불구하고 class를 2라고 구분해야한다. 이렇게 해주는 이유는 아까 앞에꺼는 가짜 이미지이면 그냥 가짜라고 구별했다. 만약 위처럼하게 되면 discriminator가 가짜 2로 구분하게 되면 generator가 해주는 역할은 data augmentation의 역할을 해주는 것이다.

이 태까지의 GAN은 generative model에 집중하였다면 ACGAN은 discriminator에 집중해서 genrator는 data augmentation하는 효과가 있고 어떤 noise가 포함된 이미지가 들어가더라도 그 이미지를 제대로 classification할 수 있으면 discriminator가 좀 더 성능이 좋아지지 않을까 라는 해석!

z는 무의미했다가 training 이후 의미를 가지며 선형성을 갖는다.

ACGAN은 loss function이 그냥 1번과제와 2번과제 loss를 그냥 더하는 것이다.

### Exctension of GANs

---

GAN의 활용

cycle GAN, disco GAN ⇒ main idea는 완벽히 같음

cycle GAN

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2022.png)

어떤 얼룩말 사진이 있을 때 얼룩말을 말로바꾸고 말사진이 잇을 떄 말을 얼룩말로바꾸는 것

이미지의 domain or style을 바꾸는 것이다.

이렇게 할때 paired image, parallel data없이 unsupervised learning으로 될수있지않을까해서 GAN을 사용해서만듬

어떻게 가능해질까??

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2023.png)

generator은 더이상 latent code를 받지 않고 어떤 image를 받음

encoder decoder 식으로 줄어들었다가 늘어나게 되는데 discriminator에 집중을 해봄

discriminator같은 경우 하고자 하는게 A domain이미지를 말로 바꾸고 싶은것이다.

discriminator에게는 말 image만 주고 얘가 진짜다 라고 학습을 하고 generator는 얼룩말 이미지를 받았을 때 얼룩말을 그대로 내보내게 되면 discriminator는 걔네를 본적이없으니 가짜라고 판별한다. 이에 Generator가 discriminator를 속이기 위해 얼룩말을 받았을 때 말로 바꾸게 된다. 이렇게 속였는데 얼룩말 모양이 유지가 될 필요가 있을 까?

그럴 필요강벗음 . discriminator만 속이면 되니까 얼룩말 사진을주고 아래쪽 말이 뛰고 있는 사진을 생성해도 discriminator를 속일 수 있게된다. 근데 이것을 막아줘야함.domain transfer , style transfer에서는 얼룩말 이 말로 변하되 그모양을 좀 유지하고 싶은것이 이 task의 목표이기 떄문이다.

그 모양을 유지하기 위해서 아래 그림과 같이해준다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2024.png)

얼룩말 이미지를 말로 바꾸고 말이미지를 다시 얼룩말로 바꾸게 됨.

### STACK GAN

---

어떤 text를 주고 이미지를 만드는것이다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2025.png)

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2026.png)

G2입젱에서는 D2를 속이게 되는데 가장 쉬운방법이 색깔을 바꾸는게 어려우니가 색깔을 바꾸지 않고 그냥 크기를 잘바꾸면서 upsampling.

encoding됐다가 decoding되는 이유?

CNN으로 구현을 하는데 이미지를 받으면은 한번 축소를 해줌. convolution layer를 여러개 쌓게되면 뒤에있는 filter가 큰영역을 볼 수 있게된다. 그래서 한번 encoding해주고 decoding을 해준다. 그래서 보통 convolution layer를 3, 4개 정도 써서 encdoing해주고 decoding해줌

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2027.png)

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2028.png)

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2029.png)

[https://www.youtube.com/watch?v=odpjk7_tGY0](https://www.youtube.com/watch?v=odpjk7_tGY0) 20:49

test data를 주고 그거에 해당하는 z벡터를 어떻게 찾냐면 G는 고정시키고 z를 random하게 setting한다. 이후 z를 forward propagation해서 이미지를 만든다. 처음 이미지는 여자를 만들 수도있고 이상한이미지를만듬

이후 G(Z)와 x를 빼서 제곱함(L2 loss) 이렇게 reconstructin loss를 계산해서 loss를 backpropagtation해서 z값 학습.

training 할 때 보여주지 않았던 test이미지를 주고 그걸 만들 수 있는 z벡터를 찾을 수 있따면 그래서 그 z벡터를 찾고 이미지를 잘만들수있다면 test이미지는 training할 떄 안보여줬으니까 model이 외운게아니고 model이 일반화를ㄹ 해서 잘생성했다고 볼 수 있다.

단점: z를 학습하고나서야 loss를 볼 수 있음.

loss를 찍어보고 싶은데 loss를 찍어보기 위해서는 z를 학습해야한다.

배보다 배꼽이 더 크게 됨.

그리고 굉장히느림

Deconvolution'

좀더 좋은 upsampling 을 찾아봐야하지 않을까!

기존에는 deconvolution을 많이 사용했었다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2030.png)

지금 filter size가 3*3이고 위에 1*1을 9개의 픽셀로 upsampling하게 된다.

사진을 보게되면 2칸씩오른쪽아랫쪽으로가는데 겹치는부분이많다.

output을 만들때 불균형하게 만들게됨

이렇게하게되면 deconvolution의 단점은 뭔가 checkboard형식으로 이상하게 나오게 된다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2031.png)

deconv를 쓰지말고 resize conv를 써야한다고 주장한다.

resize conv는 간단하게는 deconv는 upsampling하는거자체를 학습함

resize conv는 upsampling은 일단 rule base 방식으로 하고 (nearest neighbor) 그다음 convolution의 stride를 1로고정하고 filtering을 여러개의 레이어로 쌓게된다. 그렇게 하게되면 장점은 deconvolution처럼 checkboard

패턴이 생기지 않고 3\*3의 stride 1을 쓰게되면 골고루 해줄 수 있다.

![Untitled](GAN%20a750ac47810b4199b8b61af0c53fdaca/Untitled%2032.png)
