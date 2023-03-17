
# CORS의 이해

### 참고 
- CS Visualized: CORS ( https://dev.to/lydiahallie/javascript-visualized-scope-chain-13pd )


## 메모
회사에서 프로젝트를 하면서 CORS 문제에 부딪히곤 했다. 간단하게


## 개요
'Access to fetched has been blocked by CORS policy'라는 에러는 개발자라면 한번쯤 경험해 본 에러일 것입니다.
비록 이러한 에러를 콘솔에서 빠르게 제거하는 몇 가지 방법이 있다고 하더라도, 오늘은 아무 것도 당연시 여기지 않겠습니다.
대신에 CORS가 실제로 무엇을 하며 왜 우리의 친구가 될 수 있는지 알아보려고 합니다.


프론트엔드에서, 우리는 종종 다른 곳에 위치한 데이터를 보여주고 싶어합니다. 
하지만 우리가 그 데이터를 보여주기 전에, 브라우저는 먼저 서버에 요청을 해서 해당 데이터를 가져와야 합니다.
클라이언트는 요청을 받는 서버가 데이터를 클라이언트에게 다시 보낼 수 있도록, 필요한 모든 정보와 함께 HTTP Request를 보냅니다.

**예를 들어** www.mywebsite.com이라는 웹사이트에서 <- api.website.com에 위치한 서버로부터 사용자 정보를 가져오려고 한다고 해봅시다.
