# Jetson Nano

Jetson Nano Developer Kit 4GB (SD Card O)

Jetson Nano JetPack : 4.6.0

Python Version : 3.8.12

Jetson Nano는 가상 메모리가 2G로 OpenCV 와 YOLOv5를 활용하기에는 많은 어려움이 있다. 따라서 이러한 부분을 해결하고자. 가상메모리를 확장을 해야 하는데, 여러가지 방법이 있지만 가장 편리한 방법이 아래와 같음으로 이 방법을 사용해보자.

### 1. Jetson Nano Memory Swap 방법

1. 깃클론을 진행한다.

```python
git clone https://github.com/JetsonHacksNano/installSwapfile
```

1. installSwapfile Folder로 이동한다.

```python
cd installSwapfile
```

1. installlSwapfile을 설치한다.

```python
./installSwapfile.sh
```

1. 재부팅 진행한다.

```python
sudo reboot
```

1. SwapMemory 확인을 한다.

```python
free -m
```

Swap total에 8G가 뜬다면 성공!

### 2. Jetson Nano Python 3.8.12 Install

Jetson nano에는 기본적으로 Python 3.6을 지원한다. 하지만 최근 YOLOv5에서 Python3.6 지원을 중단하면서 설치에 많은 어려움이 있었다. 이 부분을 구글링을 통해서 해결할 수 있었고, 그 해결방안이 Python3.8을 설치하는 것이다.  따라서 아래 코드를 통해 설치를 진행해보자.

1. Update & Upgrade

```python
sudo apt update
sudo apt upgrade
```

1. 필요한 패키지 설치를 진행

```python
sudo apt-get install build-essential python-dev python-setuptools python-pip python-smbus
sudo apt-get install libncursesw5-dev libgdbm-dev libc6-dev
sudo apt-get install zlib1g-dev libsqlite3-dev tk-dev
sudo apt-get install libssl-dev openssl
sudo apt-get install libffi-dev
```

1. Python 3.8.12 소스코드 설치

```python
wget https://www.python.org/ftp/python/3.8.12/Python-3.8.12.tar.xz
```

1. Python 3.8.12 압출 풀기

```python
tar xvfz Python-3.8.12.tgz
```

1. Python 3.8.12 파일 열기

```python
cd Python-3.8.12
```

1. Build 진행

```python
./configure
make -j4
```

1. 마무리 설치 (install과 헷갈리지 않기) ***

```python
sudo make altinstall
```

install 대신 altinstall을 사용하는 이유 

기존에 Python 3.6이 설치되어있는데 make install을 진행하게 되면 Python3.6을 덮어 씌워버리게 되고, 나중에 pip등 많은 것들이 꼬이게 된다. 따라서 기존 Python3.6 Version을 덮어씌우지 않고 별도의 Python3.8을 설치하기 위해 altinstall 을 사용해야한다.

1. 설치한 Python Version 확인

```python
python3.8
```

Python3.6과 분리하여 설치하였기 때문에 Python3.8을 실행하고자하면, python3가 아닌 python3.8이라고 명령어를 입력해야한다.

### 3. Jetson - stats 설치

Jetson-stats 는 Jetson Nano의 종합적인 Stat을 기존의 수많은 터미널 명령어로 확인하는 불편함이 있었으나, TUI(Text User Interface)형태로 편리하게 이용이가능하게 됨.

1. jetson - stats 관련 패키지 설치

```python
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python-pip
sudo -H pip install -U jetson-stats
sudo reboot
```

1. jtop 실행 (설치확인을 위한 코드) → 3번진행 전, Q를 통해 나온다.

```python
jtop
```

1. OpenCV 4.5.0 with Cuda

```python
wget https://github.com/Qengineering/Install-OpenCV-Jetson-Nano/raw/main/OpenCV-4-5-0.sh
sudo chmod 755 ./OpenCV-4-5-0.sh
./OpenCV-4-5-0.sh
```

