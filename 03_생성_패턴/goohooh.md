# 3 생성 패턴

<U>**재사용을 위한 객체 생성 최적화**</U>

```js
function Ruler(){
	this.house = new Targaryen();
}
```

위 `Ruler`의 `house` 프로퍼티는 `Targaryen` 클래스에 강하게 결합돼있다.

변경이 일어나면 이러한 결합은 코드 전반에 변화를 야기할 수 있다.

이런 경우 아래 패턴들로 그 결합 정도를 개선하고 코드 재사용성을 증가시킬 수 있다.

- **추상 팩토리** _Abstract Factory_
- **빌더** _Builder_
- **팩토리 메소드** _Factory Method_
- **단일체** _Singleton_
- **프로토타입** _Prototype_


## 3.1 추상 팩토리

객체의 구체적인 유형을 모른 채 객체의 키트를 생성

### 구현

먼저 `King`클래스 구현.

```js
const KingJoffery = (() => {
	function KingJoffery () {}

	KingJoffery.prototype.makeDecision = () => {
		// ...
	};

	KingJoffery.prototype.marry = () => {
		// ...
	};

	return KingJoffery;
})();
```

이는 **정규 구체 클래스**(_regular concrete class_)로 어떤 세부 구현 사항도 포함할 수 있다.

유사하게 `HandOfTheKing` 클래스도 구현.

```js
const LordTywin = (() => {
	function LordTywin () {}

	LordTywin.prototype.makeDecision = () => {
		// ...
	}

	return LordTywin;
})();
```

구체 팩토리 메소드

```js
const LannisterFactory = (() => {
	function LannisterFactory () {}

	LannisterFactory.prototype.getKing = () => new KingJoffery();

	LannisterFactory.prototype.getHandOfTheKing = () => new LordTywin();

	return LannisterFactory;
})();
```

각 클래스의 새로운 인스턴스 생성 및 반환

다른 지배 가문 구현도 동일

```js
const TargaryenFactory = (() => {
	function TargaryenFactory () {}

	TargaryenFactory.prototype.getKing = () => new KingAerys();

	TargaryenFactory.prototype.getHandOfTheKing = () => new LordConnington();

	return TargaryenFactory;
})();
```

이제 추상 팩토리 패턴을 사용하려면 지배가문을 필요로하는 클래스가 필요하다

```js
const CourtSession = (() => {
	function CourtSession (abstractFactory) {
		this.abstractFactory = abstractFactory;
		this.COMPLANT_THRESHOLD = 10;
	}

	CourtSession.prototype.complaintPresented = complaint => {
		if (complaint.severity < this.COMPLAINT_THRESHOLD) {
			this.abstractFactory.getHandOfTheKing().makeDecision();
		} else {
			this.abstractFactory.getKing().makeDecision();
		}
	}

	return CourtSession;
})();
```

앞으로 `CourtSession` 클래스를 호출하고 전달하는 팩토리에 따라 다양한 기능을 삽입할 수 있다.

```js
const courtSession1 = new CourtSession(new TargaryenFactory());
courtSession1.complaintPresented({ severity: 8 });
courtSession1.complaintPresented({ severity: 12 });

const courtSession2 = new CourtSession(new LannisterFactory());
courtSession2.complaintPresented({ severity: 8 });
courtSession2.complaintPresented({ severity: 12 });
```

대규모 변경이 필요한 경우, 여러 객체들이 대체 없이 같이 사용돼야 하는 경우 유용하다


## 3.2 빌더

생성자의 복잡성 감소.

객체를 빌드하는 데 필요한 로직을 중앙집중화 하여 대량의 코드수정을 방지.

### 구현

토너먼트를 예시로 구현. 여러 종류의 토너먼트가 일어난다고 가정

```js
function Event (name) {
	this.name = name;
}

function Prize (name) {
	this.name = name;
}

function Attendee (name) {
	this.name = name;
}
```

토너먼트 자체는 간단한 클래스

```js
function Tournament () {
	this.Events = [];
	this.attendees = [];
	this.prizes = [];
}
```

다음은 서로 다른 토너먼트를 생성하는 두 개의 빌더 구현

