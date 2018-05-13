# 메시징 패턴
- 함수형 프로그래밍이 부작용을 피하려 하는 것처럼 메시지 기반 시스템도 이와 같다.
- 수백 대의 컴퓨터로 확산될 수 있는 뛰어난 확장성 제공
- 단일 애플리케이션에서 메시징은 낮은 결합성을 보장하고 테스트를 용이하게 해준다

## 메시지란
- 어떤 의미를 가진 연관된 데이터 비트들의 집합
- 여기에 몇 가지 추가 의미를 제공해 정의된다
- `AddUser` 메시지와 `RenameUser` 메시지는 다음과 같은 필드를 가질 수 있다.
    + User ID
    + User name
- 그러나 사실 이름있는 컨테이너에 포함된 필드는 서로 다른 의미를 가짐(??)
> 컴포넌트 별로 필드의 의미가 다르다는 뜻으로 해석 함...


*메시지는 일반적으로 애플리케이션, 비지니스에서의 특정 동작과 관련돼 있다.*

- 메시지에는 수신기에서 어떤 동작을 수행하는 데 필요한 모든 정보가 들어있다
- 예를 들어 `RenameUser` 메시지에는 어떤 구성요소에서
    + 사용자ID와 사용자이름의 관계를 추적하고
    + 사용자이름을 업데이트 하는 데 필요한 충분한 정보를 포함하고 있다.

*수많은 메시징 시스템, 특히 애플리케이션 간 통신하는 시스템은 봉투를 정의한다.*

- 봉투는 메시지 감사, 라우팅, 보안에 도움이 되는 메타데이터를 포함한다.
- 봉투의 정보는 비지니스 프로세스의 일부가 아니라 인프라의 일부다.
- 보안이 일반적인 비지니스 워크플로우 밖에 존재하며
- 애플리케이션의 다른 부분이 이를 소유한다면 봉투안에 보안 주석이 있어도 무관하다.

---
*메시지 예시*

- **봉투**
    + 라우팅 정보
    + 메시지 ID
    + Corrolation ID(?)
- **봉투 내부**
    + User ID
    + User name

---

메시지가 한 번 작성하면 변경되지 않도록 밀봉해야한다.
이는 감사(_auditing_)와 재생(_replaying_)같은 작업을 쉽게 만든다.

메시징은 단일 프로세스 내의 통신에 사용할 수도, 애플리케이션 간 통신에서도 사용 가능하다.

- 대부분의, 앱 내에서 메시지를 전송하는 것과 앱간 전송하는 것에 차이는 거의 없다.
    + 유일한 차이는 동시성 처리로, 단일 프로세스 내에서 메시지는 동기적 방식으로 처리된다.
    + 이는 다음 과정을 진행하기 위해서는 메시지의 처리가 완료돼야 함을 의미한다.


- 비동기 시나리오에서 메시지는 나중에 처리될 수 있다.
    + 때로, 오랜 시간 후에 처리되기도 한다.
    + 외부 서버를 호출할 때, 비동기 방식은 좋은 방법이다.
    + JS의 단일 스레드 특성으로 심지어 하나의 프로세스 내에서도 비동기 메시징 사용을 권장한다.
    + 비동기 메시징 사용시, 동기 메시징을 위해 만든 가정이 안전히 중단되오록 세심한 주의가 필요하다.
    + 예를 들어 메시지가 보내진 순서대로 응답할 것이라는 가정은 안전하지 않다.

메시지에는 

- 일어날 일을 지시하는 `명령`과
- 일어난 일에 대해 통지하는 `이벤트`의 두 가지 다른 형태가 있다. 

### 명령

`<verb><object>`형식의 이름을 갖는다. `InvadeCity` 처럼.

예를 들어 사용자 주소 변경 명령을 생각해보자.

단순히 `ChangeAdrdress`라는 명령을 호출하면 아무런 추가 정보를 제공할 수 없다.

이보다 **왜 주소가 변경되는지 이유를 제공**하는 것이 좋다.

