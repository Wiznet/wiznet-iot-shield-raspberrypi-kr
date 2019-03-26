# WIZnet IoT Shield for Raspberry Pi

이 저장소에는 다음 자료들이 포함되어 있습니다.
* [Raspberry Pi][link-raspberry-pi]의 RNDIS 기능을 활용한 Cat.M1 응용 개발 환경을 구축하는 **개발 시작 가이드**
* RNDIS 기능의 확장 활용을 위한 **가이드 문서**
* 가이드 문서의 응용을 직접 프로그램하고 확인 할 수 있는 **예제 코드**


![][hw-raspberrypi-connect-qc01]

## Folder Structure

### /docs
Raspberry Pi를 기반으로 WIZnet IoT shield의 Cat.M1 통신을 활용하는 응용 장치 구현 시 참고할 수 있는 문서가 위치합니다.
* imgs: 문서에서 활용된 이미지들이 저장된 폴더입니다.

### /samples
Raspberry Pi 및 PC에서 동작 시킬 수 있는 예제 코드가 위치합니다.

## Documentation
개발 시작 가이드 및 응용 가이드 문서는 다음과 같습니다.

* 공통 가이드
  * [Quickstart Guide](https://github.com/Wiznet/wiznet-iot-shield-kr/blob/master/docs/quickstartguide_standalone_mode.md)
  * [Hardware Guide](https://github.com/Wiznet/wiznet-iot-shield-hardware-kr/blob/master/docs/wiot_hw_overview_n_settings.md)

* 개발 환경 구축 및 시작하기
  * [Raspberry Pi 기반으로 Cat.M1 디바이스 개발 시작하기][raspberrypi-get-started]

* 활용 가이드
  * [Raspberry Pi와 Cat.M1 RNDIS를 활용한 사진 전송 가이드][raspberrypi-guide-bg96-rndis]

## Other WIZnet IoT Shield Repository
**[wiznet-iot-shield](https://github.com/Wiznet/wiznet-iot-shield-kr/)** 저장소를 방문하면 다른 플랫폼 보드를 기반으로 동작하는 다양한 예제에 대한 저장소 리스트를 확인 할 수 있습니다.


## Support

[![WIZnet Developer Forum][forum]](https://forum.wiznet.io/)

**[WIZnet Developer Forum](https://forum.wiznet.io/)** 에서 전세계의 WIZnet 기술 전문가들에게 질문하고 의견을 전달할 수 있습니다.
지금 방문하세요.


## License
**WIZnet IoT Shield for Raspberry Pi** 저장소의 모든 문서와 예제는 [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)으로 배포됩니다.


[link-raspberry-pi]: https://www.raspberrypi.org/

[forum]: ./docs/imgs/forum.jpg

[hw-raspberrypi-connect-qc01]: ./docs/imgs/hw/wiot-shield-qc01-raspberrypi.png
[hw-raspberrypi-connect-wm01]: ./docs/imgs/hw/wiot-shield-wm01-raspberrypi.png

[raspberrypi-get-started]: ./docs/Raspberrypi_get_started.md
[raspberrypi-guide-bg96-rndis]: ./docs/Raspberrypi_guide_all_rndis-datatransfer.md
