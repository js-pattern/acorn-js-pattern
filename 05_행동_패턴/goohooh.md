# 행동 패턴

객체가 데이터를 공유 또는 교환하는 방법

- 책임연쇄 _Chain of responsibility_
- 명령 _Command_
- 해석자 _Interpreter_
- 반복자 _Iterator_
- 중재자 _Mediator_
- 메멘토 _Memento_
- 감시자 _Observer_
- 상태 _State_
- 전략 _Strategy_
- 템플릿 메서드 _Template method_
- 방문자 _Visitor_

## 5.1 책임 연쇄

객체를 메시지로 보내는 함수 호출을 떠올려 보자

- 메시지가 클래스에서 다른 클래스로 전달되는 방식을 기술.
- 클래스는 메시지를 처리하거나 체인인에 있는 다음 멤버에게 전달.
- 구현에 따라 메시지 전송에 몇가지 규칙 적용 가능.
    - 체인의 첫 번째 일치하는 링크만 메시지를 처리하도록 구현
    - 모든 일치하는 링크가 메시지를 처리하도록 구현
- 때때로 링크는 처리를 중지하거나, 메시지의 변종을 만들 수 있음

## 5.2 구현

법률 시스템을 구현한다면

불만 접수 => 낮은 단계 관료가 해결 => 해결하지 못하면 윗단계로 전달 => ... => 왕까지 전달

> 왕을 모든 사람이 분쟁해결에 실패했을 때 부탁할 수 있는 기본 분쟁 해결사로 생각할 수 있음


```typescript
// 먼저 불만을 듣는 청취자를 기술하는 인터페이스
export interface ComplaintListener {
    IsAbleToResolveComplaint(complaint: Complaint): boolean;
    ListenToComplaint(complaint: Complaint): string;
}
```

불만을 구성하는 요소
```js
function Complaint(){
    this.ComplainingParty = '';
    this.ComplaintAbout = '';
    this.Complaint ='';
}
```

`ComplaintListener` 클래스 구현 및 불만을 해결하는 몇 개의 클래스
```js
function ClerkOfTheCourt() {}

ClerkOfTheCourt.prototype.IsAbleToResolveComplaint = function (complaint) {
    // 법원 서기에 의해 해결될 수 있는 불만인지 결정
    return false;
}

ClerkOfTheCourt.prototype.ListenToComplaint = function (complaint) {
    // 몇가지 작업을 수행
    // 불만에 대한 해결책을 반환
    return '';
}

function King() {}

King.prototype.IsAbleToResolveComplaint = function(complaint){
    return true;
}
King.prototype.ListenToComplaint = function(complaint){
    // 몇 가지 작업을 수행
    // 불만에 대한 해결책을 반환
    return '';
}
```

각 클래스는 불만을 해결하는 서로 다른 접근 방식을 구현한다. 

`King` 클래스가 디폴트 위치에 있는지 확인하고 각각을 체인으로 연결해야한다.

```js
function ComplaintResolver(){
    this.complaintListeners = [];
    this.complaintListeners.push(new ClerkOfTheCourt());
    this.complaintListeners.push(new King());
}

ComplaintResolver.prototype.ResolveComplaint = (complaint) => {
    for(let i = 0; i < this.ComplaintListeners.length; i++){
        if (this.complaintListeners[i].IsAbleToResolveComplaint(complaint)){
            return this.complaintListeners[i].ListenToComplaint(complaint);
        }
    }
}
```

복수의 청취자가 매개변수를 조적하여 다음 청ㅊ취자에게 전송하도록 변형하는 것도 가능

---

브라우저 기반의 JS에서, 이벤트는 책임 연쇄를 통해 전달된다.

링크에 복수의 클릭 이벤트 리스너를 연결해 놓으면 이들 각각은 마지막 기본 탐색 리스너가

발생할 때까지 계속 된다. 알게 모르게 대부분의 코드에서 책임 연쇄를 사용하고 있다.

## 5.2 명령

- 메서드의 매개변수와 객체의 현재 상태를 모두 캡슐화하여 메서드 호출
- 이때 호출에 필요한 것들은 작은 패키지로 포장
- 이로써 먼저 명령을 실행하고 어떤 코드가 명령을 실행할지 이후 결정까지 기다릴 수 있음
- 패키지는 큐에 들어가거나 추후 실행을 위해 직렬화될 수 있음
- 실행 취소, 명령 로깅 같은 기능 추가를 위한 명령 실행의 단일 지점 또한 가질 수 있음

