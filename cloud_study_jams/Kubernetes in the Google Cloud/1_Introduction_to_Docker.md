# 도커 소개


### Cloud Shell

- 개발 도구가 로드되는 가상 머신
- 5GB 홈 디렉토리 제공
- Google Cloud 에서 실행

![%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-04-24_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9 15 07](https://user-images.githubusercontent.com/48097396/234007591-19db1394-f15b-4e7c-8738-adf4ed867b44.png)

오른쪽 상단에 <img width="39" alt="%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-04-24_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9 15 28" src="https://user-images.githubusercontent.com/48097396/234007629-8ed3e704-328b-4453-ae4a-2fe6fe8c701d.png">버튼을 눌러서 ‘Cloud Shell’ 활성화를 할 수 있다.


## Docoker

- 활성 계정 이름 조회

```kotlin
gcloud auth list
```

그러면 내가 로그인한 계정이 나온다.

- Project ID 조회

```kotlin
gcloud config list project도
```

- 도커이미지 실행 명령어

```kotlin
docker run hello-world
```

1. 이미 있을 때: 로컬에서 hellow-world를 못찾으면 Docker Hub 라는 공용 레지스트리에서 이미지를 가져와서 컨테이너를 실행한다.
2. 로컬에 있을 때: 로컬 레지스트리에서 이미지를 찾아 해당 이미지에서 컨테이너를 실행한다.

- 로컬 도커 이미지 조회

```kotlin
docker images
```

이미지 아이디는 SHA256 해시 형식이다.

- 실행중인 도커 이미지 조회

```kotlin
docker ps
```

- 실행이 완료된 컨테이너를 포함하여 모든 컨테이너를 조회

```kotlin
docker ps -a`
```

`

- `Dockerfile` 와 `app.js` 를 만들어서 도커 이미지 만들기

```kotlin
cat > Dockerfile <<EOF
# Use an official Node runtime as the parent image
FROM node:lts
# Set the working directory in the container to /app
WORKDIR /app
# Copy the current directory contents into the container at /app
ADD . /app
# Make the container's port 80 available to the outside world
EXPOSE 80
# Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF
```

```kotlin
cat > app.js <<EOF
const http = require('http');
const hostname = '0.0.0.0';
const port = 80;
const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World\n');
});
server.listen(port, hostname, () => {
    console.log('Server running at http://%s:%s/', hostname, port);
});
process.on('SIGINT', function() {
    console.log('Caught interrupt signal and will exit');
    process.exit();
});
EOF
```

- 도커 이름 과 태그

```kotlin
docker build -t node-app:0.1 .
```

-t는 이름:태그 조합으로 지정한다. 태그는 도커 이미지를 만들때 권장되는 요소다. 지정해주지 않으면 덮어쓰게 되기 때문에 

- 도커 내부 bash 실행하는 방법

```kotlin
docker exec -it [container_id] bash커
```

- ㄷ도커 메타데이터 검사(? 조회?)

```kotlin
docker inspect [container_id]
```

- ㅁㅔ타 데이터 특정 필드 검사하기

```kotlin
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]
```

## Publish

비공개 레지스트리에 이미지를 푸시하려면 이미지 태그에 레지스트리 이름으로 지정해야한다.

```kotlin
<regional-repository>-docker.pkg.dev/my-project/my-repo/my-ima
```

- 예시

```kotlin
us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2
```

### **Artifact Registry**

저장소. 도커형식 / 지역: us-central1 (Iowa) 로 레포지토리를 만든다.

### 인증 구성

us-central1 리전의 Docker 리포지토리에 대한 인증 설정

```kotlin
gcloud auth configure-docker us-central1-docker.pkg.dev
```

- 도커 이미지 푸시

```kotlin
docker push us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2
```
