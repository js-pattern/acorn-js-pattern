# 생성패턴
* 자바스크립트에서의 디자인 패턴
* 추상 팩토리 패턴 ( Abstract Factory Pattern )
* 빌더 패턴 (  Builder Pattern )
* 팩토리 메소드 패턴 ( Factory Method Pattern )
* 단일체 패턴 ( Signletone Pattern )
* 프로토타입 패턴 ( Prototype Pattern )

## Javascript 에서의 디자인패턴
Javascript는 동적 타이핑 언어이므로 interface와 같은 부가적인 도구 없이 유연하고 간단하게 사용할 수 있지만 *불확실성* 을 야기하므로 단위테스트를 정확히 수행하여야 한다. (객체 타입을 검사하지 않음, 메소드는 구현되어있다고 가정)

## 추상 팩토리 패턴 ( Abstract Factory Pattern )
객체의 구체적인 유형을 모른 채 객체의 키트(Kit)를 생성하는 방법
Factory 패턴과 마찬가지로 객체의 *결합도*를 낮추기 위해 사용하지만
많은 수의 연관된 서브클래스를 특정 그룹으로 묶어 한번에 교체 할 수 있도록 만든 디자인패턴

### 추상 팩토리 패턴 예제
왕국시스템 문제
지배가문과 관련된 여러 연관클래스를 구체적으로 작성할시 애플리케이션이 유연하지 못하게 된다. 이 문제를 추상팩토리 패턴으로 유연하게 해결할 수 있다.
```javascript
//King 클래스 구현
var KingJoffery = (function () {
    function KingJoffery() {}

    KingJoffery.prototype.makeDecision = function () {
        console.log("[KingJoffery] makeDecision");
    };

    KingJoffery.prototype.marry = function () {
        console.log("[KingJoffery] marry");
    };

    return KingJoffery;
})();

//HandOfTheKing 클래스 구현
var LoadTywin = (function () {
    function LoadTywin() {}

    LoadTywin.prototype.makeDecision = function () {
        console.log("[LoadTywin] makeDecision");
    };

    return LoadTywin;
})();

//Abstract Factory
var LannisterFactory = (function () {
    function LannisterFactory() {}

    LannisterFactory.prototype.getKing = function () {
        return new KingJoffery();
    };

    LannisterFactory.prototype.getHandOfTheKing = function () {
        console.log('test');
        return new LoadTywin();
    }

    return LannisterFactory;
})();


//King 클래스 구현
var KingAerys = (function () {
    function KingAerys() {}

    KingAerys.prototype.makeDecision = function () {
        console.log("[KingAerys] makeDecision");
    };

    KingAerys.prototype.marry = function () {
        console.log("[KingAerys] marry");
    };

    return KingAerys;
})();

//HandOfTheKing 클래스 구현
var LordConnington = (function () {
    function LordConnington() {}

    LordConnington.prototype.makeDecision = function () {
        console.log("[LordConnington] makeDecision");
    };

    return LordConnington;
})();


//Abstract Factory
var TargaryenFactory = (function () {
    function TargaryenFactory() {}

    TargaryenFactory.prototype.getKing = function () {
        return new KingAerys();
    };

    TargaryenFactory.prototype.getHandOfTheKing = function () 	  {
        return new LordConnington();
    }

    return TargaryenFactory;
})();

var CourtSession = (function () {
    function CourtSession(abstarctFactory) {
        this.abstarctFactory = abstarctFactory;
        this.COMPLAINT_THRESHOLD = 10;
    }
    CourtSession.prototype.complaintPresented = function (complaint) {
        if (complaint.severity < this.COMPLAINT_THRESHOLD) {
            this.abstarctFactory.getHandOfTheKing().makeDecision();
        } else {
            this.abstarctFactory.getKing().makeDecision();
        }
    }

    return CourtSession;
})();

var courtSession = new CourtSession(new LannisterFactory());
courtSession.complaintPresented({
    severity: 8
});
courtSession.complaintPresented({
    severity: 12
});


var courtSession = new CourtSession(new TargaryenFactory());
courtSession.complaintPresented({
    severity: 8
});
courtSession.complaintPresented({
    severity: 12
});

```