사용자가 이사했는지, 원래 입력된 주소가 잘못된 것일지 이들은 다른 동작을 수반한다.

이사한 사용자에게는 이사 선물을 보내고, 잘못된 주소를 수정한 경우 그렇지 않을 것이다.

---

사용성 증대를 위해 메시지에 비지니스적 의미를 포함시키는 것이 좋다.

메시지를 정의하고 그들이 어떠헤 비지니스 내에서 구성될 수 있는지 그 자체로 좋은 연구 분야다.

**도메인 주도 디자인**이 주요 키워드 이다.

---

명령은 특정 구성요소를 대상으로 작업을 수행하도록 지시하는 **지시문**이다.

브라우저 관점에서, 명령은 버튼을 클릭할 때 발생한다고 생학할 수 있다.

명령은 이벤트로 변환돼 이벤트 리스너에 전달된다.

---

오로지 하나의 종단점만이 특정 명령을 수신해야 한다. 

이는 하나의 컴포넌트만이 동작을 수행하는 책임을 가져야 함을 의미한다.

하나 이상의 종단점에서 명령이 수행되면, 경합 조건이 발생한다.

혹, 하나의 종단점이 명령을 받아들이고 다른 종단점은 유효하지 않은 것으로 여겨 거부하면 어떨까?

심지어 거의 동일한 명령들이 다수 발생한 경우에도, 그들은 합쳐지면 안 된다.

예를 들어, 왕이 모든 장군들에게 명령을 내리면 각각의 명령이 따로따로 장군들에게 전달돼야 한다.

---

명령의 종단점은 하나이므로, 종단점이 명령을 확인하거나, 취소하는 것도 가능하다.

명령을 취소해도 나머지 애플리케이션에 영향을 주지 않는다.

그리고 명령이 동작하면, 하나 이상의 이벤트가 발생한다.

### 이벤트

어떤 일이 일어 났음을 알리는 특별한 메시지.

단순히 어떤 일이 일어 났음을 알리는 통지이므로 변경/취소는 아무 소용이 없다.

과거 시제로 작성된다. `InvadeCity` 명령이 성공하면 `CityInvaded`가 된다.

---

명령과 달리 이벤트는 여러 컴포넌트에서 수신할 수 있다. 경합조건이 발생하지 않는다.

메시지 핸들러는 메시지 변경이나 다른 메시지의 전달을 방해할 수 없다. 각 핸들러는 격리돼 있다.

브라우저 이벤트는 앞선 이벤트 정의를 충족하지 못한다.

브라우저의 이벤트 핸들러는 이벤트를 취소하고 다른 핸들러로 전파되는 것을 막을 수 있기 때문이다.

---

일부 시스템에서 이벤트는 다형성을 지닌다.

누군가 고용돼 `IsHiredSalary` 이벤트가 발생했을 때, `IsHired`라는 후속 메시지를 만들 수 있다.

`IsHiredSalary` 이벤트 수신 시, `IsHiredSalary`와 `IsHired`를 구독하는 두 핸들러가

구동할 수 있게 만들어 준다. 자바스크립트는 기본적으로 다형성을 지니지 않았기에 구현이 쉽지 않다.

```js
var IsHiredSalary = {
    __name: "isHiredSalary",
    __alsoCall: ["isHired"],
    employeeId: 77,
}
// 봉투 일부에 `_`를 사용해 필드를 표시
// 이런 식으로 메시지와 봉투에 플드로 구분되는 메시지를 구성할 수 있다.
```

1. `사용자` -> 생성(폼 작성) -> `웹서버`
2. `웹 서버` -> 사용자 생성 명령 전송 -> `명령 핸들러`
3. `명령 핸들러` -> `하나 이상의 이벤트 리스너`

_이벤트 리스너들은 시스템 관리자에게 확인 메일을 보낼 수 있다._

## 요청과 응답

메시징에서 흔한 패턴은 요청-응답 패턴

