---
title: "[지상 3팀 김충환] 08/24 업무"
descriptoin : "LNet 자료조사"
date: 2020-08-24 17:55:00 -0400
categories: recruit
---

1) Lustre Network 


-LNet은 가볍고 효율적으로  설계되었으며 RPC 요청 처리를 위한 메시지 전달과 대량 데이터 이동을 위한 RDMA를 지원합니다.


-LNet은 가볍고 다재 다능하며 이더넷, InfiniBand 및 Intel® OPA를 포함한 다양한 네트워크 패브릭에서 작동 할 수 있습니다.


-Lustre의 다른 주요 구성 요소와 마찬가지로 LNet은 Linux 커널 모듈로 구현됩니다.


-서버와 클라이언트를 포함하여 Lustre 파일 시스템의 모든 참가자는 유효한 LNet 구성이 있어야 하며 공통 네트워크 패브릭에서 직접 연결되거나 네트워크 간 라우터를 통해 연결되어야 합니다.


-LNet을 실행하는 컴퓨터 시스템의 네트워크 인터페이스는 노드 식별자 (호스트에 있는 네트워크 장치의 IPv4 주소)와 해당 프로토콜에 대한 프로토콜 식별자 및 네트워크 번호로 주소가 지정됩니다. 형식은 다음과 같습니다.


-[IPv4 주소] @ [LND 프로토콜] [lnd #]





2) 정적 Lnet 구성


-LNet 인터페이스는 lnet커널에 의해 로드 될 때 모듈에 직접 매개 변수를 제공하여 구성 할 수 있습니다 . 이렇게 하려면 일반 modprobe 구성 옵션 디렉토리 (일반적으로 /etc/modprobe.d)에 파일을 만들고 lnet모듈 항목을 추가합니다 . 관례 적으로, Lustre 모듈 구성은 /etc/modprobe.d/lustre.conf에 기록됩니다. LNET 구성을 정의하는 방법은 두 가지가 있습니다 : 첫 번째는 networks, 두 번째는 ip2nets 입니다. networks는 이해하기 가장 쉬우며 매우 간단한 구문을 사용하는 반면 ip2nets관리자는 훨씬 더 복잡한 대신 최대의 유연성을 제공합니다. Networks에 대한 특정 요구 사항이 없는 한 ip2nets구문을 사용하는 것을 권장합니다.


-모듈에 대해 하나 networks 또는 ip2nets매개 변수만 정의 되어야합니다. 두 개 이상이 정의되면 오류가 발생합니다.


-하나 이상의 커널 모듈 구성 파일에서 연속 항목을 통해 정의 된 networks 또는 ip2nets 이 하나 이상 있는 경우 모듈 로더가 읽은 마지막 항목이 사용됩니다. 이 방법을 사용하여 LNet 매개 변수를 정의 할 때 디렉터리 ( RHEL 및 CentOS)에 정의 된 LNet 구성이 하나만 있는지 확인합니다. 





2-1) 정적 Lnet 구성(LNet networks구문)


-기본적인 구문은 다음과 같습니다.
▶ options lnet networks="<lnd><#>(<dev>)[,…]"


-예를 들어 TCP/IP (socklnd) Lnet NID를 Enthernet device eth1에 추가하려면 아래와 같이 작성합니다.
▶ options lnet networks="tcp0(eth1)"


-IP 주소는 구성에 언급되지 않고 장치 이름 만 언급됩니다. 구문은 간단하고 읽기 쉽습니다.


-다음 예에서는 RDMA verbs ( o2iblnd) LNet 인터페이스를 만듭니다 .
▶ options lnet networks="o2ib0(ib0)"


-LNet 트래픽을 전달할 호스트에 둘 이상의 네트워크 인터페이스가 있는 경우 추가 인터페이스를 networks변수에 추가 할 수 있습니다 .
▶ options lnet networks="tcp0(eth1),o2ib0(ib0),o2ib1(ib1)"


-위 예는 세 Lnet 인터페이스를 구성합니다.


-하나의 물리적 인터페이스가 두 개의 LNet에 속하는 구성을 만들려면 각 LNet에 대해 괄호 안에 동일한 물리적 인터페이스를 사용하여 쉼표로 구분 된 형식으로 각 LNet을 지정합니다. 
▶options lnet networks="tcp0(eth1),tcp1(eth1)"