## 빌더 패턴 ( Builder Pattern )
클래스의 구축을 단순화하고 사용자가 전문지식 없이 클래스를 구축할 때 빌더 패턴을 사용.  여러 구체 빌더는 구현할 때 생성자의 복잡성을 감소시킴
토너먼트 예제
참석자, 이벤트, 포상 등 설정이 복잡함. 여러곳에서 토너먼트를 생성하면 토너먼트에 대한 책임이 분산됨. 빌더패턴을 활용하여 객체를 빌드하는데 필요한 로직을 중앙집중화
```javascript
var Westeros;
(function (Westeros) {
    var Event = (function () {
        function Event(name) {
            this.name = name;
        }
        return Event;
    })();

    Westeros.Event = Event;

    var Prize = (function () {
        function Prize(name) {
            this.name = name;
        }
        return Prize;
    })();

    var Attendee = (function () {
        function Attendee(name) {
            this.name = name;
        }
        return Attendee;
    })();

    var Tournament = (function () {


        function Tournament() {
            this.events = [];
            this.attendes = [];
            this.prizes = [];

        }
        return Tournament;
    })();

    var LannisterTournamentBuilder = (function () {
        function LannisterTournamentBuilder() {

        }

        LannisterTournamentBuilder.prototype.build = function () {
            var tournament = new Tournament();

            tournament.events.push(new Event("Joust"));
            tournament.events.push(new Event("Melee"));

            tournament.attendes.push(new Attendee("Jamie"));

            tournament.prizes.push(new Prize("Gold"));
            tournament.prizes.push(new Prize("More Gold"));

            return tournament;
        }

        return LannisterTournamentBuilder;
    })();

    Westeros.LannisterTournamentBuilder = LannisterTournamentBuilder;

    var BaratheonTournamentBuilder = (function () {
        function BaratheonTournamentBuilder() {

        }

        BaratheonTournamentBuilder.prototype.build = function () {
            var tournament = new Tournament();

            console.log(tournament);
            tournament.events.push(new Event("Joust"));
            tournament.events.push(new Event("Melee"));

            tournament.attendes.push(new Attendee("Stannis"));
            tournament.attendes.push(new Attendee("Robert"));

            return tournament;
        }

        return BaratheonTournamentBuilder;
    })();

    Westeros.BaratheonTournamentBuilder = BaratheonTournamentBuilder;

    var TournamentBuilder = (function () {

        function TournamentBuilder() {

        }

        TournamentBuilder.prototype.build = function (builder) {
            return builder.build();
        }

        return TournamentBuilder;
    })();

    Westeros.TournamentBuilder = TournamentBuilder;
})(Westeros || (Westeros = {}));

var tournament = new Westeros.TournamentBuilder().build(new Westeros.BaratheonTournamentBuilder());

console.log(tournament);
```

### 빌더 패턴 다른 예제
최근에는 메소드 체이닝과 함께 생성자의 복잡도 감소면에서 빌더패턴을 많이 활용하고 있는 것 같다.  아래 예제는 라이브러리 같은 코드에서 많이 볼 수 있는 패턴을 작성해보았다.
```javascript
(function () {
    'use strict';

    var HttpRequest = (function () {

        function HttpRequest(header, parameter, method, url) {
            this.header = header || 'application/form-data';
            this.parameter = parameter || {};
            this.method = method || 'get';
            this.url = url || '';
        }

        HttpRequest.prototype.request = function () {
            console.log("HttpRequest -> url : " + this.url + ", method : " + this.method + ", header : " + this.header + ", parameter : " + JSON.stringify(this.parameter));
        };


        return HttpRequest;
    })();


    var HttpRequestBuilder = (function () {
        function HttpRequestBuilder() {
            this.header = '';
            this.parameter = '';
            this.method = '';
            this.url = '';
        }

        HttpRequestBuilder.prototype.setHeader = function (header) {
            this.header = header;
            return this;
        };

        HttpRequestBuilder.prototype.setParameter = function (parameter) {
            this.parameter = parameter;
            return this;
        };

        HttpRequestBuilder.prototype.setMethod = function (method) {
            this.method = method;
            return this;
        };

        HttpRequestBuilder.prototype.setUrl = function (url) {
            this.url = url;
            return this;
        };


        HttpRequestBuilder.prototype.build = function () {
            return new HttpRequest(this.header, this.parameter, this.method, this.url);
        }

        return HttpRequestBuilder;
    })();


    var requsetBuilder = new HttpRequestBuilder();
    var httpRequest = requsetBuilder
                        .setUrl("http://www.naver.com")
                        .setMethod("POST")
                        .setParameter({
                            test: 'aa'
                        })
                        .setHeader("application/json")
                        .build();

    httpRequest.request();
})();

```

