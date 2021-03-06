### KVM-QEMU를 이용해 가상머신을 사용하는 이유

보통 가상 머신에서 구동되는 OS는 그래픽 오버헤드 데이터가 많아 버벅이는 느낌이 많았습니다 이 문제를 해결하는 것이 바로 Passthrough인데요 
GPU 패스스루를 이용해 외장 그래픽을 가상 머신에 직접 연결하면 윈도우 10 기준 약 95%, 맥오에스 기준 99%의 성능이 발휘됩니다
가장 처음 KVM-QEMU PT에 관심을 가졌던 이유는 콜드 부팅없이 운영체제를 오갈 수 있는 점이 참 매력적이었고, 더불어 랜섬웨어로 부터 주-컴퓨터를 보호할 수 있는 점 또한 뿌리치기 힘든 마성을 가지고 있었습니다 다만, 윈도우 10은 100% PT 사용이 가능하지만 맥오에스는 제한적인 PT 장치 추가가 다소 아쉽지만 실 사용에 있어선 크게 불편한 점을 느끼지 못하고 있습니다 **특히, 오픈코어 부트로더**를 사용할 경우 더욱 안정적인 오에스 부팅및 장치 활용이 가능합니다
<br/>

### 한국어 관련 피드백 안내

이 자료는 Kholia/OSX-KVM을 기반으로 보다 쉬운 카탈리나 10.15.1 설치를 목적으로 하고 있습니다
보다 자세한 방법과 설치중 오류 발생 관련 문제는 [식스플로우](https://sixflow.kr) 질문/답변 게시판을 이용해 주세요
<br/>

### 설치 필수 요구 사항

* 리눅스 배포판이 필요합니다(서버/웍스/데스크탑 버전 구애 받지 않음) 이를테면 Ubuntu 18.04 LTS 64-bit등을 꼽을 수 있습니다
* QEMU 버전은 2.11.1 이상이어야 사용 가능합니다
* Intel은 VT-x / AMD는 SVM을 지원해야 하며 CMOS 바이오스에서 반드시 활성화 시키도록 합니다
* SSE4.1과 AVX2를 지원하지 않는 경우 카탈리나(10.15.x)는 설치되지 않습니다
* 카탈리나 운영체제 사용은 하스웰 리프레시부터 사용 가능합니다
* 리눅스 설치 과정에서 반드시 스왑 파티션을 만들어 주세요 (64GB 메모리 기준 5GB정도면 됩니다)
* **처음 설치만 Q35 칩셋으로 설치하고 안정화는 i440fx 칩셋으로 작업 해야합니다**
* **식스플로우 팁 게시판에 안정화 관련 글 타래는 모두 포스팅 되어 있습니다**
<br/>

___
### 기능 작동 (World first i stabled a hackintosh system)

* AMD RX 5700XT H264, H265 가속 활성화 (다빈치 리졸브, 파컷 사용 가능)
* 넷플릭스 1080P - 파이어폭스 넷플릭스 애드온
* 아이메세지, 페이스타임, 앱스토어, 연속성 사용 가능 (리얼맥 처럼 여러개의 장치에 로그인해도 자동 해제되지 않음)
* BCM4360, I211, I219V PCI 패스스루 활성화
* 스카이레이크 X 정상 작동 (vCPU 8~10)
* Intel 300 seriese USB 3.0/2.0 - 8086:a2af 활성화 가능
* 최대 64코어 128 스레드 사용 가능 (DSDT 편집 요망)
* 내장 사운드 ALC1220A 정상 작동
* USB 추가 전력 출력 가능
* **오픈코어 부트로더 0.5.3의 최신 버전은 식스플로우 자료실에 정기적으로 업데이트 됩니다**
<br/>

### 기능 미 작동

* 가상머신 게스트 OS 특성상 맥오에스 잠자기는 지원하지 않습니다
___
<br/><br/>

### 설치 가이드

```
$ cd ~
$ git clone https://github.com/zisqo/OSX-KVM.git
```
* 홈 폴더에 OSX-KVM 관련 파일을 다운로드 합니다
<br/>

```
$ sudo apt-get install qemu uml-utilities virt-manager dmg2img git wget libguestfs-tools
```
* QEMU 및 관련 필수 패키지를 다운로드 합니다
<br/>

```
$ sudo -s
$ cd OSX-KVM
$ echo 1 > /sys/module/kvm/parameters/ignore_msrs
$ sudo cp kvm.conf /etc/modprobe.d/kvm.conf
```
* MSR lock 관련 패치를 진행합니다
<br/>

```
$ sed -i "s/CHANGEME/$USER/g" macOS.xml
```
* macOS.xml의 홈 계정을 현재 접속한 계정으로 변경합니다
* 절대 root 계정으로 접속해 위 명령을 실행하지 말아주세요
<br/>

```
$ virsh define macOS.xml
```
* /home/사용자계정/OSX-KVM 폴더에서 위 명령을 실행해 xml 파일을 virt-manager에 등록합니다
* 식스플로우에서 다운로드 받은 사전 설치 디스크(24GB)는 home/사용자계정/OSX-KVM 폴더에 배치해 주세요
* **사전 설치 디스크(24GB)는 모든 애플 제품에 복원해 사용할 수 있습니다(해킨토시화된 이미지가 아닙니다)**
* 가상 머신에서 네트워크 설정은 인텔 I211 어댑터와 BCM4360을 패스스루 할 것이므로, 별도 설정을 진행하지 않습니다
* ZISQO의 깃허브에서 배포중인 클로버 부트로더는 2560x1440 해상도를 기준으로 부팅됩니다
* TianoCore 설정의 OVMF 해상도를 2560x1440으로 표시하려면 HDMI 포트로 연결해야 합니다
* **클린 설치를 진행할 경우 이 명령은 지금 실행하지 않습니다**
<br/>
<br/>

___
* 클린 설치를 시도한다면 반드시 앱스토어에서 다운로드 받은 이미지(8GB)를 이용해 설치해 주세요
* 저용량 설치 디스크는 swdn.apple.com에서 VPN 연결 불가할 경우 패키지 오류#8을 뿜어냅니다
* APFS 부트 패치를 할 필요가 없으며 클린 설치 시도할 때 HFS로 포맷해도 APFS로 변경됩니다
*  마우스 좌표가 디스플레이 되는 영역이 다를 수 있지만 익숙해지면 생각보다 쉽게 사용 가능합니다
___
<br/>

```
$ sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/xxx
```
* 앱스토어에서 다운로드 받은 파일을 USB에 복원해야 합니다
* 명령어 마지막 문자 xxx는 USB 드라이브 이름을 지정 해주면 됩니다
* 클린 디스크 복원 과정엔 어떠한 패치도 들어가지 않습니다
<br/>

```
$ qemu-img create -f qcow2 mac_hdd_ng.img 128G
```
* 가상 머신이 설치될 디스크를 생성합니다
* 만약 식스플로우에서 사전 설치본 (24G)을 다운로드 받는다면 이 과정을 패스해 주세요
* 맥오에스를 설치할 경우 Virt-IO를 이용한 NVMe 설치를 불가하니 img 파일을 NVMe에 복사해 주세요
* 그렇지 않을경우 SSD 디스크에 복사할 것을 권장합니다
<br/>

```
$ virsh define macOS.xml
```
* 가상 머신 관리자(Virt-Manager)에 macOS.xml을 등록한 다음
* 가상 머신 관리자의 가상 머신 속성에서 하드웨어 추가를 선택한 다음
* USB 디스크를 연결할 PCI 호스트 장치에서 검색해 등록해 주세요
* **USB 장치를 직접 선택하는 방법은 패스스루가 아님을 유의합니다**
<br/>

기타 GPU 패스스루및 PCI 장치 패스스루는 [식스플로우](https://sixflow.kr) 팁 게시판을 이용해 주세요
<br/>
<br/>
<br/>

### 참고 사이트

* https://sixflow.kr

* https://github.com/kholia/OSX-KVM

* https://github.com/foxlet/macOS-Simple-KVM

* http://www.contrib.andrew.cmu.edu/~somlo/OSXKVM/

* https://www.kraxel.org/blog/2017/09/running-macos-as-guest-in-kvm/
