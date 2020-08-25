---
title: "[지상 3팀 김충환] 08/21 업무"
descriptoin : "Lustre에 관한 명령어 조사"
date: 2020-08-21 17:55:00 -0400
categories: recruit
---

1) Lustre 상태 조회 명령어




-Lustre에 관한 모든 정보를 '/proc/fs/lustre' 경로에 있습니다.  
모든 상태 정보는 위 경로의 데이터를 기반으로 보여줍니다.

아래 사진은 lustre-server에서 경로를 조회한 결과입니다.
![image](https://user-images.githubusercontent.com/49121847/91123747-937d2c80-e6d8-11ea-90ab-2f9d62b17cea.png)




-Lustre-storage에서 경로를 조회한 결과입니다.
![image](https://user-images.githubusercontent.com/49121847/91123838-d17a5080-e6d8-11ea-9dea-aa23068801ce.png)
![image](https://user-images.githubusercontent.com/49121847/91123839-d2ab7d80-e6d8-11ea-9c9e-5fe05657c8f9.png)
![image](https://user-images.githubusercontent.com/49121847/91123846-d4754100-e6d8-11ea-854f-cf48028fb2a7.png)




-llstat : Lustre에 관한 정보를 보여주는 명령어
![image](https://user-images.githubusercontent.com/49121847/91123880-e9ea6b00-e6d8-11ea-93ee-5068fc5b8f98.png)




-llobdstat : OST에 관한 정보를 보여주는 명령어
해당 OST가 있는 서버에서만 조회 가능(현재 환경에서는TestPC1, 3)
![image](https://user-images.githubusercontent.com/49121847/91123903-f7075a00-e6d8-11ea-9625-5a9ea9777131.png)




-lctl : Lustre 컨트롤 및 구성에 사용되는 명령어. Lctl을 사용하면 ioctl 인터페이스를 통해 Lustre를 직접 제어할 수 있습니다.
![image](https://user-images.githubusercontent.com/49121847/91123909-ff5f9500-e6d8-11ea-8d04-b8a76dffe77a.png)
![image](https://user-images.githubusercontent.com/49121847/91123936-07b7d000-e6d9-11ea-847b-e6711fcc796a.png)
![image](https://user-images.githubusercontent.com/49121847/91123949-0c7c8400-e6d9-11ea-8419-ab3092576033.png)




-lfs : 사용자 구성 루틴 및 모니터링에 사용 가능.
![image](https://user-images.githubusercontent.com/49121847/91123960-130afb80-e6d9-11ea-8e59-6e9297017848.png)
![image](https://user-images.githubusercontent.com/49121847/91123970-17371900-e6d9-11ea-85af-885a3c80c410.png)






2) 상태 조회 도구를 이용하여 상태 조회




-lctl dl : 디바이스 목록 조회
![image](https://user-images.githubusercontent.com/49121847/91123986-2322db00-e6d9-11ea-864b-c2296abc9c86.png)




-lctl --net tcp peer_list : 연결된 peer 목록 조회  ▶ client와 oss 서버가 조회됨.
![image](https://user-images.githubusercontent.com/49121847/91124003-2d44d980-e6d9-11ea-8733-3482a10e4e76.png)




-lctl --net tcp list_nids : MGS+MDT 가 구성된 서버가 조회됨.
![image](https://user-images.githubusercontent.com/49121847/91124021-36ce4180-e6d9-11ea-868c-30463ef4847b.png)




-lctl get_param osc.*.ost_conn_uuid : OSS, OST 연결 관계가 조회됨.
![image](https://user-images.githubusercontent.com/49121847/91124029-3fbf1300-e6d9-11ea-8197-3973b2e7b454.png)




-lfs df : OST 용량 표시 - Client에서 확인 가능
![image](https://user-images.githubusercontent.com/49121847/91124446-308c9500-e6da-11ea-9d85-85bc915aa60d.png)




-lctl get_param obdfilter.*.kbytesavail : OSS에서 용량 확인 (현재 환경에서는 TestPC 1, 3에서 가능)
![image](https://user-images.githubusercontent.com/49121847/91124461-397d6680-e6da-11ea-9f54-20e814e8ac3b.png)
![image](https://user-images.githubusercontent.com/49121847/91124466-3bdfc080-e6da-11ea-86f8-9219daa37380.png)




-lctl get_param -R osc.*.state : OST 연결 상태 조회 
![image](https://user-images.githubusercontent.com/49121847/91124479-4306ce80-e6da-11ea-8978-f87211234923.png)
![image](https://user-images.githubusercontent.com/49121847/91124486-469a5580-e6da-11ea-95a7-2cbf9f53ea1f.png)




-llstat -i 1 mdt : MDT stat 조회
/proc/fs/lustre/mds/MDS/mdt/stats << 경로에서 읽어서 보여줌


-llstat -i 1 mgs : MGS stat 조회/proc/fs/lustre/mgs/MGS/mgs/stats << 경로에서 읽어서 보여줌


-llstat -i 1 ost : OST stat 조회
/proc/fs/lustre/ost/OSS/ost/stats << 경로에서 읽어서 보여줌.


-해당 경로를 확인한 결과 stats가 생성되지 않아 해당 명령어를 실행할 수가 없었습니다. 이 부분은 확인해보겠습니다.




-llobdstat DATA-OST0000 1 : 시간에 따른 Read-delta, ReadRatem Write-delta WriteRate 을 보여줌. TestPC3에서는 OST0001로 바꿔서 실행
![image](https://user-images.githubusercontent.com/49121847/91124522-5c0f7f80-e6da-11ea-9e8a-93f99d993639.png)
![image](https://user-images.githubusercontent.com/49121847/91124528-5e71d980-e6da-11ea-8d19-95089f556640.png)




-lctl get_param obdfilter.*.stats : OSS가 있는 서버에서 확인 가능.


-lctl get_param obdfilter.DATA-OST0001.stats : 특정 OST 확인 (여기서는 OST1에 대한 상태) 
 

-lctl set_param obdfilter.DATA-OST000a.stats=0 : 초기화 
![image](https://user-images.githubusercontent.com/49121847/91124558-6cbff580-e6da-11ea-9d42-8530cd5d81a5.png)
![image](https://user-images.githubusercontent.com/49121847/91124565-6f224f80-e6da-11ea-879b-bd0043d0a709.png)






3) ChatBot과 연동

-기존에는 lustre 명령어 입력시 모든 정보를 한 번에 표시했는데,명령어를 세분화 해서 정보를 제공해줄 예정입니다.

-ChatBot이 제공하는 명령어 리스트가 많아지므로, "list"를 입력하면 lustre 관련 명령어의 리스트를 제공하는 기능을 구현할 예정입니다.