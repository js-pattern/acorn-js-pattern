# 생성 패턴
재사용 가능한 객체 지향 소프트웨어 <br>
- 추상 팩토리 Abstract Factory
- 빌더 Builder
- 팩토리 메서드 Factory method
- 단일체 Singleton
- 프로토타입 Prototype

## 추상 팩토리
객체의 구체적인 유형을 모른 채 객체의 Kit를 생성하는 방법<br>
```javascript

// 정규 구체 클래스(Regular concrete class) 구현
var KingJoffery = (function() {
  function KingJoffery() {

  }
  KingJoffery.prototype.makeDecision = function() {};
  KingJoffery.prototype.marry = function() {};
  return KingJoffery;
})();

// 유사한 hand of king(2인자) 클래스 구현
var LordTywin = (function() {
  function HandOfTheKing () {

  }
  LordTywin.prototype.makeDecision = function() {};
  LordTywin.prototype.marry = function() {};
  return LordTywin;
})();

// 구체 팩토리 메소드(Abstract Factory): 각 Product(정규 구체 클래스)의 새로운 인스턴스를 반환
var LannisterFactory = (function() {
  function LannisterFactory() {}
  LannisterFactory.prototype.getKing = function() {
    return new KingJoffery();
  };
  LannisterFactory.prototype.getHandOfKing = function() {
    return new LordTywin();
  }
  retur LannisterFactory;
})();

// 지배 가문을 필요로 하는 클래스를 생성 (Client)
var CourtSession = (function() {
  function CourtSession(abstarctFactory) {
    this.abstarctFactory = abstarctFactory;
    this.COMPLAINT_THRESHOLD = 10;
  }
  CourtSession.prototype.complaintPresented = function(complaint) {
    if (complaint.severity < this.COMPLAINT_THRESHOLD) {
      this.abstarctFactory.getHandOfKing().makeDecision();
    } else {
      this.abstarctFactory.getKing().makeDecision();
    }
  }
})();

var courtSession = new CourtSession(new LannisterFactory());
courtSession.complaintPresented({ severity: 8 });
courtSession.complaintPresented({ severity: 12 });

```
