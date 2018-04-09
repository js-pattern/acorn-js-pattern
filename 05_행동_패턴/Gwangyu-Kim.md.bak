#5장 행동 패턴
--------------------------------
* 4장 구조패턴 : 객체 간 상호작용을 용이하게 구성해주는 패턴
* 5장 행동패턴 : 객체 간 데이터를 공유하는 방법 또는 객체 사이의 데이터를 교환하는 방법

* 5장의 주요 패턴
> 책임 연쇄 (Chain of responsibility)
> 명령 (Command)
> 해석자 (Interpreter)
> 반복자 (Iterator)
> 중재자 (Mediator)
> 메멘토 (Memento)
------------------------------------ 04/02 스터디
> 감시자 (Observer)
> 상태 (State)
> 전략 (Strategy)
> 템플릿 메소드 (Template method)
> 방문자 (Visitor)
------------------------------------ 04/09 스터디
--------------------------------------------------------------------

## 책임 연쇄 (Chain of responsibility)
#### 메세지가 클래스에서 다른 클래스로 전달되는 접근 방식을 기술.
 1. 클래스는 메세지를 처리하거나 체인에 있는 다음 멤버에게 전달함.
 2. 구현하기에 따라 메세지 전송에 몇 가지 다른 규칙을 적용할 수 있음.
 - ex) 체인의 첫번째 일치하는 링크만 메세지를 처리 또는 모든 일치하는 링크가 메세지를 처리할 수 있도록 처리
       링크 처리 중지, 변종 메세지 생성

### 구현
* 클래스의 단계별 일 처리를 생각하여서.. ( 우선 낮은 단계의 클래스가 일을 처리할 수 있도록, 해결하지 못하면 높은단계의 클래스로 패스 & 처리 과정의 연속)

````javascript
//모든 클래스(청취자)의 공통 인터페이스
//두가지 기능 (불만 듣기, 불만 해결 확인)
export interface ComplaintListener {
    IsAbleToResolveComplaint(complaint: Complaint): boolean;
    ListenToComplaint(complaint: Complaint): string;
}

//불만
//청취자 클래스는 이 객체를 받아, 듣고 해결할 수 있는지를 수행함
var Complaint = (function() {
    function Complaint() {
        this.ComplainingPart = "";
        this.ComplaintAbout = "";
        this.Complaint = "";
    }
    return Complatin;
})();

//ComplaintListener를 구체적으로 구현한 클래스 ( ClerkOftheCourt, King)
var ClerkOfTheCourt = (function() {
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
})();
JudicialSystem.ClerkOfTheCourt = ClerkOfTheCourt;
var King = (function() {
    function King() {}
    King.prototype.IsAbleToResolveComplaint = function(complaint){
        return true;
    }
    King.prototype.ListenToComplaint = function(complaint){
        // 몇 가지 작업을 수행
        // 불만에 대한 해결책을 반환
        return '';
    }
})();
JudicialSystem.King = King;

//Resolver에서 해결할 수 있는 ComplaintListner를 찾는 작업을 하고 해당 클래스를 반환한다.
//해결할 수 있는 클래스가 나올 떄 까지 찾는 작업을 반복함
// ( 해결유무 확인 -> 해결할 수 있음 -> 해결)
//  (             -> 해결할 수 없음 ->     다음 객체 확인 -> 해결유무 확인 )
var ComplaintResolver = (function(){
    function ComplaintResolver() {
        this.complaintListeners = new Array();
        this.complaintListeners.push(new ClerkOfTheCourt());
        this.complaintListeners.push(new King());
    }
    ComplaintResolver.prototype.ResolveComplaint = function(complaint){
        for(var i =0; i < this.complaintListeners.length; i++){
            if(this.complaintListeners[i]).
            IsAbleToResolveComplaint(complaint){
                return this.complaintListeners[i].ListenToComplaint(complaint);
            }
        }
    };
    return ComplaintResolver;
})();

````
* 자바스크립트에서 Event가 책임 연쇄를 통해 전달됨.
개인적 생각.. 책임연쇄 행동패턴은 해당 클래스의 해결이 안되면 상위 클래스에서 해결함,
디폴트 클래스를 적은 기능을 해주고 상위 클래스에 큰 기능을 줌, 어떻게 보면 메모리를 적게도 아니면 많이도 사용할 수 있을거같고
어떤 클래스에서 문제 해결을 했느냐에 따라 요청의 수준을 알 수 있을것 같음 ( 내 생각엔 Exception 클래스도..?)

--------------------------------------------------
## 명령 패턴 (Command)
#### 메소드의 매개변수와 객체의 현재 상태 모두를 캡슐화하고 메소드를 호출하는 방법..?
 * 명령 패턴은 메소드를 나중에 호출 할 때 필요한 것들을 작은 패키지로 포장함.
 * 명령 실행 > ...( 명령 실행 코드 결정 대기) >

 *  구성 컴포넌트
    1) 클라이언트 ( 명령 생성, 전달(==호출자) )
    2) 명령
    3) 리시버  