-실제 적용이 제한되어 있기 때문에 일반적인 구성이 아닙니다 (두 LNet이 동일한 서브넷에 있고 동일한 물리적 인터페이스에 연결되므로 이 경우 두 개의 LNet을 생성해도 이점이 없습니다).





2-2) 정적 Lnet 구성(LNet ip2nets 구문)

-Ip2nets 구성 구문은 호스트의 LNET 구성을 생성할 때 정규 표현식을 사용합니다. 관리자는 대상 호스트 네트워크 인터페이스의 IPv4 주소를 기반으로 패턴 일치 규칙을 만들고 첫 번째 일치 규칙을 사용하여 적용 할 구성을 결정합니다. 이러한 방식으로 단일 구성 파일을 이기종 네트워크 환경과 여러 네트워크에 걸쳐 클라이언트와 서버 모두에 적용 할 수 있습니다.


-이러한 유연성은 단순성과 가독성을 희생합니다. Ip2nets의 구문 규칙은 복잡하고 어려울 수 있습니다. 잘못 범위가 지정된 패턴 일치 표현식과 같이 규칙에 도입 된 구문 오류는 오류가 발견되기 전에 배포되는 경우 전체 호스트 모집단의 구성에 광범위하고 부정적인 영향을 미칠 수 있습니다. Ip2nets를 사용하는 것은 결과적으로 더 복잡합니다.


-일반 구문은 다음과 같습니다.
▶options lnet ip2nets="<lnd>[<#>][(<dev>)][, <lnd>[<#>][(<dev>)]] <pattern>[; …]"


-각 규칙은 세미콜론으로 구분되며 lnet시작 시 규칙은 왼쪽에서 오른쪽 순서로 평가됩니다 . 각 LNet의 첫 번째 일치 항목이 구성에 적용됩니다. 모든 고유 LNet이 생성되거나 마지막 규칙이 평가 될 때까지 평가가 계속됩니다. 이를 통해 단일 규칙이 단일 서버에 대해 여러 NID를 정의 할 수 있습니다. 각 NID는 서로 다른 네트워크 인터페이스의 서로 다른 LNet에 대해 하나씩 정의됩니다.


-Ip2nets 예제와 함께 사용하는 방법을 설명하겠습니다 . 다음은 서로 다른 유형의 네트워크 패브릭에 있는 두 개의 서로 다른 LNet에 대한 구성을 설명합니다.
▶ options lnet ip2nets="tcp0(eth1) 10.10.100.*; o2ib0(ib0) 192.168.200.*"


-패턴과 일치하는 IP 주소를 가진 호스트 10.10.100.*는 tcp0 LNet에서 NID 192.168.200.*로 구성되고 패턴과 일치하는 호스트는 o2ib0 LNet 으로 구성됩니다 . 호스트가 두 패턴과 일치하면 두 LNet이 해당 호스트에 대해 구성됩니다.


-다음 예에서 일부 호스트는 eth0를 사용하고 다른 호스트는 Lnet tcp0에 연결 하는 데에 eth1을 사용합니다. 이 구성은 시스템 간에 하드웨어 차이가 있고 다른 NIC를 사용하여 동일한 서브넷에 연결하는 경우에 사용합니다.
▶ options lnet ip2nets="tcp0(eth0) 10.10.100.[1-50]; tcp0(eth1) 10.10.100.[100-200]"


-인터페이스 정의가 생략되면 IP 주소와 일치하는 호스트 인터페이스가 사용됩니다. 따라서 위의 예는 다음과 같이 다시 작성할 수 있습니다.
▶ options lnet ip2nets="tcp0 10.10.100.*"





3) 동적 LNet 구성


-동적 LNet 구성은 강력하고 다양하며 관리자에게 호스트에서 실행중인 LNet 구성을 보고 변경할 수 있는 쉬운 방법을 제공합니다. Lctl 을 사용하여 구성을 변경하기 전에 LNet 모듈을 커널에 로드해야합니다.. Lnet모듈을 로드하려면 다음 modprobe명령을 사용하면됩니다.
▶ modprobe [-v] lnet


-모듈이 로드된 후 lnet configure하위 명령을 실행 하여 커널에서 LNet 서비스를 초기화합니다.
▶ lnetctl lnet configure [--all]


-[--all]플래그를 사용하면, lnetctl은 modprobe option file에 있는 networks 또는 ip2nets에서 참조된 모든 LNET 인터페이스를 로드합니다.