**이 패턴은 애플리케이션의 다른 부분이 소유하고 있는 데이터를 검색하는 방법**

- 대부분의 경우 명령 전송은 비동기적 동작이다. 
- 명령이 발생해도 애플리케이션의 흐름은 계속 진행 된다.
- 이 때문에, ID로 기록을 검색하는 것과 같은 동작이 쉽지 않다.
- 대신 기록 검색 명령을 보내고, 관련 이벤트가 반환될 때까지 기다려야 한다.

대부분의 이벤트는 리스너에 의해 구독 될 수 있다. 

_하지만 요청-응답 패턴에서 다수의 이벤트 리스너를 가지는 것은 권장하지 않는다._

### 예제(까마귀 메시징)

###### 버스 구축
- 버스는 메시지를 분배하는 단순한 메커니즘
- 예제처럼 프로세스 내부에 구현할 수도, 밖에 구현할 수도 있다.

###### 프로세스 밖에 구현하는 경우
- `ZeroMQ` 부터 `RabbitMQ`에 이르는 DB와 클라우드 기반 옵션 들이 있다.
- 이 시스템들은 메시지의 신뢰성과 내구성에 대해 각가 다른 동작을 보여준다.
- 애플리케이션 구축 방법을 결정할 수 있기에, 메시지 분배 시스템 동작 방식을 조사 해야하다.
- 이들 시스템들은 신뢰할 수 없는 애플리케이션을 다루는 데 다른 접근 방식을 사용한다.

```js
function CrowMailBus (requestor) {
    this.requestor = requestor;
    this.responder = new CrowMailResponder(this);
}

CrowMailBus.prototype.send = message => {
    if (message.__from === 'requestor') {
        this.responder.processMessage(message);
    } else {
        this.requestor.processMessage(message);
    }
}
```

###### 잠재적 실수
- 클라이언트에서 수신된 메시지의 순서가 보낸 순서가 보장되지 않는 것이다.
- 이를 위해 상관 ID(correlation ID)를 포함 하는것이 일반 적이다.
- 이벤트가 발생하면, 올바른 이벤트 핸들러를 사용할 수 있도록 발신자에서 ID를 포함해 보낸다.

예제에선 버스 경로를 하드코딩 해지만,

- 실제 버스는 송신기가 배달을 위해 종단점의 주소를 지정할 수 있도록 작성해야 한다.
- 또는 수신기가 관심 있는 특정 메시지에 대해 등록할 수도 있다.
- 그러면 버스는 메시지를 특정 수신기에만 전달하는 책임을 가진다.
- 예제에서 버스는 처리하는 메시지 이름을 따서 작성됐지만, 확장성을 고려한 방법은 아니다.

```js
/**
 * 요청자 구현
 * 요청을 보내는 메서드와
 * 버스로부터 응답을 수신하는 메서드
 * 두 개만 포함한다.
 */

function CrowMailRequestor () {}

CrowMailRequestor.prototype.Request = () => {
    var message = {
        __messageDate: new Date(),
        __from: 'request',
        __corrolationId: new Guid(),
        body: 'Invade Moat Cailin'
    };

    var bus = new CrowMailBus(this);
    bus.Send(message);
}

// 현재는 단순히 응답 로그를 기록하지만
// 실제 시나리오에서는 UI 업데이트나 다른 메시지 발송 처럼
// 더 많은 작업을 수행할 것이다.
CrowMailRequestor.prototype.processMessage = message => {
    console.dir(message);
}
```

```js
/***
 * 응답자 구현
 ***
 * 단순히 메시지를 받아서 
 * 다른 메시지로 이에 응답한다.
 */
function CrowMailResponder (bus) {
    this.bus = bus;
}

CrowMailResponder.prototype.processMessage = message => {
    var response = {
        __messageDate: new Date(),
        __from: 'responder',
        __corrolationId: message.__corrolationId,
        body: 'Okay, Invaded'
    };

    this.bus.Send(response);
}
```

