![image](https://user-images.githubusercontent.com/54893898/91134474-b8799b80-e6e8-11ea-8437-958316ba3dca.png)

네임스페이스 생성

![image](https://user-images.githubusercontent.com/54893898/91134499-ba435f00-e6e8-11ea-94e5-bc6d335c1def.png)

폴더 생성

**vi deployment.yaml**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins
        image: jenkins/jenkins:lts
        ports:
        - containerPort: 8080
        volumeMounts:
          - name: jenkins-home
            mountPath: /var/jenkins_home
      volumes:
        - name: jenkins-home
          emptyDir: {}
      nodeSelector:
        kubernetes.io/hostname: node1

```

vi service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: jenkins
spec:
  type: NodePort
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30000
  selector:
    app: jenkins
```

![image](https://user-images.githubusercontent.com/54893898/91134534-bca5b900-e6e8-11ea-809a-51677a8d782e.png)

jenkins 이미지 pull 하기

![image](https://user-images.githubusercontent.com/54893898/91134551-bdd6e600-e6e8-11ea-9cb7-904599e961ed.png)

node1에서 실행중인 컨테이너로 들어가서 비밀번호를 알아야됨

```
docker exec -it <jenkins 컨테이너 이름> /bin/bash
```

```
cat /var/jenkins_home/secrets/initialAdminPassword
```

![image](https://user-images.githubusercontent.com/54893898/91134564-be6f7c80-e6e8-11ea-9d0a-8d2d11ddec7f.png)

Install suggested plugins를 클릭해서 플러그인들을 설치 해준다.

![image](https://user-images.githubusercontent.com/54893898/91134593-c0d1d680-e6e8-11ea-9253-dc150b24803c.png)

## 참고

![image](https://user-images.githubusercontent.com/54893898/91134620-c29b9a00-e6e8-11ea-94d7-2ee3ac54ac25.png)

플러그인 설치가 끝나면

![image](https://user-images.githubusercontent.com/54893898/91135330-f5459280-e6e8-11ea-8f86-de69ed1bb58d.png)

저는 admin을 계정명으로 하였습니다.

![image](https://user-images.githubusercontent.com/54893898/91136631-553c3900-e6e9-11ea-8d95-743070199c99.png)

이렇게 정상적으로 Jenkins가 설치가 완료가 되었습니다.

**'쿠버네티스 컴포넌트와 상호작용하는 소스코드의 Github으로 Push되었을 때 Jenkins에서 빌드가 발생하는 것'**

우리는 Jenkins를 이용해 CI를 구성해야 하기 때문에, Jenkins & Github를 연동해서 파이프라인을 구성을 합니다.

![image](https://user-images.githubusercontent.com/54893898/91140300-8ec17400-e6ea-11ea-9e0a-7f743f02d127.png)

Private 저장소가 아니라, public 저장소라면 이 단계는 넘어가도 됩니다.  
만약 private 저장소라면 접근을 위해서 토큰을 생성하여야 합니다.

![image](https://user-images.githubusercontent.com/54893898/91141002-c0d2d600-e6ea-11ea-92f4-c8284ea38ba2.png)

Personal access tokens 에 들어가서

![image](https://user-images.githubusercontent.com/54893898/91141235-d21be280-e6ea-11ea-8dcb-fcf825c8a4d4.png)

Generate new token 을 클릭해서 토큰을 생성해줍니다.

![image](https://user-images.githubusercontent.com/54893898/91141717-f5df2880-e6ea-11ea-9986-b7cdfce3f0f4.png)

테스트를 위해서 전부 체크해주었습니다.

그리고 토큰이 생성되었으면, 젠킨스와 연결하기 위해서 젠킨스에서

![image](https://user-images.githubusercontent.com/54893898/91142045-1dce8c00-e6eb-11ea-82c5-53579bd10ca8.png)

Jenkins 관리 에 들어갑니다.

![image](https://user-images.githubusercontent.com/54893898/91142077-2a52e480-e6eb-11ea-93b3-851fdd4a1aed.png)

그리고 시스템 설정에 들어갑니다.

![image](https://user-images.githubusercontent.com/54893898/91142127-3f2f7800-e6eb-11ea-9503-f8eb341c71c5.png)

그리고 내려가다 보면 깃허브 관련 설정이 있습니다.

![image](https://user-images.githubusercontent.com/54893898/91142310-83227d00-e6eb-11ea-8d7d-2f093293cc24.png)

Name은 원하는 이름을 넣고,

Credentials에 Jenkins를 클릭해서

![image](https://user-images.githubusercontent.com/54893898/91142295-7aca4200-e6eb-11ea-8231-4c48ff5f24d7.png)

위 그림과 같이 바꾸고, Secret에는 우리가 생성한 토큰을 넣어줍니다.

![image](https://user-images.githubusercontent.com/54893898/91142847-7fdbc100-e6ec-11ea-941d-98c6241c4f36.png)

그리고 test connection을 클릭해서 연결이 잘되는지 확인합니다.








## 참고

[https://m.blog.naver.com/PostView.nhn?blogId=alice\_k106&logNo=221562805601&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=221562805601&proxyReferer=https:%2F%2Fwww.google.com%2F)
