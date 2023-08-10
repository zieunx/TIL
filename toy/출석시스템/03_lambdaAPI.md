# 03_lambda API

람다 API 를 만드는 모든 과정을 기록해놓겠다.

### 환경 이해하기
다음과 같은 구조로 만들었더니 레포지토리를 못읽어옴.

![스크린샷 2023-06-26 오후 11 27 29](https://github.com/zieunx/TIL/assets/48097396/2714d83b-1f25-4f29-aae7-8b83d24b4c7f)

package.json 에 `"main": "index.js"` 설정을 해줘서 메인실행 경로를 지정해줘야한다(?)??

그래서 @aws/sdk 의존성을 추가한 package.json을 만들어준다. 아주 심플하게.

일단 잘 저장되는지 테스트.

```javascript
export const handler = async (event, context) => {
    const saveItem = await AttendanceRepository.putItem(
        {
            createdAt: new Date().toString(),
            userId: 1,
            message: "hello!!"
        }
    )
    
    return {
        statusCode: 200,
        body: JSON.stringify(saveItem)
    }
};
```

```javascript
static async putItem(item) {
    const putParams = {
        TableName: "Attendance",
        Item: {
            attendanceId: generateUUID(),
            ...item
        }
    };

    return await docClient.send(new PutCommand(putParams));
}
```

잘 저장된다.


### API Gateway

AWS 공식문서 [API Gateway 시작하기](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/getting-started.html)를 보고 람다를 연결해줬다.

### 계층 생성

여러개의 람다함수에 같은 환경을 설정하고 싶다면 계층을 이용하면 된다.

람다 계층을 만들어서 함수들이 공유할 수 있는 환경을 세팅할 수 있다.  

공식문서 - https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/configuration-layers.html

위에서 만든 package.json을 zip으로 만들어서 계층을 만들어준다.

![스크린샷 2023-06-26 오후 11 35 59](https://github.com/zieunx/TIL/assets/48097396/d999d471-c4b0-4a9c-8264-d5a93611a228)

계층 페이지에서 사용자 지정 계층을 올린것이다. '사용자 지정 계층'을 선택해서 방금 내가 올린 계층을 올려주면 되겠다.

진행하다보니 의도대로 안되거나 어떻게 해야되는지 모르겠음..

 AWS 컴퓨팅 블로그의 [Lambda 계층을 사용하여 개발 프로세스 단순화](https://aws.amazon.com/ko/blogs/compute/using-lambda-layers-to-simplify-your-development-process/)를 참조했더니 계층 동작에 성공했다.

 하지만 해당 글은 예전 버전 기준으로 예시가 작성되어있다. (요즘은 aws-sdk 전체 의존성을 추가하지 않고 각각의 모듈별로 의존성을 추가하여 사용하는 추세로 바뀌었다. (예. @aws-sdk/client-dynamodb) 또한 node 버전도 현재 18까지 올라갔고 aws-sdk도 3 이상으로 바뀌었다.)  
 로컬 layer 구성의 프로젝트를 생성하여 최신버전에 맞게 코드를 수정하여 배포하면 정상적으로 작동하니 특히 `samconfig.toml`파일과 `package.json` 파일을 잘 수정하여 배포하면 되겠다.  
 나도 별도의 프로젝트를 만들어 [lambda-layer](https://github.com/zieunx/lambda-layer) git repostiory에 올려두었다. (git에 public repository로 올리기 전에 개인정보가 포함되어있는지 확인 후에 올려야 한다.)


### 응답 구성


```javascript
exports.handler = async (event) => {
    const response = {
        statusCode: 200,
        headers: {
            "Content-Type": "application/json",
            "Access-Control-Allow-Origin": "*",
            "Custom-Header": "customHeaderValue"
        },
        body: JSON.stringify({ key: 'value' })
    };
    return response;
};
```

람다의 기본 응답 구성이다.

- statusCode는 http status code를 입력하면 된다.
- header는 JSON 형식으로 구성하고 여러개 추가할 수 있다.
- body는 문자열로 응답해야한다. Json으로 응답하고싶으면 `JSON.stringify({ key: 'value' })`로 응답하되 헤더에 ` "Content-Type": "application/json"`를 달아주면 된다. Json 그대로 응답하면 500 에러 발생함..

### 요청값 추출하기

람다에서 요청이벤트를 출력하면 대충 이렇게 생겼다.

```json
2023-08-10T14:50:51.575Z	7e77d29e-c7d5-4f9c-bbe0-51427ae7be2c	INFO	event:  {
  version: '2.0',
  routeKey: 'POST /attendance',
  rawPath: '/default/attendance',
  rawQueryString: '',
  headers: {
    // 중략
  },
  requestContext: {
// 중략
  },
  body: '{ \n\t "type": "url_verification",\n\t "token": "",\n\t "challenge": "test"\n}',
  isBase64Encoded: false
}

```

RequestBody를 꺼내려면 해당 이벤트의 body를 JSON으로 파싱하여 사용해야함.
