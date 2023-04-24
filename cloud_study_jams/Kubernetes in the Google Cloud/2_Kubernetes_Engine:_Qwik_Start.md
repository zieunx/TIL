# 쿠버네티스 엔진: 시작하기

## 클러스터

하나 이상의 클러스터 마스터와 노드들로 구성되어있음

노드 = 쿠버네티스 프로세스(클러스터의 일부로 만드는데에 필요한)를 실행하는 Compute Engine VM 인스턴스

> 클러스터 네이밍: 문자로 시작하고 영어 혹은 숫자로 끝나는 40자 이내
> 

### 클러스터 만들기

```bash
gcloud container clusters create --machine-type=e2-medium --zone=#{zone이름} ${클러스터이름} 
```

ㅁ몇가지 옵션

- —machine-type: CPU를 지정 ([공식가이드](https://cloud.google.com/compute/docs/machine-resource?hl=ko))
- —zone: 어떤 존에 생성할 것인지 지정

예)

```bash
gcloud container clusters create --machine-type=e2-medium --zone=us-east4-b lab-cluster
```

그럼 다음처럼 생성된다. 

```bash
NAME: lab-cluster
LOCATION: us-east4-b
MASTER_VERSION: 1.25.7-gke.1000
MASTER_IP: 34.86.87.171
MACHINE_TYPE: e2-medium
NODE_VERSION: 1.25.7-gke.1000
NUM_NODES: 3
STATUS: RUNNING
```

### 자격증명

클러스터와 상호작용하기 위해서는 인증 자격증명이 필요하다.

```bash
gcloud container clusters get-credentials lab-cluster
```

ㄱ

### 클러스터에 애플리케이션 배포

1에서 만들었던 `hello-app`에서 새 배포 객체 `hello-server`를 만든다.

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

쿠버네티스 서비스를 생성

```bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

- —port: 노출할 포트 지정
- —type: ‘LoadBalancer’는 컨테이너에 대한 compute engine 부하 분산기를 만든다

이렇게 만들어진걸 확인할 수 있다. `kubectl get service`

```bash
NAME           TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
hello-server   LoadBalancer   10.88.4.163   xx.xx.xxx.xxx   8080:30027/TCP   2m38s
kubernetes     ClusterIP      10.88.0.1     <none>          443/TCP          15m`
```

`http://[EXTERNAL-IP]:8080` 에서 `[EXTERNAL-IP]`를 `hello-server의 EXTERNAL-IP` 로 대체해서 브라우저에 요청해본다

<img width="368" alt="스크린샷 2023-04-25 오전 1 09 43" src="https://user-images.githubusercontent.com/48097396/234055177-a8761e7c-a1d6-41de-a1af-525cea23b103.png">

## 클러스터 삭제하기

```bash
gcloud container clusters delete lab-cluster
```