쉽게 그려지지 않음

### 명령 메시지

기본 컴포넌트.

- 예상 문제
    - 각 명령에서 전역 상태 추적이 어려움
    - 명령 생성 후 전역상태가 변경 된다면?(전역 상태의 사용을 피해야 함)
- 명령 설정 옵션
    - 함수와 매개변수 추적

```js
// 군 파병 명령(군사 숫자, 위치, 날짜)
const simpleCommand = [];
simpleCommand.push(new LordInstructions().BringTroops);
simpleCommand.push("King's Landing");
simpleCommand.push(500);
simpleCommand.push(new Date());

// 바람직하지 않다
simpleCommand[0](simpleCommand[1], simpleCommand[2], simpleCommand[3])
```

명령에 올바른 인수가 제공 되도록 보장하고,

명령들의 집합에서 쉽게 명령을 구분할 수 있도록 도와야한다.

```js
function BringTroopsCommand(location, numberOfTroops, when){
    this._location = location;
    this._numberOfTroops = numberOfTroops;
    this._when = when;
} 

BringTroopsCommand.prototype.Execute = () => {
    const receiver = new LordInstructions();
    receiver.BringTroops(
        this._location,
        this._numberOfTroops,
        this._when
    )
}
```

> 명령 실행 시 실패대신 명령 생성시에 실패하도록 보장한다.
> 
> 실행은 지연될 수 있고, 생성시 디버깅이 더 쉽다.

이 명령 들은 디스크나 메모리에 저장하여 나중에 사용할 수 있다.

### 호출자 _Invoker_

- 명령의 실행 지시
- 시간 이벤트(_timed event_)나 사용자 상호작용, 프로세스의 다음 단계 등 어떤 것도 될 수 있다.
- 앞선 `simpleCommand` 실행이 호출자 안에서 실행됐다.
- 더 엄격한 명령에서의 호출자는 `command.Execute()`와 같다.

명령의 실행을 이벤트 루프 끝으로 연기하는 것이 일반적이다.

```js
// process.nextTick()은 명령 실행을 루프 마지막으로
// 연기하여 프로세스가 수행할 일이 없을 때 실행된다.
process.nextTick(() => command.Execute());
```

### 수신자 _Receiver_

- 명령 패턴의 마지막 컴포넌트
- 명령 실행의 대상

```js
function LordInstructions() {}
LordInstructions.prototype.BringTroops = (location, numberOfTroops, when) => {
    console.log(
        `You have been instructed to bring ${numberOfTroops} to ${location} by ${when}`
    );
}
```

수신자는 지연된 명령을 어떻게 수행해야 하는지 알고 있다.

### 명령 패턴 정리

1. 클라이언트는 명령을 생성. 호출자로 전달
1. 호출자는 명령을 지연 혹은 즉시 실행
1. 명령은 수신자에서 실행

실행 취소 스택을 구축할 경우 `Execute`와 `Undo` 메서드를 가짐.

- 실행 취소 : `undo` 스택에서 명령을 꺼내 `Undo` 함수를 실행, `redo` 스택에 쌓는다.
- 재실행 : `redo` 스택에서 명령을 꺼내 `Execute`함수를 실행하고 `undo` 스택에 넣는다.
- 이때 명령을 통해 상태 변이가 일어나는지 반드시 확인해야한다.

**일부 코드를 지연 시킨다는 점에서 유용한 패턴이다.**

## 5.3 해석자

원형이 없다. 원하는 대로 디자인 가능

### 예제

`(aggressor -> battle ground <- defender) -> victor`

### 구현

```js
function Battle(battleGround, agressor, defender, victor){
    this.battleGround = battleGround;
    this.agressor = agressor;
    this.defender = defender;
    this.victor = victor;
}

function Parser(battleText){
    this.battleText = battleText;
    this.currentIndex = 0;
    this.battleList = battleText.split('\n');
}
Parser.prototype.nextBattle = () => {
    if (!this.battleList[0]) return null;

    const rule = /\((.+?)\s?->\s?(.+?)\s?<-\s?(.+?)\s?->\s?(.+)/;
    const segments = this.battleList[0].match(rule)
    return new Battle(segments[2], segments[1], segments[3], segments[4]);
}

const text = '(Robert -> River <- Targaryen) -> Robert';
const p = new Parser(text);
p.nextBattle()
```

