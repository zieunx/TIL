## Slack APP

워크스페이스 만들고 Slack App을 만들자.

### Slack APP 만들기

[Slack APP 만들기](https://api.slack.com/apps?new_app=1) 를 누르면 쉽게 만들 수 있다.

### 앱설치

앱설치를 위해서는 권한을 일부 부여해줘야 함.
어떤 권한을 줄지는 차차 필요할 때 마다 추가할 거다. (권한이 추가되면 워크스페이스와 앱 연동을 다시 해주어야하는데 Slack 설정페이지에서 이 과정을 잘 지원해주기 때문에 팝업 뜨는거 잘 쫒아가면 된다.

### 슬랙 이벤트 구독 기능

슬랙은 incoming hooks 기능과 event subscription 기능을 제공해준다.

incoming hooks 은 슬랙으로 후킹 메시지를 보내는 기능이다.  
event subscription 은 슬랙에서 발생하는 이벤트를 외부에서 받아볼 수 있는 기능이다.

화살표로 표현해보자면,  
- incoming hooks
    - 내시스템 -> 슬랙
- event subscription
    - 내시스템 <- 슬랙
 
내가 사용하려는 기능은 슬랙에서 내 시스템으로 후킹해주는 기능이기 때문에 'Event Subscription' 을 활성화 해줄거다. 

### Event Subscriptions 연동하기

![스크린샷 2023-08-11 오전 12 32 16](https://github.com/zieunx/TIL/assets/48097396/3aac9e16-c011-4fc1-b4e7-590a91afad96)

1. 앱 관리화면 에서 사이드바 메뉴 > 'Event Subscriptions' 클릭
2. 활성화 후, 연결할 URL 작성
3. 해당 URL에서는 Request Body의 `challenge`값을 그대로 내려주면 된다. `Content-Type`에 맞게 응답을 내려주면 되고 공식문서를 참조하면 쉽다. ([공식문서참조](https://api.slack.com/apis/connections/events-api#challenge))
4. Subscribe to bot events 에 원하는 이벤트를 추가해주면 된다. 나는 메시지가 채널에 게시될 때마다 이벤트를 구독하고 싶다. 'message.channels'(메시지가 채널에 게시) 를 연동해주고 저장버튼을 눌러준다.
5. 페이지 상단에 워크스페이스와 재연동하라고 가이드가 뜬다. 재연동하겠다고 버튼 눌러주면 알아서 재연동해준다. 굿굿.


- 'message.channels' 이벤트에 대한 수신 RequestBody JSON
```json
{
  "token": "...",
  "team_id": "...",
  "context_team_id": "...",
  "context_enterprise_id": null,
  "api_app_id": "...",
  "event": {
    "type": "message",
    "subtype": "channel_join",
    "ts": "...",
    "user": "...",
    "text": "<...> 님이 채널에 참여함",
    "inviter": "...",
    "channel": "...",
    "event_ts": "...",
    "channel_type": "channel"
  },
  "type": "event_callback",
  "event_id": "...",
  "event_time": 1691680753,
  "authorizations": [
    {
      "enterprise_id": null,
      "team_id": "...",
      "user_id": "...",
      "is_bot": true,
      "is_enterprise_install": false
    }
  ],
  "is_ext_shared_channel": false,
  "event_context": ""
}

```

`type`이 'event_callback'으로 오기 때문에 이 값으로 이벤트에 대한 처리를 해주면 되겠다.