-----------------------------------------------
> 명령 패턴 구성 컴포넌트들

### 코드 구현
1. 명령 메세지
 * 명령 설정 옵션
    1) 함수와 매개변수를 추적
        - 함수의 매개변수를 배열로 저장
````javascript
//명령 생성, 해당 명령을 구성하는 것들을 객체에 입력
var simpleCommand = new Array();
simpleCommand.push(new LordInstruction().BringTroops);
simpleCommand.push("King's Landing");
simpleCommand.push(500);
simpleCommand.push(new Date());

//1. 기본적인 명령 호출 방법
//1) 매개변수3개와  알맞는 명령을 주었을 때만 호출되도록
simpleCommand[0](simpleCommand[1], simpleCommand[2], simpleCommand[3]);
//2) 확장
simpleCommand[0](simpleCommand[1], simpleCommand[2], simpleCommand[3], simpleCommand[4], simpleCommand[5], simpleCommand[6]);
````
가장 기본적인 Command 패턴의 command 생성법
    * 명령 생성엔 실패하지 않음, 명령이 실행시 실패 판단됨(> 파라미터로 전달된 명령의 인자가 충족이 안된다고 생각해보기)
    * 이렇게 사용하는것 보다 클래스르 따로 생성해 올바른 인수 생성을 보장, 구분할 수 있도록 하는게 더 세련됨

