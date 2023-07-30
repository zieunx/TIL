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

**계층 권한 구성**

```
aws lambda add-layer-version-permission --layer-name xray-sdk-nodejs --statement-id xaccount \
--action lambda:GetLayerVersion  --principal 111122223333 --version-number 1 --output text
```
