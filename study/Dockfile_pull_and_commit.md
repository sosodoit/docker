**목표 :  Dockerfile을 이용한 도커 이미지 운영 & YOLOv5-custum-Dockerfile**
<br><br>
 
# **#1 Dockerfile**
왜 도커를 쓰는가!
- A가 구축한 환경을 도커파일을 통해 B에게 줄 수 있다. B는 도커파일을 빌드하기만 하면 환경을 재구축할 필요가 없다.
- 도커엔진을 구축만 해놓으면 된다.
    - HOW RUN : 도커허브에서 알아서 필요한 것을 다운받고 사전에 정의해 놓은 환경설정 등을 단계적으로 진행하게 된다.
- 도커파일관리 : 도커파일을 수정 및 재가공 하여 사용할 수 있다.
    - 도커파일v1에서 추가사항이 생겼고(라벨을 통해 주석처리) 도커파일v2에서 라벨을 통해 이런 추가사항이 있었구나를 확인할 수 있겠다.

<br><br>
**["sudo docker pull ubuntu:latest" 명령어를 통해 최신버전의 우분투를 받아온 것을 Dockerfile을 빌드하면 적용되도록 만든 것]**

``` Powershell Ubuntu
$ mkdir dockr
$ cd docker/
$ vim Dockerfile
$ cat Dockerfile
---
FROM ubuntu:latest # 도커허브에서 관련된 이미지를 다운로드 받겠다
LABEL ksh # 주석과 같은 용도로 생각
CMD ["echo", "Hello Dockerfile"] # 실행할 명령
RUN apt-get update # RUN이라는 명령어로 추가설치
# 저장 후 나가기 ESC > :wq > Enter
---

$ sudo docker build -t ubuntu:v1.0 . # 도커 빌드, -t는 태그옵션, v1.0으로 관리하겠다, . 현재 디렉토리에있는 도커파일을 사용하겠다
$ sudo docker images # 이미지 목록 조회
$ sudo docker run ubuntu:v1.0 # 실행시키면 정의해놓은 것이 실행됨 (like 자동 스크립트)
Hello Dockerfile
```

**[ pull해서 commit하는 방법 ]**
``` Powershell Ubuntu
sudo docker pull pytorch/pytorch
sudo docker images
sudo docker run -it --rm pytorch/pytorch /bin/bash # --rm 1회성 생성으로 도커 끄면 날라감

## bash 쉘 안으로 진입하게 되고 
python --version # 3.7.9
python
>>> import torch
>>> torch.__version__ # 1.7.1
exit()
pip list # 설치 되어있는 목록 조회

## 여기서 필요한거 추가 설치 하면 된다.
## 이 환경을 저장하려면 새로 터미널 열고 명령어 실행
sudo docker ps
sudo docker commit [container id] ksh/pytorch-custom # ksh(사용자이름)/이름지정으로 저장
sudo docker images # 저장된 이미지 확인

## 다시실행
sudo docker run -it --rm ksh/pytorch-custom /bin/bash

## 버전관리
sudo docker commit [container id] ksh/pytorch-custom:v1.0 /bin/bash # :v1.0 하면 v1.0으로 태깅이 된다.
```

> ## Remember
> 1. GPU 사용 : NVIDIA 도커 pull 
> 2. 원하는 도커 컨테이너 pull
> 3. 내가 추가로 설치하고 싶은 패키지 설치 후 저장
> 4. 팀배포

<br><br>

# **#2 YOLOv5-custum-Dockerfile**

``` Powershell Ubuntu
# 이미지 가져오기
sudo docker pull ultralytics/yolov5:latest
# 컨테이너 실행#1
sudo docker run --ipc=host -it ultralytics/yolov5:latest
# 로컬 파일 액세스#2
sudo docker run --ipc=host -it -v "$(pwd)"/coco:/usr/src/coco ultralytics/yolov5:latest
# GPU 액세스 권한#3
sudo docker run --ipc=host --gpus all -it ultralytics/yolov5:latest
# 명령 실행
$ python train.py  # train a model
$ python test.py --weights yolov5s.pt  # test a model for Precision, Recall and mAP
$ python detect.py --weights yolov5s.pt --source path/to/images  # run inference on images and videos
```

* [hub.docker.com](https://hub.docker.com/search?type=image)
* teddyle777/dl-cuda11
* teddyle777/kaggle-ko