## 팩토리 메소드 패턴 ( Factory Method Pattern )
팩토리 메소드 패턴은 객체를 생성하는 부분을 서브 클래스로 위임하는 패턴이다.
기본적으로 Product(Interface) 와 Product클래스 ProductFactory로 구성된다.
여기서 Product의 생성을 ProductFactory로 위임한다고 보면 된다.
이는 클래스 생성과 사용을 분리함으로써 테스트에 용이하고 클래스 생성을 연기할 수 있다는 장점이 있다.  주로 추상 팩토리 패턴과 많이 사용된다.

### 팩토리 메소드 패턴 예제

```javascript
var WateryGod = (function () {
    function WateryGod() {

    }

    WateryGod.prototype.prayTo = function () {
        console.log("[WateryGod] prayTo");
    };

    return WateryGod;
})();

var AncientGods = (function () {
    function AncientGods() {

    }

    AncientGods.prototype.prayTo = function () {
        console.log("[AncientGods] prayTo");
    };

    return AncientGods;
})();

var DefaultGod = (function () {
    function DefaultGod() {

    }

    DefaultGod.prototype.prayTo = function () {
        console.log("[DefaultGod] prayTo");
    }
})();

var GodFactory = (function () {

    function GodFactory() {

    }

    GodFactory.prototype.build = function (godName) {
        if (godName === "watery") return new WateryGod();
        if (godName === "ancient") return new AncientGods();
        return new WateryGod();
    }

    return GodFactory;
})();

var Prayer = (function () {
    function Prayer() {

    }

    Prayer.prototype.pray = function (godName) {
        new GodFactory().build(godName).prayTo();
    };

    return Prayer;
})();

var prayer = new Prayer();
prayer.pray("watery");
```


## 단일체 패턴 (  Singletone Pattern )
클래스의 인스턴스가 여러 개 생성되는 것을 방지하고 클래스 생성을 런타임시로 연기할 수 있음. 단, 단일체 패턴은 결과적으로 전역변수를 사용하기 때문에 단위 테스트가 어렵고 특히 자바스크립트에선 생성자를 private 설정을 할 수 없기 때문에 해당패턴을 깔끔하게 구현할 수 없다.

```javascript

var Westeros;

(function (Westeros) {
    var Wall = (function () {
        function Wall() {
            this.height = 0;
            if (Wall._instance)
                return Wall._instance;

            Wall._instance = this;
        }

        Wall.getInstance = function () {
            if (!Wall._instance) {
                Wall._instance = new Wall();
            }
            return Wall._instance;
        };

        Wall._instance = null;
        return Wall;
    })();

    Westeros.Wall = Wall;
})(Westeros || (Westeros = {}));

var a = new Westeros.Wall();
var b = new Westeros.Wall();

console.log(a === b);

```

## 프로토타입 패턴 ( Prototype Pattern )
생성할 객체들의 타입이 프로토타입인 인스턴스로부터 결정되도록 함.
복잡한 객체를 단지 한 번만 생성하고, 약간의 변화가 있는 다수의 객체로 복사 될 수 있도록 허용한다.

```javascript
var Westeros;

(function (Westeros) {
    (function (Families) {
        var Lannister = (function () {
            function Lannister() {

            }

            Lannister.prototype.clone = function () {
                var clone = new Lannister();
                for (var attr in this) {
                    clone[attr] = this[attr];
                }
                return clone;
            }

            return Lannister;
        })();
        Families.Lannister = Lannister;
    })(Westeros.Families || (Westeros.Families = {}))

})(Westeros || (Westeros = {}));

var jamie = new Westeros.Families.Lannister();
jamie.swordSkills = 9;
jamie.charm = 6;

var tryion = jamie.clone();

console.log(tryion);
```

#design-pattern