```javascript
//2. 명령을 적절한 클래스로 구현
var BringTroopsCommand = (function (){
    function BringTroopsCommand(location, numberOftroops, when) {
        this._location = location;
        this._numberOfTroops = numberOfTroops;
        this._when = when;
    }
    BringTroopsCommand.prototype.Execute = function (){ //호출자!!(명령호출 Invoker, 명령 내부에 호출자가 선언된 케이스)
        var receiver = new LordInstructions();
        receiver.BringTroops(this._location, this._numberOfTroops,this._when);
    };
    return BringTroopsCommand;
})()
````
명령을 구체적으로 생성,
    * 매개변수가 정확한지 확인할 수 있음 > 명령이 생성시 실패할 수 있음 실행시 실패확률이 낮음
    * 명령은 메모리에 저장하여 호출하여 사용함.

2. 호출자 (Invoker)
 * 명령의 실행을 지시함 ( 시간이벤트, 사용자 인터렉션, 프로세스 다음단계 등 아무거나 상관 없음. Execute 코드!)
 * 즉시호출 또는 나중에 호출을 선택할 수 있음. ( 인기있는 방법은 이벤트 루프의 끝에 호출자를 선언하는것)
    ex)
````javascript
    process.nextTick(function(){command.Execute();});
````
 3. 수신자 (Receiver)
  * 명령을 실행함
  * 명령을 어떻게 수행할 지 알 수 있음(지연 또는 기타)
````javascript
var LordInstructions = (function(){
    function LordInstructions(){
    }
    LordInstructions.prototype.BringTroops = function(location, numberOfTroops, when){
        console.log("You have been instructed to bring" + numberOfTroops
                    + "troops to" + location
                    + "by" + when)
    }
    return LordInstructions;
})();
````

* 결론
 1) 클라이언트 : 명령 생성 및 전달 / 명령 / 수신자 : 명령 실행 및 전달
-------------------------------------------------------------------

## 해석자 (Interpreter) 패턴
 * 자신만의 고유한 언어를 생성할 수 있게 해주는 패턴 > 너 쓰고싶은 대로 만들어 써
     ex) SQL, 정규 표현식
 * 도메인 특화 언어(Domain Specific Languages) 시작 -> 하나의 요구사항에 특화던 언어를 만드는 것이 유용한 경우.    

### 예제
아래의 문법을 사용하여 전투몰록을 조회할 수 있는 코드를 작성하기.
 > (aggressor -> battle ground <- defender) -> victor  (공격자 -> 전쟁터 <- 방어자 ) -> 승자

보통 정규표현식을 준수함. (-> 렉서와 파서등 까지 도메인 특화 언어로 구성하는것은 좋은 방법이 아닌것 같음. >)
* cf) 렉서(lexor) : 코드 문자열을 작고 의미있는 조각(토큰)을 구분하는것 (ex. I like hambergur를 공백 단위로 구분함 > 공백이 렉서)
      파서(parser) : 렉서를 이용하여 나뉘어진 조각(문자열 또는 토큰)을 스트림으로 파싱해 최종 결과를 만드는 것.
-------------------------------------------------------------

1. 전투를 위한 자바스크립트 모델 생성
```javascript
var Battle = (function (){
    function Battle(battleGround, agressor, defender, victor) {
        this.battleGround = battleGround;
        this.agressor = agressor;
        this.defender = defender;
        this.victor = victor;
    }
    return Battle;
})()
```

2. 파서 생성
    전투 문자열을 받아서 nextBattle을 사용하여 파싱해 Battle() 구현.
```javascript
var Parser = (function() {
    function Parser(battleText) {
        this.battleText = battleText;
        this.currentIndex = 0;
        this.battleList = battleText.split("\n");
    }
    Parser.prototype.nextBattle = function (){
        if(!this.battleList[0]) return null;
        var segments = this.battleList[0].match(/\((.+?)\s?->\s?(.+?)\s?<-\s?(.+?)\s?->\s?(.+)/);
        return new Battle(segment[2], segments[1], segment[3], segment[4]);
    };
    return Parser;
})();

var text = "(Robert Baratheon -> River Trident <- RhaegarTargaryen) -> Robert Barateheon"
var p = new Parser(text);
p.nextBattle()
```
결과코드
> { battleGround: 'River Trident',
    agressor: 'Robert Baratheon',
    defender: 'RhaegarTargaryen',
    victor: 'Rober Baratheon'}

 많이 사용되지 않음. (사용 예 Less.)

* 문법 구조를 클래스화 한 코드 구조. 일련의 규칙으로 언어 해석을 함.
  규칙이 많아지면 복잡하고 무거워 지므로 그럴땐 파서&컴파일러를 사용하여라.
  SQL구문, Shell 커맨드 해석기, 통신 프로토콜.?

  ------------------------------

## 반복자
#### 집합에서 다음 항목을 순차적으로 선택하는 간단한 메소드를 제공해 주는 패턴

기본적 반복자 패턴의 인터페이스
```javascript
interface Iterator { next (); }
```  

### 코드 구현
```javascript
var KingSuccession = (function() {
    function KingSuccession(inLineForThrone) {
        this.inLineForThrone = inLineForThrone ;
        this.pointer = 0;
    }
    KingSuccession.prototype.next = function (){
        return this.inLineForThrone[this.pointer++];
    };
    return KingSuccession;
})();

var king = new KingSuccession(["Robert Baratheon", "Joffery Baratheon", "Tommen Baratheon"]);
king.next();
king.next();
king.next();

```

ex. 피보나치 수열 등 고정되어 있지 않은 집합을 반복하는 것도 있음.
```javascript
var FibonacciIterator = (function () {
    function FibonacciIterator() {
        this.previous = 1;
        this.beforePrevious = 1;
    }
    FibonacciIterator.prototype.next = function (){
        var current = this.previous + this.beforePrevious;
        this.previous = current;
        return current;
    };
    return FibonacciIterator;
})()
```
* 반복자 패턴은 생성된 모든 목록을 탐색할 수 있는 편리한 구조.

### ECMAScript6의 반복자
유용한 패턴이어서 ECMAScript6에서 표준으로 선택되었음.
* done과 value 매개변수를 포함하는 객체를 반환하는 메소드.
    - 반복자가 집합의 마지막에 도달했을 때 done 매개변수는 true가 됨.

```javascript
var kings = new KingSuccession(["Robert Baratheon", "Joffery Baratheon", "Tommen Baratheon"]);
for( var of kings){
    //next()
}
```

--------------------------
## 중재자(Mediator)
각 컨트롤은 페이지 내의 다른 컨트롤들을 수행하기 전에 해당 컨트롤이 유효한 지를 먼저 확인하고 싶음, 하지만 각 컨트롤에 대해 모두 알고 있어야 한다는 것은 악몽
= 객체 내의 다른 객체를 호출&실행 하기전에 해당 호출 객체가 유효한지 확인해야함, 하지만 각각 객체를 내부에 할당 또는 선언하는건 너무 많음.

* 컴포넌트의 중간에 위치해 메세지의 경로변경이 이뤄지는 유일한 장소로써 동작함.
    코드를 유지하는데 필요한 복잡한 작업을 단순화 시킴.
    개인적 생각으론 MVC 패턴의 컨트롤러 가 대표..

### 구현
```javascript
//메세지를 받고, 메세지를 전달하는 기능
var Karstark = (function(){
    function Karstark(greatLord){
        this.greatLord = greatLord;
    }
    Karstark.prototype.receiveMessage = function (message){
    };
    Karstark.prototype.sendMessage = function(message){
        this.greatLord.routeMessage(message);
    };
    return Karstark;
})();

var HouseStark = (function(){
    function HouseStark(){
        this.Karstark = new Karstark(this);
        this.bolton = new Bolton(this);
        this.frey = new Frey(this);
        this.umber = new Umber(this);
    };
    HouseStark.prototype.routeMessage = function(message){
    };
    return HouseStark;
})
```
 * 통신이 복잡하고 잘 정의돼 있을 때 효과적.

---------------------------
## 메멘토
#### 객체의 상태 정보를 저장 및 복원(rollback)하는 패턴
이전의 상태로 객체의 상태를 복원할 수 있는 방법을 제공. 복구가 힘든 명령을 쉽게 복원할 수 있음.

메멘토 패턴의 구성원
    * 발신자(originator) : 상태정보를 유지, 새로운 메멘토를 생성하는 인터페이스 제공
    * 관리자(caretaker) : 패턴의 클라이언트. 새로운 메멘토를 요청 및 복원 관리
    * 메멘토(memento) : 발신자의 저장된 상태의 표현. 롤백을 위해 스토리지에 저장하는 패턴.
    
