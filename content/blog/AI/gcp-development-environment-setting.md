---
title: GCP Development Environment Setting
date: 2021-09-26 23:09:98
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 Windows 환경의 터미널(WSL2)을 통해 Google Cloud Platform에 원격 접속하는 환경을 어떻게 구성하는지 알아보도록 하겠습니다!😊 본 포스트는 WSL2가 설치되어 있다는 가정하에 진행되며 됩니다. 또한, `gcloud` command 중 자주 사용하는 명령어를 알아보며 어떻게 VM instance에 접속할 수 있는지 알아보도록 하겠습니다.

## Category

1. What is SDK?
2. `gcloud` Commands
3. Useful Command
4. Reference

## What is SDK?

&nbsp; &nbsp; **SDK**란 **"Software Development Kit"**의 줄임말입니다. **SDK**는 일반적으로 하드웨어 플랫폼, OS(운영체제) 또는 프로그래밍 언어의 **제작사가 제공해주는 tool이라고 생각**하시면 됩니다. 쉽게 말해서 어떤 물품(개발자 관점에서 봤을 때, 물품이 OS, 언어, platform 등등을 의미하겠죠?😊)을 만든 **회사(제작사)가 제공하는 유용한 도구(tool)라고 생각**하시면 됩니다!

&nbsp; &nbsp; 저희는 **GCP를 사용**하기 때문에 **Google Cloud에서 제공하는 SDK를 설치**해야합니다. 즉, G**oogle Cloud Platform을 사용하기 위해서 Google Cloud SDK를 사용해야 하는 것**입니다. Google Cloud SDK안에는 Google Cloud쪽에서 만든 **platform과 각종 서비스를 사용하기 위한 tool이나 library들**이 담겨 있습니다. 대표적으로 **SDK가 지원하는 command**에는 `gloud`, `bq` 등이 있습니다.

&nbsp; &nbsp; 이제 Google Cloud SDK가 무엇이고 어떤 것을 지원하는지 알았으니 설치해봅시다! 하지만 Google Cloud SDK를 설치하기 전에 **python3를 필수로 설치**해야 합니다. 왜냐하면 **Google Cloud SDK 자체가 python 파일로 작성되어있기 때문**인데요! 이에 **①Python3**를 먼저 설치한 이후 **②Google SDK를 설치**해보도록 하겠습니다😎

```bash
// apt-get version update
sudo apt-get update

// install python3
sudo apt-get install -y python3 python3-pip

// installation completed
python3
```

&nbsp; &nbsp; `python3`를 작동시켜서 python code를 작성시키는 shell이 나오게 되었다면 알맞게 설치가 된 것입니다. 다음으로는 **Google Colud SDK를 설치**해보도록 하겠습니다.

```bash
curl https://sdk.cloud.google.com | bash
```

&nbsp; &nbsp; 위 명령어 한줄로 Google Cloud SDK를 설치할 수 있습니다😉

&nbsp; &nbsp; 이후, shell을 재시작하고 Google Cloud SDK가 잘 설치되어 있는지 확인하기 위해 아래와 같은 2개의 command를 작성합니다.

```bash
// shell 재시작
exec -l $$SHELL

// Google Cloud SDK installation check
gcloud version
```

## `gcloud` Commands

1.  `gcloud auth list`: **등록된 계정 확인**

    &nbsp; &nbsp; `gcloud auth list`을 사용하면 **현재 사용 가능한 계정**을 볼 수 있습니다. 계정을 등록하게 되면 아래와 같이 list형태로 나타나게 됩니다. 이 command의 결과는 아래와 같습니다.

    <p align="center">
      <img src="assets\2021-09-27\1.png"/>
    </p>

    &nbsp; &nbsp; `gcloud auth login`을 통해 **계정을 등록**할 수 있습니다.

2.  `gcloud projects list`: **현재 계정 안에 있는 project들을 확인**

    &nbsp; &nbsp; `gcloud projects list`를 사용하면 현재 로그인된 계정에 존재하는 project들을 보여주게 됩니다.

    <p align="center">
      <img src="assets\2021-09-27\2.png"/>
    </p>

