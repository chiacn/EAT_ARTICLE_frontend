
# CORS의 이해

### 참고 
- CS Visualized: CORS ( https://dev.to/lydiahallie/javascript-visualized-scope-chain-13pd )


## 메모
회사에서 프로젝트를 하면서 CORS 문제에 부딪혔을 때, 관련 개념을 찾아보긴 했지만
현상을 해결하는게 최우선이었다. 위의 아티클을 통해 CORS에 대해 더 깊이 이해하고자 한다.


## 개요
CORS - Cross-origin-resource-sharing

'Access to fetched has been blocked by CORS policy'라는 에러는 개발자라면 한번쯤 경험해 본 에러일 것입니다.  
비록 이러한 에러를 콘솔에서 빠르게 제거하는 몇 가지 방법이 있다고 하더라도, 오늘은 아무 것도 당연시 여기지 않겠습니다.  
대신에 CORS가 실제로 무엇을 하며 왜 우리의 친구가 될 수 있는지 알아보려고 합니다.  


프론트엔드에서, 우리는 종종 다른 곳에 위치한 데이터를 보여주고 싶어합니다. 
하지만 우리가 그 데이터를 보여주기 전에, 브라우저는 먼저 서버에 요청을 해서 해당 데이터를 가져와야 합니다.  
클라이언트는 요청을 받는 서버가 데이터를 클라이언트에게 다시 보낼 수 있도록, 필요한 모든 정보와 함께 HTTP Request를 보냅니다.  

**예를 들어** 'www.mywebsite.com' 이라는 웹사이트에서 <- 'api.website.com' 에 위치한 서버로부터 사용자 정보를 가져오려고 한다고 해봅시다.

<img src="https://user-images.githubusercontent.com/68171739/225825480-834483a2-8358-4835-add1-38e7eb906106.gif"  width="700" height="400">

위의 내용은 정상 작동 합니다. HTTP Request가 서버에 정상적으로 보내졌고, JSON data를 응답해주었습니다.

**이번엔** 하지만 다른 도메인으로부터 같은 HTTP Request를 보내볼까요?
이번엔 Request가 www.mywebsite.com이 아니라 www.anotherdomain.com에서 보내졌습니다.

<img src="https://user-images.githubusercontent.com/68171739/225826134-b491d232-98d9-483a-a61f-643b21e522a8.gif"  width="700" height="400">


웁스..  
***같은 Request*** 를 보냈는데 브라우저는 에러를 보여줍니다.
방금 우리는 CORS가 작동하는 모습을 본 것입니다.이 오류가 왜 발생하였고 정확히 무슨 의미인지 알아봅시다!


# Same-Origin Policy
웹은 Same-Origin Policy(동일 출처 정책)이라는 것을 적용합니다.
기본적으로 우리는 <U>요청의 출처와 동일한 출처에 위치한 리소스에만 접근할 수 있습니다.</U>
예를 들어, https://mywebsite.com/image1.png에 위치한 이미지를 불러오는 것은 전혀 문제가 없습니다.

**하지만 리소스가 다른 (Sub)domain, protocol, port에 위치해 있으면 Cross Origin(교차 출처)리소스입니다.**

<img src="https://user-images.githubusercontent.com/68171739/225829434-8cce9c20-d199-498d-a6b6-95480f8adbbd.png"  width="700" height="400">

(참고 - URL(Uniform Resource Locator)의 구조) 
출처 : https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F#CORS_(Cross_Origin_Resource_Sharing)

<img src="https://user-images.githubusercontent.com/68171739/225830156-0903a819-c9d3-4e4d-b8e8-8c4957fb39cf.png"  width="700" height="400">

- Protocol: http, https  
- Host: 사이트 도메인  
- Port: 포트 번호  
- Path: 사이트 내부 경로
- Query string: 요청의 key와 value 값
- Fragment: 해시태그
(즉, 출처(Origin)는 Protocol, Host, Port 이 3가지만 동일하다면 동일 출처로 판단한다는 것. )

<br>

    좋습니다, 그런데 왜 same-origin policy(동일 출처 정책)이 존재하는 걸까요?

same-origin policy가 존재하지 않는다고 가정해봅시다.  
어느 날 당신은 Facebook에서 이모가 보낸 바이러스 링크 중 하나를 실수로 클릭하였습니다.  
그런데 이 링크는 당신을 악의적인 웹사이트로 리다이렉션하고, 은행 웹 사이트를 로드하는 iframe이 포함하고 있었습니다.  
악의적인 웹사이트의 개발자들은 이 웹사이트가 iframe에 접근하고 은행 웹사이트의 DOM과 상호작용하여 당신의 계좌에서 돈을 보내도록 만듭니다..

...!

이것은 엄청난 보안 위험입니다.
우리는 아무나 그냥 모든 것에 접근하게 두고 싶지 않습니다.


운 좋게도 Same-origin policy는 우리를 도와 줄 수 있습니다.  
**Same-origin policy은 우리가 같은 출처로부터의 리소스에만 접근할 수 있게 해줍니다.**  
이 경우, www.evilwebsite.com 가 Cross-orgin 리소스인 www.bank.com 에 접근하려고 시도했습니다.  
Same-origin policy는 이러한 접근을 막아줍니다.



    음. 알겠습니다.. 그런데 CORS가 뭐가 문제라는거죠?


## Client-side CORS

Same-origin policy는 사실 스크립트에만 적용되지만, 브라우저는 이 정책을 Javascript 요청에 대해서도 확장했습니다.  
기본적으로 우리는 동일한 출처에서 가져온 리소스에만 접근할 수 있습니다.

<img src="https://user-images.githubusercontent.com/68171739/225839513-d1aaae65-d7cc-4650-b9cd-9c0453733a24.gif"  width="700" height="400">

음...
그런데 우리는 종종 다른 출처의 리소스에 접근해야하지 않나요?  
아마 우리 프론트엔드는 백엔드 API와 상호작용해 데이터를 로드해야 될텐데요..?  
Cross-origin Requests를 안전하게 적용하기 위해, 브라우저는 CORS(Cross Origin Resource Sharing)라는 매커니즘을 적용합니다.  

CORS는 Cross-Origin Resource **Sharing**(교차 출처 리소스 공유)의 약자입니다.  
브라우저는 동일한 출처에 위치하지 않은 리소스에 대한 접근을 허용하지 않지만, CORS를 사용하여 이러한 보한 제한을 조금 변경하면서도 리소스에 안전하게 엑세스 할 수 있습니다.

사용자 에이전트(예를 들면 브라우저)는 원래대로라면 막혔어야 했던 Cross-origin requests(교차 출처 요청)를 허용하기 위해서
CORS(Cross Origin Resource Sharing)을 사용하는데, **이는 HTTP Response의 특정 CORS 관련 Header 값을 기반을 작동합니다.**

Cross-origin Request가 만들어졌을 때, 클라이언트는 자동적으로 extra header를 HTTP Request: Origin에 추가합니다. 
Origin header의 value는 Request가 발생한 출처입니다.

<img src="https://user-images.githubusercontent.com/68171739/225845165-f76fc9bb-f160-49a4-9a98-7860a2ffd9d2.gif"  width="700" height="400">


브라우저가 cross-origin resource를 허용하기 위해서 브라우저는 서버의 Response로부터 
해당 서버가 cross-origin requests를 허용하는지 여부를 나타내는 특정 header를 요구합니다!



## Server-side CORS

위에서 살펴본 Client Side에서의 CORS를 이제 Server Side에서 바라봅시다.  
서버 개발자의 입장에서, ***HTTP Response에 headers를 추가함으로써 Cross-origin requests를 허용할 것인지***를 명확하게 하는 것이 중요합니다.  
(CORS Response는 Access-Control-*로 시작합니다.)  
이러한 CORS Response header를 기반으로, 브라우저는 이제 특정 Cross-origin Response들을 허용할 수 있게 됩니다.

우리가 사용할 수 있는 몇 개의 CORS Header들이 더 있긴 하지만, 브라우저가 Cross-origin resource 접근을 허용하기 위해 필요한 header는 하나입니다.
바로 **Access-Control-Allow-Origin**입니다.

**Access-Control-Allow-Origin**의 value는 어느 출처들이 리소스들에 접근할 수 있는지를 서버로부터 명시합니다.  
예를 들어, https://mywebsite.com 가 접근해야하는 서버를 개발한다고 한다면, 우리는 Access-Control-Allow-Origin header에 그 도메인을 추가할 수 있습니다.

<img src="https://user-images.githubusercontent.com/68171739/225851626-712c775c-bd03-4c03-a591-dfe2518489cc.gif"  width="700" height="400">

이제 더 이상 Same-origin policy가 https://api.mywebsite.com 으로부터 리소스를 받는 것을 제한할 수 없을 것입니다.  
만약 https://mywebsite.com에서 -> https://api.mywebsite.com 으로 Request를 보내는 경우

<img src="https://user-images.githubusercontent.com/68171739/225855271-446b5002-8122-49d0-8eb2-83bb248d2233.gif"  width="700" height="400">

위처럼,  
브라우저 내부의 CORS 매커니즘은 서버로부터 Response된 Access-Control-Allow-Origin Header value가 클라이언트로부터 Request된 Origin의 value와 같은지를 검사할 것입니다.

위 케이스에서, Request의 Origin은 https://www.mywebsite.com 은 Response header의 Access-Control-Allow-Origin 목록에 있습니다.
아래와 같이 말이죠.

<img src="https://user-images.githubusercontent.com/68171739/225856275-99cb203c-188c-4ce6-9186-04e217e45984.gif"  width="700" height="400">

하지만 Request의 Origin value와 Response의 Access-Control-Allow-Origin의 value와 다르다면?
아래와 같은 에러가 나게 됩니다.

<img src="https://user-images.githubusercontent.com/68171739/225856683-55069817-2880-4d6a-ac16-d83a7ffbcaa8.gif"  width="700" height="400">

위의 케이스에서 제공된 Origin(출처)는 https://www.anotherwebsite.com 였습니다.  
하지만 서버가 Access-Control-Allow-Origin Header에서 해당 Origin을 리스트에 포함하지 않았습니다.
따라서 CORS는 해당 Request를 막았습니다.

    - 참고 : CORS는 wildcard '*'를 허용합니다. 이는 모든 Origin으로부터의 접근이 허용된다는 의미입니다. 조심해서 씁시다!




Access-Control-Allow-Origin은 많은 CORS Header들 중 하나입니다.  
서버 개발자는 명확한 요청을 위해 서버의 CORS policy들을 추가할 수 있습니다.

***Access-Control-Allow-Methods***
다른 공통 Header는 Access-Control-Allow-Methods Header입니다.  
CORS는 Cross-origin Request가 list에 있는 방법대로 전송되었을 경우에만 허용합니다.

<img src="https://user-images.githubusercontent.com/68171739/225886187-d372745e-6e69-4f25-a641-92435aec1078.gif"  width="700" height="400">

위 경우에서는 GET, POST, PUT 메서드가 있는 요청만 허용됩니다.
PATCH와 DELETE 등 다른 메서드는 차단됩니다.

- 잠깐 PUT, PATCH, DELETE에 대해 언급하자면, CORS는 그들을 조금 다르게 다룹니다. 이들 Request들은 preflight request라고 불립니다.



## Preflighted Requests (사전 요청)

CORS에는 두 가지 타입의 Request가 있습니다.
하나는 Simple Request, 다른 하나는 preflighted Request 입니다.
Request가 Simple인지 Preflighted인지는 Request 안에 있는 값에 따라 정해집니다.
(너무 걱정하진 마세요, 안 외워도 됩니다. lol)

    1. GET, POST  
    Request는 GET이나 POST 메서드일 때 Simple하며 어떠한 custom header들도 가지지 않습니다.

    2. PUT, PATCH, DELETE  
    PUT, PATCH, DELETE와 같은 다른 메서드는 사전요청됩니다.

<br>

    그래서 preflighted request가 무슨 의미며 무슨 일이 일어나는거죠?


실제 Request를 보내기 전에, 클라이언트는 Preflighted Request를 생성합니다.  
Preflighted Request에는 Access-Control-Request-* header에 있는 실제 Request에 대한 정보가 포함되어 있습니다.

이는 서버에게 브라우저가 만드려는 실제 Request에 대한 정보. 즉 Request의 메서드는 무엇인지, 추가 header는 무엇인지 등에 대해 알려줍니다.

<img src="https://user-images.githubusercontent.com/68171739/225959947-efc3445f-8d5f-4001-896e-110b4bbea8ab.gif"  width="700" height="400">


서버는 이 Preflighted Request를 받습니다. 그리고 빈 HTTP Response를 서버의 CORS headers와 함께 보냅니다.  
브라우저는 Preflight Response를 받는데, 여기에는 CORS headers를 제외하면 다른 데이터가 포함되어 있지 않으며
HTTP Request가 허용되어야 하는지의 여부를 체크할 뿐입니다.


<img src="https://user-images.githubusercontent.com/68171739/225961276-4c673d59-e1b1-452e-b2e2-6d7553da9f8c.gif"  width="700" height="400">

HTTP Request가 허용되어야 하는 경우가 맞다면, 브라우저는 서버에게 실제 Request를 서버에 보냅니다.   
그리고 우리가 요청한 Data와 함께 Response됩니다.


<img src="https://user-images.githubusercontent.com/68171739/225961325-4532e82d-842d-463a-ab88-c6febd2c27f0.gif"  width="700" height="400">

하지만 HTTP Request가 허용되지 않아야 하는 케이스라면, CORS는 Preflighted Request를 막을 것입니다.  
실제 Request는 또한 받아들여지지 않습니다.

Preflighted Request는 CORS policy가 활성화되지 않은 서버의 리소스에 접근하는 것을 막는 훌륭한 방법입니다.  
서버는 이제 원치 않는 Cross Origin Request들로부터 보호됩니다.

    서버로의 왕복횟수를 줄이기 위하여, 우리는 Preflighted Response를 캐싱할 수 있습니다. 
    이는 CORS Requests에 Access-Control-Max-Age를 추가함으로써 가능합니다.
    이 방법으로 Preflighted Response를 캐싱하면 브라우저는 새로운 Preflighted Request를 보내는 대신 
    캐싱된 Preflighted Response를 사용할 수 있습니다.


## Credentials

Cookies, authorization headers, TLS certificates는 기본적으로 Same-Origin Request에만 설정됩니다.  
하지만 Cross Origin Request에서 이러한 자격 증명을 사용하려 할 수도 있습니다.  
아마도 서버가 유저를 식별하기 위해 사용할 수 있는 Request에 Cookie를 포함하길 원할 수도 있습니다.  

CORS가 기본적으로 자격 증명을 포함하지는 않지만 Access-Control-Allow-Credentials CORS header를 추가하여 이를 변경할 수 있습니다.  

만약 Cookie나 다른 권한 header를 Cross Origin Request에 포함하고자한다면, Request에서 withCredentials 필드를 true로 설정하고
Access-Control-Allow-Credentials header를 Response에 추가하는 것이 필요합니다.

<img src="https://user-images.githubusercontent.com/68171739/225969719-ea757c07-e6b3-474f-9d9c-15c2144a9847.gif"  width="700" height="400">

