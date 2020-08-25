멀티 노드를 구현하기 위해서는 여러 대의 서버가 있거나

혹은 가상머신을 설치하여 여러 대의 가상 서버를 구축해야합니다.

## 1\. 가상머신 설치(리눅스기반 설치)

### 1-1. Virtual Box 설치

[https://www.virtualbox.org/wiki/Download\_Old\_Builds\_5\_2(Virtual](https://www.virtualbox.org/wiki/Download_Old_Builds_5_2(Virtual) [Box 설치 링크)](https://www.virtualbox.org/wiki/Download_Old_Builds_5_2)

위 링크 접속 후

![image](https://user-images.githubusercontent.com/54893898/91118216-6a09d400-e6cb-11ea-860a-26fc36d811d6.png)

메인 PC의 OS에 맞는 것을 다운로드!

### 1-2. CentOS7 ISO 설치

[https://www.centos.org/download/(centOS7](https://www.centos.org/download/(centOS7) [설치 링크)](https://www.centos.org/download/)

![image](https://user-images.githubusercontent.com/54893898/91118220-6b3b0100-e6cb-11ea-8b16-0216f73b56b5.png)

x86\_64 ISO 클릭!

![image](https://user-images.githubusercontent.com/54893898/91118222-6c6c2e00-e6cb-11ea-8b9d-8ddb33cbd872.png)

저기서 아무곳 클릭! (네이버 클릭했음)

![image](https://user-images.githubusercontent.com/54893898/91118224-6d9d5b00-e6cb-11ea-9c88-adfa3b1cb06a.png)

저기서 맨 위에있는 iso 파일 다운로드 했습니다.

적은 용량으로 하고싶다면 밑에 Minimal 버전 iso 파일 다운로드!

나머지 Virtual box에 centOS7 설정 및 실행은[https://xxsiyoung.tistory.com/2](https://xxsiyoung.tistory.com/2)참조

## 2\. SSH 연결 

kubespray는 ansible을 기반으로 하기 때문에 SSH 연결이 되어있어야 합니다.

[https://amorfati-1000.tistory.com/50(윈도우](https://amorfati-1000.tistory.com/50(윈도우) [SSH 설치 및 연결)](https://amorfati-1000.tistory.com/50)

마스터 노드가 될 노드에서 key 생성

```
ssh-keygen -t rsa
```

마스터로부터 노드에게 key를 복사

```
ssh-copy-id root@<ip>
```

master에도 ssh-copy-id 작업을 해주어야합니다.

## 3\. 마스터 노드 환경 구성

마스터 노드에 필요한 패키지들 설치

```
yum install -y python3, python3-pip
```

## 4\. Kubernetes 설치 전 환경설정

### 4-1. /proc/sys/net/ipv4/ip\_forward 활성화 (Master Node & Worker Node)

```
echo > 1 /proc/sys/net/ipv4/ip_forward enable
```

### 4-2. firewalld 중지 및 disable (Master Node & Worker Node)

```
systemctl stop firewalld && systemctl disable firewall
```

## 5\. Kubespray 설치 및 구성

### 5-1. kubespray git clone 하기

```
sudo git clone https://github.com/kubernetes-sigs/kubespray
```

```
cd kubespray
```

### 5-2. 필요한 패키지들 설치

```
sudo pip3 install -r requirements.txt
```

![image](https://user-images.githubusercontent.com/54893898/91118289-9887af00-e6cb-11ea-8a79-b5ed48ed4078.png)

### 5-3. inventory.ini 파일 정의

\- kubespray/inventory/sample/inventory.ini 파일 정의

![image](https://user-images.githubusercontent.com/54893898/91118225-6fffb500-e6cb-11ea-87da-6e0fd4d42b1f.png)
### 5-4. 필요한 addons 설정

\- kubespray/inventory/sample/group\_vars/k8s-cluster/addons.yml 파일

helm repo (쿠버네티스의 패키지 매니저)

metrics\_server(쿠버네티스 리소스 현황 및 모니터링 패키지)

ingress\_controller(쿠버네티스 네트워크 관련 오브젝트 패키지)

true로 바꿔주기

### 5-5. iptables로 바꿔주기

\- kubespray/inventory/sample/group\_vars/k8s-cluster/cluster.yml 파일

ipvs => iptables 로 바꿔주기

### 5-6. ansible-playbook 실행

\- kubespray 폴더(cluster.yml 파일이 존재하는 곳)에서 실행

```
ansible-playbook --flush-cashe -K -b -i inventory/sample/inventory.ini cluster.yml -v
```

이렇게 하면 설치가 진행됩니다.

그리고 전부 완료가되면 쿠버네티스가 구축이 완료가 된 것입니다. 

## 설치 도중 & 이후 참고 사이트

### 설치시 나타나는 오류 해결 했던 것들

[https://junghyeonsu.tistory.com/49](https://junghyeonsu.tistory.com/49)

### PHP - Redis 이용한 방명록 애플리케이션 배포하기

[https://kubernetes.io/ko/docs/tutorials/stateless-application/guestbook/](https://kubernetes.io/ko/docs/tutorials/stateless-application/guestbook/)

[\[Kubernetes 공식문서 파헤치기\] Redis를 사용한 PHP 방명록 애플리케이션 배포하기 + PV, PVC, Affinity 사용해보기](https://junghyeonsu.tistory.com/56)

### 쿠버네티스 고가용성 테스트하기 (방명록 애플리케이션 기준)

[https://junghyeonsu.tistory.com/58](https://junghyeonsu.tistory.com/58)

### 쿠버네티스 CI/CD 맛보기

[https://m.blog.naver.com/PostView.nhn?blogId=alice\_k106&logNo=221562805601&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=221562805601&proxyReferer=https:%2F%2Fwww.google.com%2F)