3.  `gcloud compute instances list`: **현재 project안에 있는 VM instance들 확인**

    &nbsp; &nbsp; `gcloud compute instances list`를 사용하면 **특정 프로젝트**(**모든 프로젝트 밑에 있는 instance가 출력되는 것이 아닙**니다!) **밑에 존재하는 instance를 확인**할 수 있습니다. 각각의 VM instance의 Zone(Region내의 어떤 zone인지), Machine type(머신종류), PREEMPTIBLE(선점형),내부IP, 외부IP, STATUS(RUNNING or TERMINATED)를 나타내 줍니다.

    <p align="center">
      <img src="assets\2021-09-27\3.png"/>
    </p>

4.  `gcloud compute instances describe instance-1`**:** **현재 project안에 있는 instance-1에 대한 설명**

    &nbsp; &nbsp; `gcloud compute instances describe instance-1`를 입력하면 아래의 화면처럼 결과가 나오는 것을 볼 수 있습니다. 현재 **"golr"** project안에 있기 때문에 **"My First Project"**라는 project에 존재하는 **instance-1을 찾을 수 없는 것**입니다.

    <p align="center">
      <img src="assets\2021-09-27\4.png"/>
    </p>

    &nbsp; &nbsp; **"golr"** project에 존재하는 instance인 **k80-gpu-instance**를 입력해보겠습니다.

    <p align="center">
      <img src="assets\2021-09-27\5.png"/>
    </p>

    &nbsp; &nbsp; 위와 같은 결과가 나오게 됩니다.

    &nbsp; &nbsp; `gcloud compute instances list`, `gcloud compute instances describe instance-1`을 통해서 알 수 있었던 것은, **shell을 통해 접속해있는 프로젝트의 VM instance에만 접근**할 수 있는 것입니다. **현재 shell을 통해 접속해있지 않은 "My First Project"의 VM instance는 출력되지 않았으니까요!**

    &nbsp; &nbsp; 그렇다면 5.에서는 **어떻게 My Frist Project로 들어가는지** 알아보도록 하겠습니다.

5.  `gcloud config set project <project name>`**: 프로젝트 전환**

    &nbsp; &nbsp; `gcloud config set project <project name>`를 통해 원하는 project로 들어갈 수 있습니다. 아래의 그림을 보게되면 `prefab-bonito-32640`이라고 입력되어 있는데 이것은 **"My First Project"**의 `Project_ID` 입니다.

    <p align="center">
      <img src="assets\2021-09-27\6.png"/>
    </p>

    &nbsp; &nbsp; 이 때, **My First Project에 존재하는 instance-1 VM instance에도 접근**할 수 있습니다. 그렇다면 **"golr" project안에 있는 k80-gpu-instance VM instance에 접근**해보겠습니다.

    &nbsp; &nbsp; 아래의 그림은 `gcloud compute instances describe k80-gpu-instances`을 입력한 결과입니다.

    <p align="center">
      <img src="assets\2021-09-27\7.png"/>
    </p>

    &nbsp; &nbsp; **현재 shell**이 `gcloud config set project prefab-bonito-32640`을 사용하여 **MY First Project안에 있으므로** 다른 프로젝트(**"golr"** project)안에 있는 **k80-gpu-instance VM instance에 접근할 수 없습니다.**

