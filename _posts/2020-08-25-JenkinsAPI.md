---
title: "JenkinsAPI-Python Jenkins"
date: 2020-08-25 08:26:28 -0400
categories: recruit
---
## Jenkins API 조사 - Python Jenkins
### 설치 및 서버 연결
* 설치
> $ pip install python-jenkins
* 서버 연결 및 젠킨스 버전 확인
```python
import jenkins
server = jenkins.Jenkins('http://localhost:8080', username='myuser', password='mypassword')
user = server.get_whoami()
version = server.get_version()
print('Hello %s from Jenkins %s' % (user['fullName'], version))
```
### Job 관련 API
> create_job(name, config_xml)  

jenkins에 새로운 작업 생성  
 - name: (str)생성할 작업 이름  
 - config_xml: (str) config 파일 text  
> get_jobs(folder_depth=0, view_name=None)  

모든 작업에 대한 정보를 각각 딕셔너리 형태로 리스트를 리턴  
 - folder_depth: (int) 탐색할 레벨 지정 가능, default = 0   
 - view_name: (str) 작업을 검색할 Jenkins 뷰 이름 지정 가능  
> get_job_config(name)

존재하는 Jenkins 작업의 configuration XML 형식으로 리턴
 - name: (str) 젠킨스 작업 이름  
> reconfig_job(name, config_xml)

작업 configuration 변경  
 - config_xml: 새로운 XML configuration  
> disable_job(name)

작업 disable  
> enable_job(name)

작업 enable  
> copy_job(from_name, to_name) 

작업 복사
 - from_name: (str) 복사할 Jenkins 작업의 이름  
 - to_name: (str) 복사될 Jenkins 작업의 이름  
> delete_job(name)

작업 영구 삭제  
> get_job_info(name, depth=0, fetch_all_builds=False)

작업 정보 dictionary 형태로 리턴   
 - depth: (int) JSON 깊이  
 - fetch_all_builds: (bool) True인 경우, 전체 작업 정보 리턴. False인 경우, 가장 최근 100개의 빌드만 리턴  

## 빌드 관련 API
> build_job(name, parameters=None, token=None)  

작업에 빌드를 유발함  
대기열 항목 번호를 반환하는데 이 번호는 작업이 완료된 후 약 5분 동안만 유효함  
 - name: 작업 이름  
 - parameters: (None, dict)job의 매개변수  
 - token: Jenkins API 토큰  
> get_build_info(name, number, depth=0)

빌드 정보 dictionary 리턴  
 - name: (str) 작업 이름  
 - number: (int) 빌드 번호  
 - depth: (int) JSON 깊이  
> get_queue_item(number, depth=0)  

대기 중인 항목에 대한 정보를 가져옴  
 - name: (int) 큐 번호  
> get_queue_info()  

작업 딕셔너리 리스트 리턴  
 - 예시   
```python
queue_info = server.get_queue_info()
print(queue_info[0])
# {u'task': {u'url': u'http://your_url/job/my_job/', u'color': u'aborted_anime', u'name': u'my_job'}, u'stuck': False, u'actions': [{u'causes': [{u'shortDescription': u'Started by timer'}]}], u'buildable': False, u'params': u'', u'buildableStartMilliseconds': 1315087293316, u'why': u'Build #2,532 is already in progress (ETA:10 min)', u'blocked': True}
```
> cancel_queue(id)  

큐에서 빌드 정보 제거  
 - id: (int) 빌드에 대한 Jenkins 작업 ID 번호    

## View 관련 API
> create_view(name, config_xml)

뷰 생성  
 - name: (str) 생성할 뷰 이름  
 - config_xml: (str) config 파일 text  
> get_view_config(name)

존재하는 뷰의 configuration XML 형태로 리턴  
- name: (str) 뷰 이름  
> get_views()

run 상태의 뷰 리스트 반환  
각각의 뷰는 'name'과 'url' key의 딕셔너리    
> delete_view(name)

뷰 제거

## Plugin 관련 API 
> get_plugins(depth=2)

설치된 모든 플러그인에 대한 정보를 검색하고 버전 비교를 위해 helper class를 사용하여 플러그인 정보 리턴  
 - depth: JSON 깊이
 - 예시
```python
 j = Jenkins()
info = j.get_plugins()
print(info)
# {('gearman-plugin', 'Gearman Plugin'): {u'backupVersion': None, u'version': u'0.0.4', u'deleted': False, u'supportsDynamicLoad': u'MAYBE', u'hasUpdate': True, u'enabled': True, u'pinned': False, u'downgradable': False, u'dependencies': [], u'url': u'http://wiki.jenkins-ci.org/display/JENKINS/Gearman+Plugin', u'longName': u'Gearman Plugin', u'active': True, u'shortName': u'gearman-plugin', u'bundled': False}, ...}
```

## Reference
* [Jenkins-Python 공식 페이지](https://python-jenkins.readthedocs.io/en/latest/index.html)