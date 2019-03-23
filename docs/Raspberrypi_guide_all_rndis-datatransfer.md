# RaspberryPi와 Cat.M1 RNDIS를 활용한 사진 전송 가이드

## 목차

-   [시작하기 전에](#Prerequisites)
-   [소개](#Step-1-Overview)
-   [주요 소스 코드](#Step-2-SourceCode)
-   [동작 영상](#Step-3-Video)
-   [속도 검증](#Step-4-verification)

<a name="Prerequisites"></a>
## 시작하기 전에

> * 하드웨어 설정과 개발환경 구축은 **[Raspberry Pi 기반으로 Cat.M1 디바이스 개발 시작하기][raspberry-pi-getting-started]** 문서에 상세히 설명되어 있습니다.

> * Cat.M1과 같은 Cellular IoT 디바이스는 통신 서비스 사업자의 운영 기준 및 규정에 따라 모듈 펌웨어 및 동작 방식에 차이가 있을 수 있습니다. 본 문서는 한국 **[SK Telecom Cat.M1 서비스][skt-iot-portal]** 를 기준으로 작성되었습니다.


### Development Environment
* **[Raspbian OS][link-raspbian]**
* **Python 2.72**

### Hardware Requirement

| MCU Board | IoT Shield Interface Board |
|:--------:|:--------|
| [Raspberry Pi 2 Model B][link-raspberry-pi purchase] | WIoT-QC01 (BG96)<br>WIoT-WM01 (WM-N400MSE)<br>WIoT-AM01 (AMM5918K) |

<a name="Step-1-Overview"></a>
## 소개

일반적으로 LPWAN(Low-Power Wide-Area Network)은 저전력 장거리 통신이라는 특성으로 인해 Data Rate이 낮으며 아래 표에서 LPWAN 기술의 최대 전송 속도가 12Bytes~ 375Kbps인 것을 확인할 수 있습니다.

LTE Cat.M1의 경우 Sigfox, LoRa, NB-IoT 보다 최대 전송 속도가 비교적 높기 때문에 실시간 트래킹 서비스 또는 사진 전송, 생체 정보와 같은 중용량 및 실시간 응용에 사용하기 적합합니다.

![][1]

본 가이드에서는 Raspberry Pi와 WIZnet IoT Shield Cat.M1 보드를 이용하여 사진 전송이 가능한지 확인하고 속도를 검증해보도록 하겠습니다.


<a name="Step-2-SourceCode"></a>
## 주요 소스 코드

### 1. RaspberryPi Client

Raspberry Pi에서는 Python 2.72 버전을 사용하며, 국내 SK Telecom과에서 서비스 중인 Cat.M1의 경우 IPv6 주소만 사용하므로 서버 IPv4 주소를 다음과 같이 변환하여 사용해야 합니다.

```python
import socket
from os.path import exists
import time,datetime
import os

HOST = '64:ff9b::de62:adcb'             //Prefix: 64:ff9b
# HOST = '222.98.163.203'               //각 IP 주소를 Hexa값으로 변환 (222->de)
```

전체적인 소스의 흐름은 Raspberry Pi Camera를 이용하여 사진을 찍은 후, 지정된 IP의 서버로 파일 전송을 합니다.

```python
os.system("raspistill -o test.jpg -t 500 -q 5")       //사진 촬영

filename = "test.jpg"                                 //보낼 파일 이름
fileSize = os.path.getsize("test.jpg")                //파일 사이즈 출력
print("File size is : ", str(fileSize))
fileInfo = filename + ","+str(fileSize)
sendFileToServer(fileInfo)                            //sendFileToServer 메소드를 이용하여 파일 전송

```

세부 소스는 하단의 링크를 확인해주세요.


### 2. PC GUI Program
PC 환경에서 사진을 받는 서버는 pyQt를 이용하여 GUI 프로그래밍을 하였으며, Server Open 버튼을 통해 구동할 수 있습니다. GUI 프로그램의 진행 상태 막대를 통해 라즈베리 파이에서 전송 중인 사진의 진행상태를 확인할 수 있고, 수신한 이미지를 확인할 수 있도록 하였습니다.

![][2]

아래와 소스와 같이 TCP 서버는 pyQT 관련 동작과 독립적으로 수행할 수 있도록 Thread로 실행하였습니다.

```python
def runServer():
    print('[Server Start]')
    print("[To terminate, Press 'Ctrl + C']")

    try:
        server = socketserver.TCPServer((HOST,PORT),MyTcpHandler)
        #server.serve_forever()
        threading.Thread(target=server.serve_forever).start()
    except KeyboardInterrupt:
        print('[Terminate]')
```

이미지 및 진행 상태 막태의 Refresh를 위해서 Signal-pyqtSlot() 기능을 사용하였습니다.

```python
class MySignal(QtCore.QObject):
    signal1 = QtCore.pyqtSignal(QtCore.QObject)
    def run(self):
        self.signal1.emit(self)

class MySignal2(QtCore.QObject):
    signal2 = QtCore.pyqtSignal(QtCore.QObject)
    def run(self):
        self.signal2.emit(self)
```

```python
@pyqtSlot()
def imageRefresh(self):
    #self.setupUi.label.setText("첫번째 버튼")
    im = Image.open('download/test.jpg')
    size = (500,500)
    im.thumbnail(size)
    im.save('download/test.jpg')
    self.label.setPixmap(QPixmap("download/test.jpg"))

@pyqtSlot()
def resetProgressbar(self):
    while progressBar_statusValue < 100:
        self.progressBar.setValue(progressBar_statusValue)
    self.progressBar.setValue(100)
```

전체 소스 코드: **[링크 바로가기][link-github]**

<a name="Step-3-Video"></a>
## 동작 영상

동작 영상은 **[Youtube 동영상][link-youtube]**을 참고해 주기시 바랍니다.
![][3]

<a name="Step-4-Verification"></a>
## 속도 검증

 아래 표와 같은 형태로 총 50번 테스트를 진행하였으며, 평균 데이터 전송률은 298.37bps 이었습니다. LoRa Cat.M1 최고 전송 속도에 비해 약 80% 정도의 속도로 데이터를 보낼 수 있는 것을 확인할 수 있었습니다.

![][4]

<a name="Step-5-Finally"></a>
## 마치며

 IoT 분야가 확대되며 LPWAN 기술 응용범위가 기존의 작은 센서 데이터를 보내거나 모니터링 하는 수준의 응용이 아닌 사진을 전송하거나 실시간 위치 추적을 하는 등의 응용으로 늘어나고 있습니다.

LTE Cat.M1의 RNDIS 모드를 활용하면 LPWAN 통신 중 가장 빠른 속도의 데이터 전송이 가능하므로, LoRa 혹은 NB-IoT 기술로 구현하기 어려운 중용량 데이터 전송 응용에 널리 활용 할 수 있습니다.


 [raspberry-pi-getting-started]: https://
 [skt-iot-portal]: https://www.sktiot.com/iot/developer/guide/guide/catM1/menu_05/page_01
 [link-woorinet]: http://www.woori-net.com
 [link-wiznet]: https://www.wiznet.io
 [link-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
 [link-raspberry-pi purchase]: https://www.raspberrypi.org/products/raspberry-pi-2-model-b/
 [link-github]: https://github.com/stkim92/LTECatM1-RaspberryPi-Photo-Transfer-Application
 [link-youtube]: https://youtu.be/xJbm8BLaym0

 [1]: ./imgs/Raspberry_pi_guide_picture_1.png
 [2]: ./imgs/Raspberry_pi_guide_picture_2.png
 [3]: ./imgs/Raspberry_pi_guide_picture_3.png
 [4]: ./imgs/Raspberry_pi_guide_picture_4.png