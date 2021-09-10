**목표 : Windows10에 WSL2 설치 & WSL2에서 Ubuntu와 CUDA 사용 (feat.도커)** - *WSL : 리눅스용 윈도우 서브시스템*<br><br>
 
# **#1 Windows10에 WSL2 설치**

``` PowerShell
# 1단계 
- 윈도우에서 리눅스를 설치하기위해 "WSL" 옵션 기능을 사용하도록 설정 필요

$ dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# 2단계 
- WSL2 실행(업데이트)을 위한 요구 사항 확인
    - x64 시스템의 경우: 버전 1903 이상, 빌드 18362 이상
    - (18362보다 낮은 빌드는 WSL 2를 지원하지 않음) 
    - Windows 로고 키 + R > winver > 버전 ****(OS 빌드 *****.****) 확인 가능

# 3단계 
- WSL2를 설치하려면 Virtual Machine 플랫폼 옵션 기능 설정 필요(이 기능을 사용하려면 컴퓨터에 가상화 기능이 필요)

$ dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# 재부팅 필요

# 4단계
- WSL2로 업데이트를 위해 리눅스 커널 업데이트 패키지를 다운로드 하고 실행
- [x64 머신용 최신 WSL2 Linux 커널 업데이트 패키지]

# 5단계
- 리눅스(Ubuntu) 설치 후 사용자 계정 및 암호 설정

sudo apt update && sudo apt upgrade

# 6단계
- WSL2를 기본 버전으로 설정하기 위함

$ wsl --list --verbose
$ wsl --set-version Ubuntu 2
$ wsl --set-default-version 2

# 확인
$ uname -r 
```

**Only code**
``` PowerShell 
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
# 재부팅 > 커널실행 > 우분투설치
sudo apt update && sudo apt upgrade
wsl --list --verbose
wsl --set-version Ubuntu 2
wsl --set-default-version 2
```
<br><br>

# **#2 WSL2에 도커 설치**

## STEP1
[NVIDIA Drivers for CUDA on WSL](https://developer.nvidia.com/cuda/wsl/download)
리눅스용 NVIDIA 그래픽 드라이버 설치
## STEP2
WSL에 Docker 설치 - [튜토리얼](https://docs.docker.com/engine/install/ubuntu/#uninstall-old-versions)

``` PowerShell Ubuntu
# 이전에 설치된 도커가 있다면 삭제
sudo apt-get remove docker docker-engine docker.io containerd runc

## 사전준비
# Set up the repository
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release

# Add Docker’s official GPG key:
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set up the stable repository
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

## 도커설치
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

## 테스트
sudo docker run hello-world
```
<br><br>

# **#3 WSL2에서 Ubuntu와 CUDA 사용**
## STEP3
NVIDIA Container Toolkit 설치

``` PowerShell Ubuntu
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container-experimental.list | sudo tee /etc/apt/sources.list.d/libnvidia-container-experimental.list
```
- 변수 distribution을 설정하고, NVIDIA 레포지토리 GPG 키를 가져온 뒤, NVIDIA 레포지토리를 Ubuntu의 apt 패키지 매니저에 추가합니다.

```
sudo sed -i "s/archive.ubuntu.com/mirror.kakao.com/g" /etc/apt/sources.list
```
- Ubuntu의 기본 레포지토리를 한국 서버로 변경합니다. apt update나 apt upgrade 등의 작업이 훨씬 빨라집니다.

```
sudo apt update && sudo apt install -y nvidia-docker2
```
- Ubuntu의 apt 레포지토리를 초기화한 뒤, NVIDIA 런타임을 설치합니다.

``` Powershell
wsl.exe --shutdown Ubuntu
```
- 설치 완료 시, 모든 Ubuntu 터미널을 종료하고, Powershell 터미널을 열어 모든 Ubuntu WSL 인스턴스를 종료합니다

``` PowerShell Ubuntu
sudo service docker start
```
- 새로운 Ubuntu 터미널을 열어, Docker 서비스를 시작합니다.