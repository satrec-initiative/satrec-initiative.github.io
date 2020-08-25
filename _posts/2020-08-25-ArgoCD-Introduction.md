## DevOps

DevOps란 개발(Dev)과 운영(Ops)을 따로 구분짓지않고 함께하는 것을 의미합니다.

## CI(Continuous Integration)

**지속적 통합(Continuous Integration)**은 자동화된 빌드 및 테스트가 수행된 후, 개발자가 코드 변경 사항을 중앙 리포지토리에 정기적으로 병합하는 데브옵스 소프트웨어 개발 방식입니다. 지속적 통합은 모든 개발을 완료한 뒤에 소프트웨어의 질적 향상과 소프트웨어를 배포하는 데 걸리는 시간과 비용을 줄이는데 초점이 맞추어져 있습니다.

![image](https://user-images.githubusercontent.com/54893898/91117894-b56fb280-e6ca-11ea-9c2f-592e348b70a1.png)

대표적인 CI 툴은 Jenkins가 있습니다.

[핀다](https://medium.com/finda-tech)에서는 Jenkins를 통해서 새로운 코드를 테스트하고, 빌드한 후 빌드 파일을 Docker image로 만들어 쿠버네티스 시스템에서 새로운 코드가 돌아갈 수 있도록 파이프라인을 구성했습니다.

하지만 Jenkins에서의 역할은 오로지 빌드까지만 이였습니다. 그럼 새로운 코드의 배포는 어디서 담당할까요?

바로 CD에서 진행됩니다.

## CD(Continuous Delivery)

**지속적 전달**은 무엇일까요? 이름 그대로 지속적 전달이란, 지속적 통합을 통해 테스트되고 빌드된 코드를 지속적으로 전달하여 제품의 질적 향상을 꾀하는 것 입니다.

![image](https://user-images.githubusercontent.com/54893898/91117912-c1f40b00-e6ca-11ea-814b-f5b2213a15c4.png)

대표적인 CD 툴은 Argo CD가 있습니다.

Argo CD는 쿠버네티스를 위한 GitOps 지속적 전달 툴 입니다.

### GitOps

쿠버네티스 운영을 위한 manifest 파일들을 하나의 원격 리포지토리에서 관리한다는 개념입니다.

즉, 이름 그대로 Git + Ops(운영) 즉, 운영적인 부분을 Git으로 관리하겠다는 뜻입니다.

우리는 애플리케이션을 쿠버네티스에서 관리할 때 필요한 오브젝트(manifest)파일 들이 많습니다.

Deployment, Service, volumes, ingress, PVC, PV... 등등

이 많은 YAML 파일들을 좀 더 편하게 관리하기 위해서 Git으로 관리하는 것입니다.

### Helm 

만약 deployment에서 애플리케이션 두 개를 배포하는데,

중첩되는 코드가 많다면 중첩되는 값을 변수로 만든 다음 코드에 바인딩만 시켜주면 되지 않을까요?

그것을 도와주는 도구가 Helm 입니다.

Helm은 쿠버네티스의 패키지 매니저 입니다.

따라서 Helm은 쿠버네티스에서 패키징, 구성 및 설정, 배포에 관련된 작업들을 보다 수월하게 할 수 있도록 도와줍니다.

─── example-app  
├── /templates  
│ └── deployment.yaml  
├── Chart.yaml  
└── values.yaml

```
kind: Deployment 
apiVersion: apps/v1 
metadata:
   name: {{ .Values.app-name }}
spec:
   replicas: 1
   selector:
     matchLabels:
       app: {{ .Values.app-label }}
   template:
     metadata:
       labels:
         app: {{ .Values.app-label }}
     spec:
       containers:
       - name: {{ .Values.app-label }}
         image: python:2.7
         imagePullPolicy: IfNotPresent
         env: # 환경 변수 추가
         - name: say
           value: {{ .Values.say }}
         command: ["/bin/bash"]
         args: ["-c", "echo \"<p>$(say) from $(hostname)</p>\" > index.html; python -m SimpleHTTPServer 8080"]
         ports:
         - name: http
           containerPort: 8080
```

/example-app/templates/deployment.yaml

```
apiVersion: v1
appVersion: "1.0"
description: This is Example App Helm chart for Kubernetes 
name: example-app 
version: 0.1.0
```

/example-app/Chart.yaml

```
app-name: hello-app-deployment 
app-label: hello-app 
say: Hello
---
app-name: world-app-deployment
app-label: world-app
say: World
```

/example-app/values.yaml

이렇게 하면 world app deployment, hello app deployment를 따로따로 만들지 않아도 됩니다.

나중에 애플리케이션이 더 커져서 많은 디플로이먼트를 요구한다해도 다음과 같은 방식이면

코드량을 많이 줄일 수 있습니다.

다시 돌아와서 [Argo CD](https://argoproj.github.io/argo-cd/getting_started/)는 쿠버네티스 운영에 관련된 manifest 파일들을 관리하고 있는 원격 리포지토리를 조회합니다.(GitOps)

 Argo CD는 쿠버네티스로 배포되고 관리됩니다. (이 말은 즉슨 Argo CD는 쿠버네티스내에서 작동됩니다.)

![image](https://user-images.githubusercontent.com/54893898/91117928-ccaea000-e6ca-11ea-9a6d-a36818e96285.png)

정리를 하자면

Jenkins가 가장 우선적으로 새로운 코드를 테스트하고 빌드한 후 Docker image를 만들어내고

이를 AWS ECR(Elastic Container Repository)에 저장한 후 (다른 클라우드를 쓰면 다른 클라우드에 저장)

이에 대한 새로운 Docker image 버젼 태그 내용을 GitOps 리포지토리의 내용에 업데이트 해줍니다.

그리고 Argo CD에서는 새로 업데이트된 GitOps 리포지토리의 내용을 감지해서 현재 쿠버네티스에 배포된 manifest 내용과 새로 업데이트된 GitOps 리포지토리의 manifest 내용을 비교(Diff)하여 업데이트 된 내역을 확인하면 운영자의 조작에 따라서 혹은 자동으로 업데이트된 내용을 적용(Sync) 하여 쿠버네티스에 배포합니다.

## Jenkins 설치

Jenkins는 쿠버네티스 파드로 실행할 수 있습니다.

1\. kubernetes jenkins 설치하기

[https://velog.io/@rudasoft/Docker-on-Kubernetes-%EC%97%90-Jenkins-%EC%84%A4%EC%B9%98-%EC%9C%88%EB%8F%84%EC%9A%B0-%ED%99%98%EA%B2%BD-v6k3thwevd(루다소프트 기술 블로그)](https://velog.io/@rudasoft/Docker-on-Kubernetes-%EC%97%90-Jenkins-%EC%84%A4%EC%B9%98-%EC%9C%88%EB%8F%84%EC%9A%B0-%ED%99%98%EA%B2%BD-v6k3thwevd)

2\. kubernetes hele 사용해서 jenkins 설치하기

[https://velog.io/@hamon/Kubernetes-Cluster%EC%97%90-Jenkins-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0(](https://velog.io/@hamon/Kubernetes-Cluster%EC%97%90-Jenkins-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)

## Argo CD 설치

[https://argoproj.github.io/argo-cd/getting\_started/:공식사이트](https://argoproj.github.io/argo-cd/getting_started/)

### 1\. Argo CD 설치하기

Argo CD namespace 생성하기

```
kubectl create namespace argocd
```

Argo CD 설치하기

```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

만약 GKE(Google Kubernetes Engine)이라면 아래의 명령어를 통해서 새로운 클러스터 roles를 생성해야합니다.

```
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user="$(gcloud config get-value account)"
```

### 2\. Argo CD CLI 설치

Argo CD CLI 참고사이트 : [https://argoproj.github.io/argo-cd/cli\_installation/](https://argoproj.github.io/argo-cd/cli_installation/)

우선 아래의 명령어를 통해서 Argo CD의 version을 확인합니다.

```
VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
```

그리고 그 버전을 아래의 명령어에서 $VERSION 에 변경시켜줍니다.

```
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64

```

다음은 argocd CLI를 실행가능하게 만들어줍니다.

```
chmod +x /usr/local/bin/argocd
```

### 3\. Argo CD API Server에 접근하기

우선 argocd-server의 서비스 타입을 LoadBalancer로 바꾸기위해 다음의 명령어를 입력합니다.

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

> 만약 Ingress를 사용해서 argocd를 설정하고싶다면 [사이트](https://argoproj.github.io/argo-cd/operator-manual/ingress/)를 참고하세요

port forwarding은 service 노출없이 API Server에 연결을 할 수 있습니다.

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### 4\. CLI를 이용해서 로그인하기

비밀번호 초기값은 Argo CD API Server의 파드 이름입니다. 다음 명령어를 통해서 찾을 수 있습니다.

```
kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2
```

username admin 과 위의 패스워드를 사용해서 로그인 할 수 있습니다.

```
argocd login <ARGOCD_SERVER>
```

만약 패스워드를 바꾸고 싶다면 아래의 명령어를 통해서 바꿀 수 있습니다.

```
argocd account update-password
```

### 5\. 깃 레파지토리에서 앱 생성하기

방명록 애플리케이션이 포함 된 예제 저장소는

[https://github.com/argoproj/argocd-example-apps.git에서](https://github.com/argoproj/argocd-example-apps.git에서)

Argo CD의 작동 방식을 보여줍니다.

IP/hostname 을 브라우저에서 입력해서 UI를 열수있습니다.

![image](https://user-images.githubusercontent.com/54893898/91118062-0da6b480-e6cb-11ea-80a8-33139b900499.png)
그리고  +NEW APP을 클릭해서

앱 이름은 guestbook,

project는 default,

동기화 정책은 Manual로 설정해줍니다.

![image](https://user-images.githubusercontent.com/54893898/91118063-0f707800-e6cb-11ea-8377-02529703c421.png)

 [https://github.com/argoproj/argocd-example-apps.git](https://github.com/argoproj/argocd-example-apps.git) 에 연결을 위해

Repository URL에 위의 주소를 넣어줍니다.

그리고 리비전을 HEAD로 설정하고, guestbook 경로를 설정하여 Argo CD에 연결합니다.

![image](https://user-images.githubusercontent.com/54893898/91118064-10a1a500-e6cb-11ea-8b1f-58937eadb64d.png)

DESTINATION에서는 Cluster와 namespace를 설정하는데

자기자신의 클러스터와 namespace는 default로 설정해줍니다.

![image](https://user-images.githubusercontent.com/54893898/91118068-11d2d200-e6cb-11ea-9f89-3ee2caab4c46.png)

다음과 같이 정보들을 기입해주고 CREATE 버튼을 클릭해서

방명록 애플리케이션을 만듭니다. 

![image](https://user-images.githubusercontent.com/54893898/91118073-15665900-e6cb-11ea-9809-dcdb0c7d77fb.png)

### 6\. 애플리케이션 동기화(배포)하기

```
$ argocd app get guestbook
Name:               guestbook
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://10.97.164.88/applications/guestbook
Repo:               https://github.com/argoproj/argocd-example-apps.git
Target:
Path:               guestbook
Sync Policy:        <none>
Sync Status:        OutOfSync from  (1ff8a67)
Health Status:      Missing

GROUP  KIND        NAMESPACE  NAME          STATUS     HEALTH
apps   Deployment  default    guestbook-ui  OutOfSync  Missing
       Service     default    guestbook-ui  OutOfSync  Missing
```

처음 방명록 앱을 만들면 이 상태를 볼 수 있습니다.

애플리케이션이 아직 배포되지 않았고 Kubernetes 리소스가 생성되지 않았으므로

애플리케이션 상태는 초기에 OutOfSync 상태입니다.

애플리케이션을 동기화 (배포)하려면 다음을 실행해야합니다.

```
argocd app sync guestbook
```

![image](https://user-images.githubusercontent.com/54893898/91118076-16978600-e6cb-11ea-84d8-8e7f19d1aa32.png)

## ArgoCD의 장단점

ArgoCD는 Git 저장소에 있는 매니페스트를 쿠버네티스 클러스터에 반영해 주는 역할을 합니다.

10초 마다 주기적으로, Job 오브젝트를 이용해서 git의 내용을 쿠버네티스 클러스터에 적용을 할 수 있습니다.

직접 Job 오브젝트를 구현해서 사용할 수도 있지만, 보안이나 모니터링등 여러 측면에서 불편하기 때문에

기존에 만들어진 GitOps 오퍼레이터를 사용합니다. 대표적인 GitOps 오퍼레이터가 ArgoCD인 것 입니다.

Argo CD는 GitOps스타일의 배포를 지원하는 CD 도구입니다. 원하는 설정 사항을 변경하여 Git에 푸시하면, 자동으로 쿠버네티스 클러스터의 상태가 Git에 정의된 상태로 동기화 됩니다.

장점으로는 Git에 정의된 상태로 지속적으로 동기화를 시켜주기 때문에 개발자는 개발에만 집중할 수 있습니다.

그리고 여러 클러스터에 대해 관리 및 배포 기능을 지원하고, Git 저장소에서 커밋 된 모든 앱의 구성으로

롤백이 가능합니다. 그리고 수동/자동으로 전환이 가능합니다.

단점은 최초 세팅 시간이 조금 길다는 점, 그리고 ArgoCD같은 툴의 버전이 바뀐다면 그에 맞게 변경해야

하는 것들이 있을 수 있다는 것입니다. 

## 참고

[\[FINDA\] MSA를 위한 Kubernetes 세팅과 CI/CD Pipeline 구성](https://medium.com/finda-tech/finda-msa%EB%A5%BC-%EC%9C%84%ED%95%9C-kubernetes-%EC%84%B8%ED%8C%85%EA%B3%BC-ci-cd-pipeline-%EA%B5%AC%EC%84%B1-%EA%B7%B8%EB%A6%AC%EA%B3%A0-monitoring-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B5%AC%EC%B6%95-2-ef29380ec474)

[https://argoproj.github.io/argo-cd/getting\_started/](https://argoproj.github.io/argo-cd/getting_started/)

[https://gruuuuu.github.io/cloud/gitlab-cicd/#](https://gruuuuu.github.io/cloud/gitlab-cicd/#)

[https://kangwoo.kr/tag/argocd/](https://kangwoo.kr/tag/argocd/)