예제에서는 모든 것을 동기적으로 처리했지만, 비동기로 작성하려면 버스를 교체한다.

Node.js의 경우 이벤트 루프를 통해 다음 함수로 연기하는 

`process.nextTick`을 사용해 이 작업을 수행할 수 있다.

웹 컨텍스트라면, 웹 워커를 사용해 다른 스레드에서 이를 처리할 수 있다.

```js
CrowMailBus.prototype.Send = message => {
    var _this = this;
    if (message.__from === 'requestor') {
        process.nextTick(() => {
            return _this.responder.processMessage(message);
        });
    } else {
        process.nextTick(() => {
            return _this.requestor.processMessage(message);
        });
    }
}
```

이런 접근 방식은 메시지가 처리되기 전에 다른 코드를 수행할 수 있게 됐다.

각 버스에서 `Send` 이후에 인쇄문을 추가하면 다음과 같은 출력 결과를 얻을 수 있다.

```
Request sent!
Reply sent
{
    __messageDate: Mon Aug 05 2018 18:30:05 GMT+0900 (KST),
    __from: 'responder',
    __corrolationId: 0.58504819293,
    body: 'Okay, invaded'
}
```
인쇄문이 메시지가 처리되기 전에 실행되고, 이처리가 반복적으로 수행되는 것을 알 수 있다.

## 발행과 구독

발행-구독은 처리 코드에서 이벤트를 분리해주는 강력한 도구다.

메시지 발행자 입장에서, 메시지를 보내느 순간 책임은 끝난다.

관심 메시지 타입을 등록하는 것은 리스너 책임이다.

> 물론 불량 서비스의 등록을 허용치 않도록 보안 수단을 제공하고 싶을 수도 있다.

완벽한 라우팅, 복수의 메시지를 보낼 수 있는 서비스 버스를 업데이트를 해보자.

전송함수에선 `Send`를 유지한 채 `Publish` 메서드를 만든다.

까마귀로는 `broadcasting`이 어려워서 까마귀 방송 센터가 있다 가정한다. 거의 버스와 같다.

###### 메시지의 이름 함수로 동작하는 라우터를 작성한다.
- 메시지 속성 중 하나를 사용해 메시지 라우팅을 할 수 있다.
- 예로, 리스너는 `amount` 필드가 $10,000보다 큰 모든 `invoicePaid` 메시지를 구독할 수 있다.
- 버스에 이런류의 로직을 추가하면 속도를 느리게 하고 디버깅을 어렵게 만든다.
- 버스가 비지니스 프로세스 결합 엔진이 되지 않도록 작업한다.

```js
// 먼저 발행된 메시지를 구독하는 일이다.
CrowMailBus.prototype.Subscribe (messageName, subscriber) => {
    this.responders.push({
        messageName, subscriber
    });
}
```

- `Subscriber` 함수는 메시지 핸들러와 소비할 메시지 이름을 추가한다.
- 응답 배열은 단순히 핸들러의 배열이다.

```js
// 메시지가 발행되면, 배열을 돌며
// 해당 이름의 메시지를 등록한 각 핸들러를 호출한다.
CrowMailBus.prototype.Publish = message => {
    for (var i = 0; i < this.reponders.length; i++) {
        (b => {
            process.nextTick(() => {
                return b.subscriber.processMessage(message);
            })
        })(this.responders[i]);
    }
};
```

이제 `Send` 대신 새로운 `Publish` 메서드를 사용해 `CrowMailResponder`를 변경할 수 있다.

```js
CrowMailResponder.prototype.processMessage = message => {
    var response = {
        __messageDate: new Date(),
        __from: 'responder',
        __corrolationId: message.__corrolationId,
        __messageName: 'KindomIvaded',
        body: 'Okay, invaded'
    };
    this.bus.Publish(response);
    console.log('Reply published');
}
```

앞서 자신의 버스를 생성하기 위해 `CrowMailRequestor`를 허용하는 대신,