```js
const LannisterTournamentBuilder = (() => {
	function LannisterTournamentBuilder () {}

	LannisterTournamentBuilder.prototype.build = function () {
		const tournament = new Tournament()
		tournament.events.push(new Event('Joust'));
		tournament.events.push(new Event('Melee'));

		tournament.attendees.push(new Attendee('Jamie'));

		tournament.prizes.push(new Prize('Gold'));
		tournament.prizes.push(new Prize('More Gold'));

		return tournament;
	}

	return LannisterTournamentBuilder;
})();

const BaratheonTournamentBuilder = (() => {
	function BaratheonTournamentBuilder () {}

	BaratheonTournamentBuilder.prototype.build = () => {
		const tournament = new Tournament();
		tournament.events.push(new Event('Joust'));
		tournament.events.push(new Event('Melee'));

		tournament.attendees.push(new Attendee('Stannis'));
		tournament.attendees.push(new Attendee('Robert'));

		return tournament;
	}	
})();
```

마지막 `TournamentBuilder`라 부를 `director`는 단순히 빌더를 인자로 받아 실행

```js
const TournamentBuilder = (() => {
	function TournamentBuilder () {}

	TournamentBuilder.prototype.build = builder => builder.build();

	return TournamentBuilder;
})();
```

**빌더는 완전히 구현된 객체를 반환하지 않아도 된다.**
- 객체를 부분적으로 처리하는 빌더 생성
- 이를 다른 빌더로 전달하여 객체 생성 마무리

이러한 패턴은 제한적 역할을 수행하는 여러 클래스로 나누어 객체를 생성하게 한다.

위 코드는 이벤트를 처리하는 빌더와 참석자를 처리하는 빌더를 가진다.

> 저자는 복잡한 객체가 서로 다른 접근 방식에 따라 생성돼야 할 경우 의미가 있다고 함

## 3.3 팩토리 메서드

- 추상 팩토리 : 관련성을 갖는 클래스들의 집합 생성

- 빌더 : 추상 팩토리와 다른 전략으로 복합 객체 생성

팩토리 메서드는 인터페이스를 어떻게 구현할지에 대한 결정 없이 클래스가 인터페이스의 새로운 인스턴스를 요청할 수 있도록 허용.

팩토리는 어떤 구현을 선택할지에 대해 몇 가지 전략을 사용할 수 있음

### 구현

종교별 기도 규칙이 다르지만 `Prayer` 클래스는 이를 구성하는 방법에 대해 알 필요가 없도록 하고자 한다.

먼저 대상이 될 다양한 신들을 생성한다.

```js
const WateryGod = (() => {
	function WateryGod () {}
	WateryGod.prototype.prayTo = () => {}
	return WateryGod
})();

const AncientGods = (() => {
	function AncientGods () {}
	AncientGods.prototype.prayTo = () => {}
	return AncientGods; 
})();

const DefaultGod = (() => {
	function DefaultGod () {}
	DefaultGod.prototype.prayTo = () => {}
	return DefaultGod; 
})();
```

다음은 서로 다른 신을 구성하는 역할을 담당하는 팩토리

```js
function GodFactory () {
	function GodFactory () {}
	GodFactory.Build = godName => {
		if (godName === 'watery') return new WateryGod();
		if (godName === 'ancient') return new AcientGods();
		return new DefaultGod();
	}

	return GodFactory
}
```

기도의 목적에 따라 대상이 되는 신이 결정되기도 한다

```js
function GodDeterminant (religionName, prayerPurpose) {
	this.religionName = religionName;
	this.prayerPurpose = prayerPurpose;
}
// 이렇게 되면 팩토리는 이 클래스를 취하도록 수정이 필요하다.
```


```js
const Prayer = (() => {
	function Prayer () {}
	Prayer.prototype.pray = godName => {
		GodFactory.build(godName).prayTo();
	}
	return Prayer;
})();
```

**JS에서 팩토리 메서드 패턴이 유용하다.**

- 인스턴스 생성과 시용의 분리가 유용한 경우가 많다.
- 인스턴스 생성 테스트도 간단해진다.
- `Prayer` 클래스의 테스트를 위해 가짜 팩토리를 추가하는 것도 용이해진다.
- 클래스가 인스턴스 생성 구현의 선택을 다른 클래스로 연기하도록 도와준다.
- 전략패턴(5장)처럼 다우싀 유사한 구현이 필요할 경우 유용하다.
- 주로 추상 팩토리 패턴과 많이 사용된다.(추상 팩토리의 구체 객체를 구현할 때, 추상팩토리는 다수의 팩토리 메서드를 포함하게됨)


## 3.4 단일체

많이 쓰이지만 최근엔 되도록 쓰지 않는 추세다.