위 코드의 경우 Swap된 메모리에 따라 다르지만, 대략 2시간 가량 시간이 걸림. 

1. PyTorch 1.8 + torchvision v0.9.0

```python
# PyTorch 1.8.0 다운로드 및 dependencies 설치
wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl -O torch-1.8.0-cp36-cp36m-linux_aarch64.whl
sudo apt-get install python3-pip libopenblas-base libopenmpi-dev 
 
# Cython, numpy, pytorch 설치
pip3 install Cython
pip3 install numpy torch-1.8.0-cp36-cp36m-linux_aarch64.whl
 
# torchvision dependencies 설치
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
git clone --branch v0.9.0 https://github.com/pytorch/vision torchvision
cd torchvision
export BUILD_VERSION=0.9.0
python3 setup.py install --user
cd ../  # attempting to load torchvision from build dir will result in import error
```

### 4. Jetson Nano Yolov5 Install

1. github에서 기본 repository 받아오기

```python
git clone https://github.com/ultralytics/yolov5
cd yolov5
 
# yolov5s.pt weight 다운로드
wget https://github.com/ultralytics/yolov5/releases/download/v6.0/yolov5s.pt
```

1. 설치된 Yolov5 위치에 들어가서 requirements.txt 파일 수정

```python
# 다음 내용 requirements.txt에서 제거
 
numpy>=1.18.5
opencv-python>=4.1.2
torch>=1.7.0
torchvision>=0.8.1
```

1. pip 업그레이드 이후 수정한 requirements.txt 설치 진행

```python
python3.8 -m pip install --upgrade pip
 
python3.8 -m pip install -r requirements.txt
```

이때, 중요한 점 이 바로 python3이 아닌 python3.8이라는 점 유의바란다. 

### 느낀점

Jetson Nano Emmc JetPack 설치에 문제가 생기면서 오랜 시간이 걸렸다. (1주일 넘게) 결국 Emmc 보드는 포기를 한체 Jetson Nano 메모리카드가 있는 보드를 빌려와서 진행을 함으로써 빠르게 해결할 수 있었다. 

하지만 임베디드는 Version 0.0.1 차이로 Error가 발생하기도 한다는 점을 몸소 느끼면서 정말 많이 힘들었다. 특히, 가장 최신의 Jetson Nano image 파일을 설치하게 되면 4.6.1 을 수 있었고, 이 image 파일을 통해 앞에 적힌 방식대로 진행을 하였다. 하지만 계속되는 pytorch 설치 오류로 정말 미치는 줄 알았다. (image 재설치만 30번 이상 반복…) 결국 서칭을 통해 4.6.1 버전의 경우 pytorch 지원이 안된다는 점을 알게되었고…  여기에서만 3~4일 정도를 보냈던것 같다. 그래서 이점 유의하고 독자들은 4.6.0 image 설치후, 문제점을 즉시 해결할 수 있었다.

위 앞까지가 90%정도이고, 나머지 10%로 설치에 있어서도 문제점이 발생하였다. jetson Nano 의 기본적인 Python은 3.6으로 설치되어 진행된다. 그러다보니 Yolov5의 requirments.txt 파일 설치 오류가 계속 발생하였고, 이 오류가 YOLOv5에서 python3.6을 지원을 중단하게 되면서 발생 하다는 것을 알 수 있었다. 그래도 이 오류는 Jetson Nano를 설치하면서 직접적으로 느꼈던거라 바로 찾을 수 있었고, 금방 해결이 가능했다. 

임베디드 시스템 자체가 어렵다고는 느껴지지는 않았다.하지만  임베디드의 진짜 어려운점은 운영체제를 설치하고 이와 같은 버전 문제가 많이 발생하다는 것을 알 수 있었다. 따라서 이러한 점을 유의하고 앞으로 개발에 임해야겠다.

https://makingrobot.tistory.com/83