외부에서 버스의 인스턴스를 받도록 수정해야 한다.

단순히 `CrowMailRequestor` 지역 변수에 할당한다.

마찬가지로 `CrowMailResponder` 역시 버스의 인스턴스를 받아야 한다.

이를 위해 단순히 새로운 버스 인스턴스를 생성하고 요청자에게 전달하면 된다.

```js
var bus = new CrowMailBus();
bus.Subscribe('KindomInvaded', new TestResponder1());
bus.Subscribe('KindomInvaded', new TestResponder2());
var requestor = new CrowMailRequestor(bus);
requestor.Request();

function TestResponder1 () {}
TestResponder1.prototype.processMessage = message => {
    console.log('Test Responder 1: got a message');
}
```

실행 결과는 다음과 같다.

```
Message sent!
Reply published
Test responder 1: got a message
Test responder 2: got a message
Crow mail responder: got a message
```

## 팬아웃과 팬인

발행-구독 패턴 덕에 여러 노드로 문제를 분산하는 것이 가능해졌다.

싱글 코어 프로세서는 흔치 않고, 클라우드 컴퓨팅이 보편화 됐다.

멀티코어를 활용한 SW 작성은 우리 시대 당면한 과제다.

하지만 스레드를 직접 다루는 것은 재앙으로 가는 지름길이다.

잠금/경합 문제는 개발자가 풀어야할 어려운 숙제다.

일부 유형의 문제는, 쉡게 하위 문제로 나누어 처리할 수있다.

`처치 곤란 병렬 문제`로 불린다.

---

메시징은 문제로부터 입/출력을 전달하는 메커니즘을 제공한다.

검색과 같이 쉽게 병렬화 할 수 있는 문제는, 입력을 하나의 메시지로 묶을 수 있다.

이 경우 검색어를 포함하며, 또한 메시지는 검색할 문서를 포함한다.

- 문서가 10,000개 라면 
- 검색 범위를 2,500개로 나눈다.
- 검색어, 검색할 문서 범위를 포함하는 다섯 개의 메시지를 발행한다.

> 분할 -> 병렬 처리 -> 재결합

1. 각 검색 노드는 메시지를 선택하고 검색을 수행한다.
2. 결과는 다시 노드로 보내지고, 노드는 메시지를 수집하여 하나로 묶는다.
3. 이 값이 클라이언트로 반환된다.

이는 매우 간소화 한것 이다. 

수신 노드는 자체적으로 책임을 지고 있는 문서들의 목록을 유지할 가능성이 높다.

이는 원 발행 노드가 검색된 문서에 대해 전혀 알지 못해도 되게 해준다.

심지어 검색 결과는 조합을 하는 클라이언트에 직접 반환될 수도 있다.

---

브라우저에서도 웹워커를 사용해 팬아웃-팬인 접근 방식으로 여러 코어에 계산을 분배할 수 있다.

물약을 여러 재료의 조합으로 만들 때, 매우 복잡한 계산이 필요하다고 가정하자. 이를 워커에 나눈다.

먼저 모든 분산된 재료가 결합되면 호출되는 `complete()` 함수와 `combine()` 메서드를

포함하는 `combiner`부터 시작한다.

```js
function Combiner () {
    this.waitingForChunks = 0;
}
Combiner.prototype.combine = (ingredients) => {
    var _this =this;
    console.log('Starting combination');
    if (ingredients.length > 10) {
        for (var i = 0; i < Math.ceil(ingredients.length / 2); i++) {
            this.waitingForChunks++;
            console.log(`Dispatched chunks count at: ${this.waitingForChunks}`)
            var worker = new Worker('FanOutInWebWorker.js');
            worker.addEventListener('message', message => {
                return _this.complete(message);
            });
            worker.postMessage({
                ingredients: ingredients.slice(i, i * 2)
            });
        }
    }
}
Combiner.prototype.complete = message => {
    this.waitingForChunks--;
    console.log(`Outstanding chunks count at: ${this.waitingForChunks}`);
    if (this.waitingForChunks === 0) {
        console.log('All chunks received');
    }
}
```

