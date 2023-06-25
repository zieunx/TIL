출석 정보는 DynamoDB를 선택했다.

이유는,

- 한번 해보고 싶어서
- AWS Lambda를 활용해서 심플한 서비스를 만들건데 손이 많이 안갔으면 좋겠었음
- 정보 수정이 적고 어떤 DB를 써도 큰 차이가 없을만큼 규모가 작았음

## 설정

// Todo

## 코드

람다에서 사용할 `AttendanceRepository.mjs`이다.

```mjs
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, GetCommand, UpdateCommand, DeleteCommand } from "@aws-sdk/lib-dynamodb";

const ddbDocClient = new DynamoDBClient({ region: "ap-northeast-2" });
const docClient = DynamoDBDocumentClient.from(ddbDocClient);

export class AttendanceRepository {
    static async putItem(item) {
        const putParams = {
            TableName: "Attendance",
            Item: item
        };

        return docClient.send(new PutCommand(putParams));
    }
    
    static async getItem(id) {
        const getParams = {
            TableName: "Attendance",
            Key: { id }
        };

        const { Item } = await docClient.send(new GetCommand(getParams));
        return Item;
    }
    
    static async updateItem(id, updates) {
        const updateParams = {
            TableName: "Attendance",
            Key: { id },
            UpdateExpression: "SET #updates = :updates",
            ExpressionAttributeNames: { "#updates": "updates" },
            ExpressionAttributeValues: { ":updates": updates }
        };

        return docClient.send(new UpdateCommand(updateParams));
    }

    static async deleteItem(id) {
        const deleteParams = {
            TableName: "Attendance",
            Key: { id }
        };

        return docClient.send(new DeleteCommand(deleteParams));
    }
}

```

사실 전체적인 구조는 ChatGPT에게 요청함.


## 학습 정리

- aws에서 공식적으로 제공해주는 DynamoDB SDK를 사용해보았다. [DynamoDB 문서 클라이언트 사용](https://docs.aws.amazon.com/ko_kr/sdk-for-javascript/v3/developer-guide/dynamodb-example-dynamodb-utilities.html)
- js에서 클래스를 선언해서 사용하는 방식에 대해 학습할 수 있었음.