-서버에 다른 구성이 없으면 LNet은 loopback인터페이스에 대한 참조 만 갖게 됩니다 . 
▶ [root@rh7z-pe ~]# lnetctl net show
net:
    - net: lo
      nid: 0@lo
      status: up 


-커널 모듈을 로드하고 lnetctl lnet configure명령을 실행 한 후, lnetctl은 호스트에 새 LNet 인터페이스를 만드는 데 사용할 수 있습니다. 구문 기본 형식은 다음과 같습니다.
▶lnetctl net add --net <lnet name> --if <net interface> [<options> …]

EX: 
▶lnetctl net add --net tcp1 --if eth1
▶lnetctl net add --net o2ib0 --if ib0


-LNet 구성에서 NID를 삭제하려면 다음 lctl net del명령을 사용하면 됩니다.
▶ lnetctl net del --net <lnet name>

EX: lnetctl net del --net tcp1


-현재 구성된 NID를 검토하려면 다음 구문을 사용하면 됩니다.
▶ lnetctl net show [--verbose]

EX: [root@rh7z-pe ~]# lnetctl net show 
net:
    - net: lo
      nid: 0@lo
      status: up
    - net: tcp
      nid: 192.168.207.2@tcp
      status: up
      interfaces:
          0: eth1


-lnetctl 이 옵션없이 호출되면, 명령 shell을 제공합니다.
▶ [root@rh7z-pe ~]# lnetctl
lnetctl > help

Available commands are:
    lnet
    route
    net
    routing
    set
    import
    export
    stats
    peer_credits
    help
    exit
    quit
For more help type: help command-name
lnetctl > net
net {add | del | show | help}
lnetctl > 


-Lnet 커널 모듈이 언로드 되거나 호스트가 재부팅 되면 lnetctl에 의해 적용된 구성 이 손실됩니다. 구성을 보존하기 위해 호스트를 다시 시작할 때 다시 가져올 수 있도록 파일로 export 할 수 있습니다.


-LNet 구성을 저장하려면 lnetctl export또는 lnetctl net show를 사용하면 됩니다. export서브 명령 구문은 다음과 같습니다.
▶ lnetctl export [{file}]

EX: lnetctl export /etc/lnet.conf


-더 간단한 구성을 위해 다음 lnetctl net show명령을 사용하면 됩니다.
▶ lnetctl net show [--net <lnet>] [> {file}]

EX: lnetctl net show --net tcp0 > /etc/lnet.conf


-물론 import명령도 있습니다.
▶ lnetctl import [--add|--del|--show] {file}


-import명령 의 기본 동작은 구성 파일에 설명된 인터페이스를 추가하는 것입니다. 따라서 다음 두 명령은 동일합니다.
▶ lnetctl import --add {file}
lnetctl import {file}


-import --del하위 명령은 YAML 구성 파일에 설명된 스펙과 일치하는 호스트에 구성된 모든 NIDS를 삭제합니다. 명령이 성공하면 shell에 출력을 반환하지 않지만 시스템 콘솔 및 syslog에 기록된 항목이 있습니다.


-예를 들어, lnet.cf 파일의(NID 항목이 포함 된 192.168.207.2@tcp1)경우 다음 명령은 다음과 같습니다.

EX: [root@rh7z-pe ~]# lnetctl import --del lnet.cf 


-이 lnetctl import --show명령은 구성 파일에 정의 된 인터페이스 중 실제로 호스트에 구성된 활성 LNet NID를 식별하는 데 사용됩니다. 일치하는 NID lnetctl import --show가 없으면 출력을 반환하지 않습니다.
▶ [root@rh7z-pe ~]# lnetctl net show
net:
    - net: lo
      nid: 0@lo
      status: up
[root@rh7z-pe ~]# cat lnet.cf 
net:
    - net: tcp1
      nid: 192.168.207.2@tcp1
      status: up
      interfaces:
          0: eth1
      tunables:
          peer_timeout: 180
          peer_credits: 8
          peer_buffer_credits: 0
          credits: 256
[root@rh7z-pe ~]# lnetctl import --show lnet.cf 
[root@rh7z-pe ~]# lnetctl import --add lnet.cf 
[root@rh7z-pe ~]# lnetctl import --show lnet.cf 
net:
    - net: tcp1
      nid: 192.168.207.2@tcp1
      status: up
      interfaces:
          0: eth1