간단한 카운터로 워커 수를 추적한다.

코드 메인이 단일 스레드이기 때문에 경합 조건을 걱정할 필요가 없다.

```js
// 웹 워커
self.addEventListener('message', e => {
    var data = e.data;
    var ingredients = data.ingredients;
    combinedIngredient = new Potion.CombinedIngredient();
    for (var i = 0; i < ingredients.length; i++) {
        combinedIngredient.Add(ingredients[i]);
    }  
    console.log('calculating combination');
    // 단순히 복잡한 계산을  시뮬레이션 하기 위해 사용
    setTimeout(combinationComplete, 2000);
}, false);

function combinationComplete () {
    console.log('combination complete');
    self.postMessage({
        event: 'combinationComplete',
        result: combinedIngredient
    });
}
```

여러 노드에 맡겨진 하위 문제가 동일한 문제일 필요는 없다.

하지만 이 하위 문제들이 복잡해서 이로 인해 절감된 비용이 메시지를 전송하는 데

소요되는 오버헤드보다는 커야한다.


## 데드 레터 큐

메시징 시나리오에서 실패를 다루는 것은 쉽다. 실패 전략의 핵심은 오류를 받아들이는 것이다.

**비동기 시스템에서** 오류 발생 즉시 이를 처리할 필요는 없다.

대신 오류를 일으키는 메시지를 따로 두었다가 나중에 직접 처리하면 된다.

메시지는 데드 레터/오류 큐에 저장된다.

거기에서 메시지는 오류나 핸들러가 수정된 후 쉽게 다시 처리될 수 있다.

이상적으로 메시지 핸들러는 오류를 발생시킨 속성을 나타내는 메시지를 다루도록 변경한다.

이것은 향후 발생할 오류를 방지하고, 다른 메시지가 시스템에서 같은 문제를 

일으키지 말라는 보장이 없기 때문에 메시지를 생성하는 모든 것을 수정하는 것이 좋다.

###### 더 많은 오류가 발견되고 수정될수록, 메시지 핸들러 품질은 향상된다

- 메시지의 오류 큐는 `BuyBook` 메시지 같은 중요 메시지를 하나라도 놓치지 않도록 보장한다.
- 이는 올바른 시스템 확보하는 과정이 마라톤 경주라는 것을 시사한다.
- 제대로 테스트되기 전에 서둘러 제품을 수정할 필요가 없다.
- 올바른 시스템을 ㅗ진행은 꾸준하고 신뢰할 만해야 한다.

###### 데드 레터 큐는 간헐적 오류 수정 역시 향상 시킨다.

이러한 오류는 외부 리소스 사용이 불가하거나, 잘못된 외부 리소스 사용시 발생하는 오류다.

외부 웹 서비스를 호출하는 핸들러를 가정해보자. 전통적 시스템에서 웹 서비스의 실패는 

곧 메시지 핸들러의 실패를 의미한다.

그러나 메시지 기반 시스템에서 명령은 마지막으로 이동 후, 큐의 맨 처음으로 도달할 때 까지

다시 반복 시도될 수 있다. 봉투에 메시지의 큐(처리된) 카운트를 기록한다. 

이 규 카운트가 정해진 한도에 도달해야만 메시지가 오류 큐로 이동한다.

- 이 접근법은 시스템 전반의 품질을 향상시키고
- 큰 실패로 발전하지 않도록 막아준다.
- 오류 큐는 작은 오류가 시스템 전체에 영향을 미치는 큰 오류로 발전하는 것을 막아준다.

### 메시지 재생

개발자가 오류를 일으키는 메시지들로 작업할 때, 매시지를 재처리할 수 있는 기능은 꽤 유용한다.

데드 레터 큐의 스냅샷으로 디버그 모드에서 메시지를 제대로 처리할 때까지 반복적 재처리를 한다.