- 전역 변수가 필요할 때 사용되며, 실수로 복잡한 객체의 사본이 여러 개 생성되는 것을 방지한다.
- 객체의 인스턴스가 처음 사용될 때까지 객체의 인스턴스 생성 연기가 가능하게 한다.

### 구현

```js
const Wall = (() => {
	function Wall () {
		this.height = 0;
		if (Wall._instance) return Wall._instance; // 이상해 보이지만 현재의 인스턴스를 반환
		Wall._instance = this;
	}

	Wall.prototype.setHeight = height => {
		this.height = height;
	}

	Wall.prototype.getStatus = () => {
		console.log(`Wall is ${this.height} meters tall`);
	}

	Wall.getIncetance = () => {
		if (!Wall._instance) return new Wall(); // 멀티 스레딩 에서는 race condition	을 위한 처리가 필요
		return Wall._instance
	}

	Wall._instance = null;
})();
```

### 단점

인스턴스 생성을 쉽게 재정의할 수 없기 때문에 유닛 테스트가 어렵다.

싱글턴 패턴으로 해결할 수 있는 문제 대부분은 다른 방법으로 해결할 수 있다.

그리고 JS에서 깔끔하게 구현할 수 없다.


## 3.5 프로토타입

JS 상속을 지원하는 데 사용되는 메커니즘

프로토타입을 상속에반 제한하여 사용할 필요는 없다.

기존 객체 복사는 유용한 패턴이 될 수 있으며 구성된 객체를 복하는 것이 편라한 경우도 많다.

예를 들어, 복제를 활용해 생성한 이전의 인스턴스를 저장하여 객체의 상태 기록을 유지할 수 있다.

### 구현

새로운 새대가 탄생하면, 객체를 새로 만드는 것보다 기존의 가족 구성을 복사하고 수정하는 편이 더 쉽다.

```js
function clone (source, destination) {
	for(let attr in source.prototype) {
		destination.prototype[attr] = source.prototype[attr];
	}
}
```

이제 클래스 내에서 자신의 복사본을 반환하도록 변경할 수 있다.

```js
const Lannister = (() => {
	function Lannister () {}

	Lannister.prototype.clone = () => {
		const clone = new Lannister();
		for(let attr in this) {
			clone[attr] = this[attr];
		}
		return clone;
	}

	return Lannister;
})();
```

이제 아래와 같이 사용할 수 있다.

```js
const jamie = new Lannister();

jamie.swordSkills = 9;
jamie.charm = 6;
jamie.wealth = 10;

const tyrion = jamie.clone();
tyrion.charm = 10;

tyrion.wealth === 10 // true
tyrion.swordSkills === 9 // true
```

프로토타입 패턴은 복잡한 객체를 한 번만 생성하고, 약간의 변화가 있는 다수의 객체로 복사하도록 허용한다.

원본 객체가 복잡하지 않다면, 복제 방법을 통해 얻을 수 있는 이익은 별로 없다.

> 종속적 객체에 프로토타입 방식을 사용할 때, 복제의 대상이 복잡한 객체인지 고려해야한다.


## 힌트와 팁

생성 패턴은 대부분 크로스 커팅 로직이 포함될 수 있는 확장점을 제공한다.

이는 크로스 커팅 로직이 여러 타입의 객체에 적용할 수 있음을 의미한다.

애플리케이션 전체에 간여할 수 있는 방법을 찾고자 한다면 팩토리가 좋은 방법이 된다.

---

생성 패턴이 유용하더라도 자주 사용하지 않는 편이 좋다.

대부분 객체의 인스턴스 생성은 여전히 일반적인 방법을 사용하는 것이 좋다. 각 상황에 맞게 써야한다는 뜻이다.

무엇보다 일반적인 생성 보다 복잡하고, 이는 버그 발생 가능성을 높인다.

## 요약

생성 패턴은 객체 생성의 전형적 메서드에 추상화를 제공한다.

- 추상팩토리 : 교체 가능한 키트 또는 관련 객체들의 집합을 구축할 수 있는 방법을 제공
- 빌더 : 텔레 스코핑 매개변수 문제에 대한 해결책을 제공하여 복잡한 객체를 쉽게 구성할 수 있게 도와줌
- 팩토리 메서드 : 추상 팩토리 보완. 정적 팩토리를 통해 서로 다른 구현이 가능케 함
- 단일체 : 단 하나의 클래스 사본을 제공. 의문점이 남는 패턴
- 프로토타입 : JS에서 기존의 다른 객체를 기반으로 객체를 구축하는데 사용