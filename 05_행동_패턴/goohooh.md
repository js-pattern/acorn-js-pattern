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