또한 메시지 스냅샷은 핸들러에 대한 테스트의 일부도 만들 수 있다.

---

오류가 발생하지 않아도 메시지는 매일마다 서비스로 전송돼 시스템이 정상적임을 보여준다.

이 메시지들은 시스템에 입력될 때 감사 큐에 미러링 될 수 있다.

감사 큐의 데이터를 테스트에 이용할 수 있다.

새로운 기능이 추가되면, 정상적 워크로드가 재생돼 올바른 동작이나 성능에 문제가 없음을 보장한다.

---

감사 큐가 모든 메시지의 목록을 포함하고 있다면 애플리케이션이 어떻게 현재 상태에 도달하게 됐는지

쉽게 이해할 수 있다. 종종 사용자 작성 코드를 추가하거나 트리거와 감사 테이블을 사용해

히스토리 기능을 구현한다. 하지만 이런 방법 어느 것도 어떤 데이터가 변경되었는지 뿐 아니라

왜 변경됐는지 이해하는 데 메시징만큼 좋은 방법은 아니다.

주소 변경 시나리오를 생각해보자. 메시징 없이는 사용자의 주소가 왜 이전과 달라졌는지 알지 못한다.

---

시스템 데이터의 변화 기록을 잘 유지하려면 스토리지를 많이 쓰지만,

감사자가 각각의 변경이 왜 그리거 어떻게 만들어졌는지 쉽게 확인토록 해준다.

또한 잘 구성된 메시지는 변경을 일으킨 사용자의 의도를 잘 기록해 준다.

---

이런 메시징 시스템을 하나의 프로세스에 구현하는 것이 가능은 하지만 매우 어렵다.

메시지를 다루는 전체 프로세스가 다운될 수 있기 때문에, 메시지가 오류 이벤트에 

적절히 저장되도록 보장하기 힘들다.

현실적으로 메시지의 재생이 가치가 있어 보인다면 외부 메시지 버스가 좋은 솔루션이 될 수 있다.

### 파이프와 필터

메시지는 변경되지 않아야 된다고 했다. 이는 몇몇 속성 변경/새로운 유형의 메시지로

재방송될 수 없다는 의미는 아니다.

많은 메시지 핸들러들이 이벤트를 소비하여 작업을 수행한 다음 새로운 이벤트를 발행한다.

예를 들어 새로운 사용자를 시스템에 추가하는 워크플로우를 생각해보자.

---
1. `사용자` -> 생성 폼을 채움 -> `웹 서버` 
2. `웹 서버` -> 사용자 생성 명령 전송 -> `명령 핸들러` 
3. `명령 핸들러` -> UserCreated 이벤트 발행 -> `전파` 
    1. 사용자 정보를 가입 핸들러에 전송 
    2. 가입
2. `가입 후` -> UserAddedToAffilate 이벤트 발행 -> `메시지 핸들러` 
> affiliate : 제휴사

---

- `CreateUser` 명령은 `UserCreated` 이벤트를 트리거한다.
- 이 이벤트는 여러 서비스들에 의해 소비된다.
- 이 서비스 중 하나는 사용자 정보를 선택된 여러 기관들에 전달한다.
- 이 서비스가 실행되면, 일련의 자체 이벤트를 발행하고, 각 기관들은 새로운 사용자 정보를 수신한다.
- 이러한 이벤트들은 자신의 이벤트를 트리거할 수 있는 다른 서비스들에 의해 차례로 소비된다.

이러한 방식으로 변경은 전체 애플리케이션으로 파급된다.

**하지만 어떤 서비스도 무엇이 처음 이벤트를 시작했고 어떤 이벤트가 발행됐는지 모른다**

이 시스템은 거의 결합돼 있지 않다. 새로운 기능 추가/제거가 매우 간단하다.

메시징과 자율 컴포넌트 구성 시스템은 종종 _SOA, Service Oriented Architecture_로 불린다.