6.  `gcloud compute ssh <Instance_Name>`**: VM instance에 원격 접속**

    &nbsp; &nbsp; 아래의 그림은 `gcloud compute ssh k80-gpu-instance`를 입력한 결과 화면입니다. 첫번째로 `gcloud compute ssh k80-gpu-instance`를 입력하게 되면 **외부 IP address를 찾을 수 없다**고 뜹니다. 이 오류는 **k80-gpu-instance를 시작시키지 않았기 때문**인데요!

    <p align="center">
      <img src="assets\2021-09-27\8.png"/>
    </p>

    &nbsp; &nbsp; 이에 다시 시작시키기 위해 `gcloud compute ssh k80-gpu-instance`를 입력하였습니다. 그런데 다시 오류가 뜨게 됩니다. **ssh가 외부 IP의 22번 포트로 연결**을 하려고 하는데 **연결이 거절** 되었다고 뜹니다. 이것은 **22번 포트를 허용해주지 않았기 때문에 뜬 것**인데요 이것은 아래와 같은 방법으로 방화벽을 열어줄 수 있습니다.

    ```bash
    gcloud compute firewall-rules create default-allow-ssh ls--allow tcp:22
    ```

    &nbsp; &nbsp; 위의 command를 입력하게 되면 아래와 같이 **default-allow-ssh가 생성**됩니다.

    <p align="center">
      <img src="assets\2021-09-27\9.png"/>
    </p>

    &nbsp; &nbsp; 이제 `gcloud compute ssh`를 통해 VM instance에 접근해보겠습니다. 현**golr project**안에 있으므로 golr project의 VM Instance에 접근해보도하겠습니다.

    &nbsp; &nbsp; 먼저, **golr project에 2개의 VM Instance**를 만들었습니다. `gcloucompute instances list`를 입력하여 어떤 VM Instance가 있는지 확인해보도하겠습니다.

    <p align="center">
      <img src="assets\2021-09-27\10.png"/>
    </p>

    &nbsp; &nbsp; 위 그림의 두가지 instance 중 k80-cpu-instance에 접근해보도록 하겠습니다`gcloud compute ssh k80-gpu-instance`를 통해 접근합니다.

    <p align="center">
      <img src="assets\2021-09-27\11.png"/>
    </p>

    &nbsp; &nbsp; **성공적으로 접근**한 것을 볼 수 있습니다. 만약 처음 만들어 접근하는 instance라면 아래와 같이 뜰 것입니다.

    <p align="center">
      <img src="assets\2021-09-27\12.png"/>
    </p>

    &nbsp; &nbsp; 저의 경우, **기존에 private key와 public key를 부여**받았기 때문local에 존재하는 private key에 접근하기 위해 비밀번호를 입력하면 됩니다처음하시는 분들은 `gcloud compute ssh`를 사용하시면 private key와 publikey를 먼저 할당받는 절차가 있을 겁니다. 즉, 결론적으로 `gcloud computssh`만 입력하면 GCP에서 알아서 다 해준다는 말이죠!😎

    &nbsp; &nbsp; 이후, 성공적으로 VM instance에 접속을 하면 아래와 같은 결과화면이 뜨됩니다.

    <p align="center">
      <img src="assets\2021-09-27\13.png"/>
    </p>

    &nbsp; &nbsp; nvidia driver를 다운받을 때 주의사항은 중간에 `ctrl+c`를 누르지 마세(저는 uncompressing 도중에 `ctrl+c`를 누른지 모르고 있다가 삽질하고해결했습니다...😂 만약 저처럼 `ctrl+c`를 눌러서 도중에 멈`driver_installer.run`이 만들어지신 분들은 `.sudo/driver_installer.run` command에 입력하세요! (아니면 vm instance를 삭제하고 다시 만들면 됩니다!😉)

7.  `gcloud compute instances start <Instance_Name>`**: VM instance 시작**

    &nbsp; &nbsp; `gcloud compute instances start <Instance_Name>`을 통해 정지 되어 있는 VM Instance를 시작시킬 수 있습니다.

    <p align="center">
      <img src="assets\2021-09-27\14.png"/>
    </p>

8.  `gcloud compute instances stop <Instance_Name>`**: VM instance 중지**

    &nbsp; &nbsp; `gcloud compute instances stop <Instance_Name>`를 통해 실행되고 있는 VM Instance를 정지 시킬 수 있습니다.

    <p align="center">
      <img src="assets\2021-09-27\15.png"/>
    </p>

    &nbsp; &nbsp; 이후, 꺼졌는지 확인하기 위해 `gcloud compute instances list`를 통해 status를 확인할 수 있습니다. 확인해보면 `k80-gpu-instance`가 아래와 같이 꺼진 것을 알 수 있습니다.

    <p align="center">
      <img src="assets\2021-09-27\16.png"/>
    </p>

## Useful Command

1. nvidia Driver 설치 여부 및 버전 확인

   &nbsp; &nbsp; nvidia driver 설치 여부와 버전 확인을 하기 위해선 아래와 같은 command를 입력하면 됩니다.

   ```bash
   cat /proc/driver/nvidia/version
   ```

   &nbsp; &nbsp; 위의 command를 입력하게 되면 아래와 같은 출력 화면을 볼 수 있습니다.

    <p align="center">
      <img src="assets\2021-09-27\17.png"/>
    </p>

   &nbsp; &nbsp; Google Cloud Platform(GCP)은 **Debian os를 사용**하기 때문에 **GCC version이 Debian에 맞춰져 있는 것**을 볼 수 있습니다. 하지만 위의 코드 보단 `nvidia-smi`를 **사용하는 것을 선호**한다고 합니다! `nvidia-smi`의 설명은 "**2. 사용 GPU 확인**"에서 볼 수 있습니다.

2. 사용 GPU 확인

   &nbsp; &nbsp; 사용하고 있는 gpu를 확인하기 위해선 아래와 같은 command를 입력하면 됩니다.

   ```bash
   nvidia-smi
   ```

   &nbsp; &nbsp; `nvidia-smi`를 command로 입력하게 되면 cuda version과 GPU 종류 등등 다양한 정보를 얻을 수 있습니다. 아래 그림은 `nvidia-smi`에 대한 결과 화면입니다.

   <p align="center">
      <img src="assets\2021-09-27\18.png"/>
    </p>

3. cuda 버전 확인

   &nbsp; &nbsp; **cuda 버전을 확인**하기 위해선 3가지 방법이 존재합니다.

   ```bash
   # 1.
   nvidia-smi

   #2.
   nvcc --version
   #or nvcc -V

   #3.
   cat /usr/local/cuda/version.txt
   ```

   1. `nvidia-smi`

      <p align="center">
          <img src="assets\2021-09-27\18.png"/>
      </p>

   2. `nvcc --version`(=`nvcc -V`)

      <p align="center">
          <img src="assets\2021-09-27\19.png"/>
      </p>

   3. `cat /usr/local/cuda/version.txt`

      <p align="center">
          <img src="assets\2021-09-27\20.png"/>
      </p>

4. cudnn 버전 확인

   &nbsp; &nbsp; cudnn 버전을 확인하기 위해선 아래와 같은 command를 입력하면 됩니다!

   ```bash
   cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
   ```

   &nbsp; &nbsp; GCP 기준(os: Debian GNU/Linux 10)으로 위와 같이 입력하면 됩니다. 다른 블로그나 포스트를 보게 되면 아래와 같이 입력 되어 있는데 다른 이유는 **아마 운영체제가 달라서**(?) 그런 것 같습니다. 혹시 아시는 분 있으면 댓글 부탁드립니다😉

   ```bash
   cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
   ```

   &nbsp; &nbsp; Google Cloud Platform(GCP)기준으로 첫번째로 보여드린 명령어를 입력하면 아래와 같은 출력이 나옵니다.

    <p align="center">
      <img src="assets\2021-09-27\21.png"/>
    </p>

   &nbsp; &nbsp; 따라서 위의 VM instance의 경우 **cudnn version이 8005**가 됩니다!😎

5. GCP VM instance os 버전 확인

   <p align="center">
     <img src="assets\2021-09-27\22.png"/>
   </p>

   &nbsp; &nbsp; 위의 화면을 통해 운영체제가 **Debian GNU/Linux 10**이라는 것을 알 수 있습니다. 위의 화면에서 나온 `*-release`는 버전 확인을 하는데 사용되며 아래의 command를 입력시켜주면 **os-release**라는 파일 이라는 것을 알 수 있습니다.

   <p align="center">
     <img src="assets\2021-09-27\23.png"/>
   </p>

6) pytorch 버전 확인

   <p align="center">
     <img src="assets\2021-09-27\24.png"/>
   </p>

   &nbsp; &nbsp; `torch.__version__`을 통해 pytorch version을 알 수 있습니다. 위의 출력을 보면 pytorch 버전이 1.9.0인 것을 알 수 있습니다.

