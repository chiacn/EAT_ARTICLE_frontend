
# Event Loop의 이해

### 참고 
- JavaScript Visualized: Event Loop ( https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif )


## 메모
이벤트 루프, 개발 인생(짧지만) 내내 들어왔으면서 명확하게 알고 있진 않았었다. 자바스크립트가 싱글스레드 언어인만큼 확실히 알아두어야 할 필요성을 느꼈다.


## Event Loop

먼저, 당신이 왜 이벤트 루프에 대해 알아야 할까요?

자바스크립트는 **싱글 스레드(single-threaded)** 언어이기 때문입니다.  
한번에 하나의 task만 수행될 수 있어요. 별게 아니라고 생각할 수 있지만, 당신이 30초 정도 걸리는 어떤 일을 수행하고 있다고 상상해봅시다.  
이 작업을 수행하는 30초 동안 다른 일은 일어나지 않습니다. (자바스크립트는 기본적으로 브라우저의 main thread에서 작동하며 이 때 전체 UI는 멈추게 됩니다.)

운 좋게도, 브라우저는 우리에게 자바스크립트는 제공하지 않는 기능을 제공합니다.  
그건 바로 **Web API** 입니다. Web API는 DOM API와 setTimeout, HTTP Request 등을 포함하고 있어요.  
이들 기능은 우리가 어떤 async, non-blocking 동작을 생성할 수 있도록 도와줍니다.

우리가 어떤 함수를 호출할 때, 이 함수는 CALL STACK이라는 곳에 추가됩니다.  
CALL STACK은 자바스크립트 엔진의 일부분입니다. 브라우저에 국한되는건 아니에요.  
( CALL STACK은 말 그대로 Stack 구조이고 Last In First Out(LIFO) 구조를 따릅니다. )

<img src="https://user-images.githubusercontent.com/68171739/226222046-8edc49ec-6ef3-49f5-a61e-fae2a8ef6a59.gif"  width="700" height="400">

아래 그림에서 'respond' 함수는 setTimeout 함수를 리턴합니다. setTimeout은 WEB API를 통해 제공됩니다.  
**setTimeout을 통해 우리는 main thread를 차단하지 않고 작업을 지연시킬 수 있습니다.** 
setTimeout 함수에 전달한 ***콜백 함수***, 즉 화살표 함수 '() => { return 'Hey'}' 는 **Web API에 추가됩니다.**  
그러는동안 setTimeout 함수와 respond 함수는 stack에서 제거되고 둘 다 그들의 값을 리턴합니다.

<img src="https://user-images.githubusercontent.com/68171739/226223149-816a2a80-a68b-4563-9889-4960ed4f6d78.gif"  width="700" height="400">

**WEB API에서 타이머는 우리가 전달한 두 번째 인수인 1000ms동안 실행됩니다.**  
콜백은 즉시 CALL STACK에 추가되지 않습니다. 대신에 Queue라는 곳에 전달 됩니다.

<img src="https://user-images.githubusercontent.com/68171739/226223369-d7710ab6-8a1d-4e39-8d6c-73443e744205.gif"  width="700" height="400">

위의 설명은 우리를 다소 혼란스럽게 할 수 있습니다.  
콜백 함수가 1000ms 후에 CALL STACK에 추가된다는 의미가 아닙니다. (따라서 값을 반환함.)
단순히, 1000ms 후에 Queue에 추가된다는 이야기입니다. Queue에 추가된 후, 함수는 자신의 차례를 기다려야 합니다.

자, 이 순간은 우리가 기다려온 순간입니다. Event Loop가 유일한 일을 할 시간이죠.  
그것은 바로 **Queue를 CALL STACK에 연결**하는 일입니다!  
만약 CALL STACK이 비어 있다면, 그래서 모든 함수가 그들의 값을 리턴하고 STACK에서 제거된 경우,  
Queue의 첫 번째 항목이 CALL STACK에 추가됩니다. 이 경우 다른 함수는 호출되지 않았습니다.
즉 콜백 함수가 대기열의 첫 번째 항목일 때까지 호출 스택이 비어있었습니다.


<img src="https://user-images.githubusercontent.com/68171739/226226682-d2e3ae86-8f93-4838-9dcc-bfb35b66b0de.gif"  width="700" height="400">

콜백은 CALL STACK에 추가되고, 호출됩니다. 그리고 값을 리턴한 후 STACK에서 제거됩니다.

<img src="https://user-images.githubusercontent.com/68171739/226226689-29456e9b-d323-490f-aabf-374c6824aad3.gif"  width="700" height="400">


아티클을 읽는 것은 재밌지만, 반복적으로 작업해야 익숙해질 겁니다. 아래 내용을 실행하면 콘솔에 무엇이 기록되는지 알아봅시다.


    const foo = () => console.log("First");
    const bar = () => setTimeout(() => console.log("Second"), 500);
    const baz = () => console.log("Third");

    bar();
    foo();
    baz();


이해하셨나요? 위 코드를 브라우저에서 실행할 때, 무슨 일이 일어나는지 살펴볼까요?

<img src="https://user-images.githubusercontent.com/68171739/226227161-441a1245-b113-4b2b-8236-9d128e6ee4ec.gif"  width="700" height="400">

1. bar를 호출합니다. bar는 setTimeout 함수를 호출합니다.

2. 우리가 setTimeout 함수에 전달한 콜백은 Web API에 추가됩니다. setTimeout 함수와 bar는 모두 CALL STACK에서 제거됩니다.

3. 타이머가 실행되고, 그러는 동안 foo 함수가 호출되어 'First'라는 로그를 남깁니다. foo는 undefined를 리턴하고,
baz 함수가 호출됩니다. baz가 호출되는 동안 (bar 함수에서 보내진) 콜백이 Queue에 추가됩니다.

4. baz는 'Third'라는 로그를 남깁니다. 그 후 Event Loop는 CALL STACK이 비어 있는 것을 확인한 후, (bar에서 보낸)콜백이 CALL STACK 에 추가됩니다.

5. 콜백은 'Second'라는 로그를 남깁니다.