마이크로 서비스와 차이에 관한 논쟁이 많이 있지만 더이상 언급은 않는다. 각자 알아서 생각하길.

메시지의 변경 또는 재방송은 파이프나 필터로 생각할 수 있다.

서비스는 파이프를 통해 메시지를 프록시 하거나, 필터처럼 선택적으로 메시지를 재발행할 수 있다.

### 메시지 버전 관리

시스템 발전에 따라, 메시지에 포함된 정보 또한 변경될 수 있다. 사용자 생성 예제에서

원래는 이름과 메일 주소를 요청하도록 돼있었다. 하지만 마케팅 부서에서 존슨 부인과 존슨 씨를

구분해서 메일을 보내고 싶어한다. 따라서 사용자의 타이틀 정보도 수집하고 싶다.

_이것이 메시지 버전이 필요한 이유다._

이제 이전 메시지를 확장하여 새로운 메시지를 생성할 수 있다.

###### 옵션 1: 메시지에 추가 필드를 포함하거나, 버전 번호/날짜를 사용해 구분

`CreateUser`같은 메시지는 `CreateUserV1`/`Createuser20180513`이 될 수 있다.

앞서 다형성 메시지를 언급했다. 이것이 메시지의 버전을 관리하는 하나의 방법이다.

새 메시지는 이전 메시지의 확장이어서 이전 메시지 핸들러는 잘 동작한다(JS에 다형성이 없음도 언급했다)

###### 옵션 2: 메시지 핸들러를 업그레이드하는 방법

이 핸들러는 메시지의 버전을 확인하여 새로운 버전의 메시지를 이전 버전으로 변환한다.

물론, 새로운 메시지는 적어도 이전 버전만큼의 데이터나 메시지를 새로운 형식으로 변환하는 데

필요한 데이터를 가지고 있어야한다. v1 메시지를 가지고 있다면 다음과 같다.

```java
class CreateUserv1Message implements IMessage {
    __messageName: string;
    UserName: string;
    FirstName: string;
    LastName: string;
    EMail: string;
}
```

v2 메시지는 사용자 타이틀을 추가하여 확장한다.

```java
class CreateUserv2Message extends CreateUserv1Message implements IMessage {
    UserTitle: string;
}
```

그리고 간단한 업그레이더나 다운그레이더를 만들 수 있다.

```js
function CreateUserv2tov1Dowgrader (bus) {
    this.bus = bus;
}
CreateUserv2tov1Dowgrader.prototype.processMessage = message => {
    message.__messageName = 'CreeateUserv1Message';
    delete message.UserTitle;
    this.bus.publish(message);
}
```

이로써 쉽게 메시지 수정/재전송이 가능해 졌다.

## 힌트와 팁

메시지로 서로 다른 두 시스템 사이에 잘 정의된 인터페이스를 만들 수 있다.

메시지 정의는 둘 사이의 구성원에 의해 수행돼야 한다. 공통의 언어를 사용해 만들면,

특히 서로 다른 비지니스 영역에서 용어가 다른 의미로 사용될 때 곤란한 상황을 피할 수 있다.

판매부서와 배송부서에서 생각하는 고객은 완전히 다를 것이다.

**도메인 주도 디자인은 용어의 혼란을 피하고 둘의 경계를 수립할 힌트를 제공한다**

훌륭한 큐 기술이 있고, HTTP 에서의 JSON 읽기/쓰기를 지원한다. 잘 찾아보라.

## 요약

메시지를 자세히 알고싶다면, 고성능 컴퓨팅 솔루션, DDD, 

CQRS(command query responsibility segregation, 정보에 대한 업데이트와 읽기에 다른 모델을 사용하는 방식)

모든 애플리케이션이 메시징으로 혜택을 얻을 수 있는 건 아니다.

메시징 처럼 느슨한 결합 애플리케이션을 구축하면 추가 오버헤드가 발생한다.

**데이터 손실에 민감하고, 강력한 히스토리 기능이 필요한 협업 응용 프로그램이 제격이다**                         