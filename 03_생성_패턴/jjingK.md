# 생성 패턴
재사용 가능한 객체 지향 소프트웨어 <br>
- 추상 팩토리 Abstract Factory
- 빌더 Builder
- 팩토리 메서드 Factory method
- 단일체 Singleton
- 프로토타입 Prototype

## 추상 팩토리Abstract Factory
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

## 빌더Builder

```javascript
var Wasteros;
(function(Wasteros) {
  var Event = (function() {
    function Event(name) {
      this.name = name;
    }
    return Event;
  })();
  Wasteros.Event = Event;
  var Prize = (function() {
    function Prize(name) {
      this.name = name;
    };
    return Prize;
  })();
  Wasteros.Prize = Prize;
  var Attendee = (function() {
    function Attendee(name) {
      this.name = name;
    };
    return Attendee;
  })();
  Wasteros.Attendee = Attendee;
  var Tournament = (function() {
    function Tournament() {
      this.events = [];
      this.attendees = [];
      this.prizes = [];
    };
    return Tournament;
  })();
  Wasteros.Tournament = Tournament;
  // 빌더를 구현
  var LannisterTournamentBuilder = (function() {
    function LannisterTournamentBuilder() {

    };
    LannisterTournamentBuilder.prototype.build = function() {
      var tourunament = new Tournament();
      tourunament.events.push(new Event('Joust'));
      tourunament.events.push(new Event('Melee'));

      tourunament.attendees.push(new Attendee('Jamie'));

      tourunament.prizes.push(new Prize('Gold'));

      return tourunament;
    }
    return LannisterTournamentBuilder;
  })();
  Wasteros.LannisterTournamentBuilder = LannisterTournamentBuilder;

  // Director 구현
  var TournamentBuilder = (function() {
    function TournamentBuilder() {}
    TournamentBuilder.prototype.build = function(builder) {
      return builder.build();
    }
    return TournamentBuilder;
  })();
  Wasteros.TournamentBuilder = TournamentBuilder;
})(Wasteros || (Wasteros = {}));
var builder = new Wasteros.TournamentBuilder();
var lannister = builder.build(new Wasteros.LannisterTournamentBuilder());
console.log(lannister);
```

## 팩토리 메소드Factory Method
전략패턴 같이 다수의 유사한 구현이 필요한 경우 유용<br>
일반적으로 추상 팩토리 패턴과 함께 사용<br>
```javascript
var Wasteros;
(function(Wasteros) {
 (function(Religion) {
   var WateryGod = (function() {
     function WateryGod() {};
     WateryGod.prototype.prayTo = function() {};
     return WateryGod;
   })();
   Religion.WateryGod = WateryGod;

   var AncientGod = (function() {
     function AncientGod() {};
     AncientGod.prototype.prayTo = function() {};
     return AncientGod;
   })();
   Religion.AncientGod = AncientGod;

   var DefaultGod = (function() {
     function DefaultGod() {};
     DefaultGod.prototype.prayTo = function() {};
     return DefaultGod;
   })();
   Religion.DefaultGod = DefaultGod;

   var GodFactory = (function() {
     function GodFactory() {};
     GodFactory.Build = function(godName) {
       if (godName === 'watery')
         return new WateryGod();
       if (godName === 'ancient')
         return new AncientGod();
       return new DefaultGod();
     };
     return GodFactory;
   })();
   var GodDeterminant = (function() {
     function GodDeterminant(religionName, prayerPurpose) {
       this.religionName = religionName;
       this.prayerPurpose = prayerPurpose;
     };
     return GodDeterminant;
   })();
   var Prayer= (function() {
     function Prayer() {}
     Prayer.prototype.pray = function(godName) {
       GodFactory.Build(godName).prayTo();
     }
     return Prayer;
   })();
   Religion.Prayer = Prayer;
 })(Wasteros.Religion || (Wasteros.Religion = {}));
 // var Religion = Wasteros.Religion; ??
})(Wasteros || (Wasteros = {}));

```

## 단일체Singleton


```javascript
var Wasteros;

(function(Wasteros) {
	var Wall = (function() {
		function Wall() {
			this.height = 0;
			if (Wall._instance)
				return Wall._instance;
			Wall._instance = this;
		}
		
		Wall.prototype.setHeight = function(height) {
			this.height = height;
		};
		Wall.prototype.getStatus = function(status) {
			console.log('Wall is ' + this.height + ' meters wall');
		};
		Wall.getInstance = function() {
			if (!Wall._instance) {
				Wall._instance = new Wall();
			}
			return Wall._instance;
		};
		Wall._instance = null;
		return Wall;
	})();
	Wasteros.Wall = Wall;
})(Wasteros || (Wasteros = {}));

```

## 프로토타입Prototype

```javascript
var Wasteros;

(function(Wasteros) {
	(function(Failies) {
		var Lannister = (function() {
			function Lannister() {}
			Lannister.prototype.clone = function() {
				var clone = new Lannister();
				for (var attr in this) {
					clone[attr] = this[attr];
				}
				return clone;
			}
			return Lannister;
		})();
		Failies.Lannister = Lannister;
	})(Wasteros.Failies || (Wasteros.Failies = {}));
	var Failies = Westeros.Families;
})(Wasteros || (Wasteros = {}));

var jamie = new Wasteros.Failies.Lannister();
jamie.swordSkills = 9;
jamie.charm = 6;
jamie.wealth = 10;
var tryion = jamie.clone();
tryion.charm = 10;

```

## 요약
- 추상화 팩토리: 교체 가능한 Kit, 관련 객체들의 집합을 구축
- 빌더: 매개변수 문제에 대한 해결책
- 팩토리 메서드: 추상 팩토리를 보완하는 정적 팩토리 제공
- 단일체: 단 하나의 인스턴스 제공
- 프로토타입: 다른 객체를 기반으로 객체를 구축하는 패턴