&nbsp; &nbsp; 워낙 방대한 양의 내용을 다루다 보니 설명이 길어졌는데요! 주기적으로 추가할 내용이 있다면 기술하겠습니다. 다음시간에 봐요~!😎

## Reference

- cuda, cudnn, nvidia GPU 확인
  - cuda 버전, nvidia GPU: [https://vense.tistory.com/394](https://vense.tistory.com/394)
  - cudnn 버전 확인(Get inspiration to detect cudnn version from this blog!!😊): [https://driz2le.tistory.com/264](https://driz2le.tistory.com/264)
- VM Instance
  - How to edit the configurations in VM Instance: [https://blog.dalso.org/google-cloud-platform-2/nas-project/9232](https://blog.dalso.org/google-cloud-platform-2/nas-project/9232)
  - How to transfer the files(source code files and the other files)from local to VM instance(solution: `gcloud compute scp 'LOCAL_FILE_PATH' 'VM_NAME'`): [https://cloud.google.com/compute/docs/instances/transfer-files#transfergcloud](https://cloud.google.com/compute/docs/instances/transfer-files#transfergcloud)
- Metadata
  - What is Meta data?: [https://velog.io/@hyeseong-dev/메타데이터란](https://velog.io/@hyeseong-dev/%EB%A9%94%ED%83%80%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%9E%80)
- Google Cloud Command
  - Introduction for Google Cloud shell and show many commands by using `gcloud compute`(including `gcloud compute ssh` ): [https://www.waytoliah.com/1444](https://www.waytoliah.com/1444)
  - **A Documentation** for All of the `gloud` commands: [https://cloud.google.com/sdk/gcloud/reference/compute](https://cloud.google.com/sdk/gcloud/reference/compute)
  - Many useful and frequently used `gcloud` commands: [https://codechacha.com/ko/gcp-command/](https://codechacha.com/ko/gcp-command/)
  - How to run VM instances and stop them(`gcloud compute instances (start` or `stop) <Instance_Name>`): [https://cloud.google.com/compute/docs/instances/stop-start-instance](https://cloud.google.com/compute/docs/instances/stop-start-instance)
- Setting Development Environment in Vscode
  - Install the Google Cloud SDK & show description for connecting VS code and GCP: [https://gre-eny.tistory.com/333?category=993806](https://gre-eny.tistory.com/333?category=993806)
- SSH
  - How to generate SSH key in WSL2: [https://wslhub.com/wsl-firststep/firststep/sshkey/](https://wslhub.com/wsl-firststep/firststep/sshkey/)
  - Description for the basic concepts of SSH Key and how to generate SSH key file in Unix line: [https://jsparkdev.tistory.com/9](https://jsparkdev.tistory.com/9)
  - How to access GCP vm instance in the shell by using ssh: [https://mentha2.tistory.com/210](https://mentha2.tistory.com/210)
  - The whole process for accessing Google Cloud VM Instance with `glcoud compute ssh 'INSTANCE_NAME'` command line: [https://gurumee92.github.io/2020/09/gce-인스턴스-생성-및-설정하기/](https://gurumee92.github.io/2020/09/gce-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/)
  - Make default-allow-ssh firewall: [https://cloud.google.com/compute/docs/troubleshooting/troubleshooting-ssh#check_firewall](https://cloud.google.com/compute/docs/troubleshooting/troubleshooting-ssh#check_firewall)