결과

```json
{
    battleGround : 'River',
    aggressor : 'Robert',
    defender : 'Targaryen',
    victor : 'Robert'
}
```

JS에서 해석자의 좋은 예는 `LESS` 언어로 JS에 의해 CSS로 컴파일 된다.

## 5.4 반복자

일반적 인터페이스는

```typescript
interface Iterator {
    next();
}
```

### 구현

```js
function KingSuccession(inLineForThrone){
    this.inLineForThrone = inLineForThrone;
    this.pointer = 0;
}

KingSuccession.prototype.next = () => {
    return this.inLineForThrone[this.pointer++];
}

// 배열로 사용한다
const king = new KingSuccession(['Robert', 'Joffery', 'Tommen']);
king.next(); // 'Robert'
king.next(); // 'Joffery'
king.next(); // 'Tommen'
```

고정돼 있지 않은 집합을 반복할 수 있다.

```js
function FibonacciIterator() {
    this.previous = 1;
    this.beforePrevious = 1;
}

FibonacciIterator.prototype.next = () => {
    const current = this.previous + this.beforePrevious;
    this.beforePrevious = this.previous;
    this.previous = current;
    return current;
}

const fib = new FibonacciIterator();
fib.next() // 2
fib.next() // 3
fib.next() // 5
fib.next() // 8
fib.next() // 13
fib.next() // 21
```

### ES6 반복자

[GDG 2016](http://www.bsidesoft.com/?p=2913)


## 5.5 중재자

클래스의 다 대 다 관계 관리는 어렵다. 폼을 떠올려보자.

각 컨트롤은 해당 컨트롤의 유효성을 검사하고 다른 컨트롤들을 수행한다.

하지만 각 컨트롤이 다른 컨트롤에 대해 모두 알아야 한다면 매우 복잡해질것이다.

새로운 컨트롤이 추가될 때마다 다른 컨트롤들도 수정돼야 한다.

---

**중재자**는 다양한 컴포넌트 중간에서 메시지의 경로 변경이 이루어지는 유일한 곳이다.

유지보수를 쉽게 해준다. 

### 구현

대 군주는 소 영주들 사이에서 분쟁을 해결하거나 정보를 교환해주는 중재자 역할을 한다.

```js
// 소영주들의 인터페이스
// 제 3자로부터 메시지를 받거나
// 인스턴스로 생성된 대 군주에게 메시지를 전송
function Karstark(greatLord){
    this.greatLord = greatLord;
}
Karstark.prototype.receiveMessage = message => {};
Karstark.prototype.sendMessage = message => {
    this.greatLord.routeMessage(message);
}

function HouseStark(){
    this.karstark = new Karstark(this);
    this.bolton = new Bolton(this);
    this.frey = new Frey(this);
    this.umber = new Umber(this);
}
HouseStark.prototype.routeMessage = message => {};
```

HouseStark 클래스를 통해 모든 메시지 전달함으로써,

다른 가문들은 메시지가 어떻게 전달되는지에 대해 걱정할 필요가 없다.

중재자는 **통신이 복잡하고 잘 정의돼 있을 때 효과적**이다.

통신이 복잡하지 않다면 오히려 중재자 때문에 복잡성이 증가할 수 있다.

통신이 잘 정의돼 있지 않다면, 한 곳에서 통신 규칙을 체계화하는 것이 어려워 진다.

---

```js
$('.error').slideToggle();
```
> 저자는 jQuery가 사실상 중재자 역할을 한다고 주장

## 5.6 메멘토

앞선 명령 패턴에서 잡업 취소 기능을 알아보았다.

하지만 가역의 명령을 생성하는 것이 항상 가능한 것은 아니다.

```js
function SquareCommand(numberToSquare){
    this.numberTosquare = numberToSquare;
}

SquareCommand.prototype.Execute =  () => {
    this.numberToSquare *= this.numberToSquare;
}
```

-9를 입력하면 81이 된다. 하지만 9를 입력해도 결과는 같으므로 추가 정보 없이 명령을 반전시킬 수 없다.

**메멘토는 변수의 이전 값에 대한 기록을 유지하고 복원하는 기능을 제공**한다.

`Undo` 스택 외에도 인스턴스에서 객체의 상태를 되돌리는 기능이 필요한 경우가 많다.

`what-if` 분석은 상태 정보를 가상으로 변경하고 어떤 변화가 일어나는지 관찰해야 한다.

이 변경은 일반적으로 영구적이지 않아서 메멘토 패턴을 사용해 다시 되돌리거나 프로젝트에서 필요한 경우

변경된 상태로 남겨놓아야 한다.

---

전형적인 메멘토 구현의 컴포넌트는

- 발신자 _originator_ : 상태정보를 유지하고 새로운 메멘토를 생성하는 인터페이스 제공
- 관리자 _caretaker_ : 패턴의 클라이언트. 새로운 메멘토를 요청하고 복원돼야 하는 경우 이를 관리
- 메멘토 _memento_ : 발신자의 저장된 상태의 표현. 롤백을 위해 스토리지에 저장해야 하는 값

상사가 비서에게 메모를 요청한다 가정하면,

상사(관리자)는 메모장(메멘토)에 메모를 기록하는 비서(발신자)에게 몇 가지 메모를 지시. 

때로 상사는 비서에게 기록한 메모의 삭제도 요청.

---

관리자의 참여로 패턴의 변경이 있을 수도 있다. 일부 구현에서 발신자는

상태 정보에 변화가 생길 때마다 새로운 메멘토를 생성한다.

이는 상태의 새 복사본을 만들어 여기에 변경을 적용하기 때문에 일반적으로

`쓰기 시 복사(COW)`_Copy on Write_ 로 알려져 있다. 이전 버전은 메멘토에 저장된다.


### 구현

예언가/점쟁이들은 미래를 보기 위해 마법을 사용해 특정한 현재의 변화가 미래에

어떻게 반영되는지 관찰한다.

다양한 예언을 위해 종종 다른 시작 조건이 필요하다.

특정 시작점에 대한 세계의 상태 정보를 제공하는 `world state` 부터 시작

```js
function WorldState(numberOfKings, currentKingInKingsLanding, season){
    this.numberOfKings = numberOfKings;
    this.currentKingInKingsLanding = currentKingInKingsLanding;
    this.season = season;
}
```

이 `WorldState`는 세계를 구성하는 모든 조건을 추적하는데 사용된다.

이 변수는 애플리케이션에 의해 시작 조건에 변경이 발생할 때마다 변경된다.

이제 메멘토와 동일한 상태정보를 제공하고 메멘토의 생성 및 복구를 가능케 하는 클래스를 구현한다.

```js
function WorldStateProvider() {}

WorldStateProvider.prototype.saveMemento = () => {
    return new WorldState(
        this.numberOfKings,
        this.currentKingInKingsLanding,
        this.season
    );
}

WorldStateProvider.prototype.restoreMemento = memento => {
    this.numberOfKings = memento.numberOfKings;
    this.currentKingInKingsLanding = memento.currentKingInKingsLanding;
    this.season = memento.season;
}
```

다음은 클라이언트

```js
function Soothsayer(){
    this.startingPoints = [];
    this.currentState = new WorldStateProvider();
}

Soothsayer.prototype.setInitialConditions =
(numberOfKings, currentKingInKingsLanding, season) => {
    this.currentState.numberOfKings = numberOfKings;
    this.currentState.currentKingInKingsLanding = currentKingInKingsLanding;
    this.currentState.season = season;
}

Soothsayer.prototype.alterNumberOfKingsAndForeTell = numberOfKings => {
    this.startingPoints.push(this.currentState.saveMemento());
    this.currentState.numberOfKings = numberOfKings;
};

Soothsayer.prototype.alterSeasonAndForetell = season => {
    // 상동
};

Soothsayer.prototype.alterCurrentKingInKingsLandingAndForetell = season => {
    // 상동
};

Soothsayer.prototype.tryADifferentChange = () => {
    this.currenttate.restoreMemento(this.startingPoints.pop());
}
```

이 클래스는 세계의 상태를 변경하고 예언을 실행하는 메서드를 제공한다.

각 메서드는 히스토리 배열인 `startingPoints`에 이전 상태를 푸시한다.

또한 이전 상태 변경의 실행을 취소하고 다른 예언을 실행할 준비를 하는 `tryADifferentChange` 

메서드도 존재한다. 실행 취소는 배열에 저장돼 있는 메멘토를 다시 읽어 수행한다.