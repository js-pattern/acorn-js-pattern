//3장 생성패턴
/*
클래스가 다른 클래스에 최소한의 의존성만 가지도록 하는것 >> 분리가능한 코드를 만들어. >> 너무 밀접한 관계를 가지게 되면 의존성이 높다.
*/
//ex. 의존성이 강한 코드
var Westeros;
(function (Westeros) {
	var Ruler = (function(){
		function Ruler(){
		this.house = new Westeros.Houses.Targaryen();
		}
		return Ruler;
	})();	//Westeros 변수 선언, Ruler는 생성하자마자 가문이 Targaryen으로 직접 연결됨 > 강한 의존
	Westeros.Ruler = Ruler;
})(Westeros || (Westeros = {}));


//1. 추상 팩토리 패턴
/*객체의 구체적인 유형을 모린 채 객채를 생성하는 방법.
  이전에 작성했던 소스보다 유연한 애플리케이션 생성 가능.
  **선언된 인터페이스를 구체 클래서(Concrete factory)에서 구현해 return함.
*/

/*cf. 덕타이핑(Duck Typing)
  동적 타이핑의 한 종류로, 객체의 변수 및 메소드의 집합이 객체의 타입을 결정하는 것을 말함.(>미리 객체의 타입이 결정되어 있지 않음)
  덕타이핑은 객체가 어떤 타입에 걸맞은 변수와 메소드를 지니면 해당 타입으로 속하는 것으로 간주함.
  오버헤드(특별한 목표를 위해 필요한 시간,리소스,자원등)를 줄일 수 있지만 불확실성은 높일 수 있음.
*/

/* cf. 모듈구조
*/

//1)구현
//구체적 concrete class 선언
var KingJoffery = (function() {
	function KingJoffery() {
		console.log("I'm Joffery");
	}
	KingJoffery.prototype.makeDecision = function() {
		console.log("Joffery makes decision");
	};
	KingJoffery.prototype.marry = function() {
		console.log("Joffery married with Sansa");
	};
	return KingJoffery;
})();

var LordTywin = (function () {
	function LordTywin() {
		console.log("I'm Tywin");
	};
	LordTywin.prototype.makeDecision = function(){
		console.log("lani's always pay his depbts");
	};
	return LordTywin
})();	
//구체적인 factory 선언
var LannisterFactory = (function() {
	function LannisterFactory(){
	};
	LannisterFactory.prototype.getKing = function(){
		return new KingJoffery();
	};
	LannisterFactory.prototype.getHand = function(){
		return new LordTywin();
	};
	return LannisterFactory;
})();
//팩토리 사용을 위한 클래스
var CourtSession = (function(){
	function CourtSession(abstractFactory) {
		this.abstractFactory = abstractFactory;	//파라미터 값으로 팩토리 생성 
		this.COMPLAINT_THRESHOLD = 10;
	}
	CourtSession.prototype.complaintPresented = function(complaint)
	{
		if(complaint.severity < this.COMPLAINT_THRESHOLD){
			this.abstractFactory.getHand().makeDecision();
		}else
			this.abstractFactory.getKing().makeDecision();
	};	//파라미터 값에 따라서 분기
	return CourtSession;
})();

//2. 빌더 패턴
/*클래스가 구성된 방식에 따라 인터페이스 구현이 달라지는 경우... 클래스 구축 단순화&구조 지식없이 클래스 구축할 떄 사용.
  생성자가 클래스 생성의 복잡지 않도록. 생성자 추가 없이 빌더 연결로 생성. > 객체 빌드하는데 필요한 로직을 중앙집중화 하여 
  ex.토너먼트..  유틸리티 클래스 생성
*/
//구현
//1) 유틸리티 클래스 사용 ( 중앙 로직이 불러올 클래스들을 선언 )
var Event = (function () {
	function Event(name){
		this.name = name;
	}
	return Event;
})();
var Prize = (function() {
	function Prize(name){
		this.name = name;
	}
	return Prize;
})();

var Attendee = (function() {
	function Attendee(name){
		this.name = name;
	}
	return Attendee;
})();
Westeros.Event = Event;
Westeros.Prize = Prize;
Westeros.Attendee = Attendee;

var Tournament = (function(){
	this.Events = [];
	function Tournament(){
	}
	return Tournament;
})();
Westeros.Tournament = Tournament;

//서로다른 토너먼트를 생성하는 빌더 선언 > 
var LannisterTournamentBuilder = (function() {
	function LannisterTournamentBuilder(){
	}
	LannisterTournamentBuilder.prototype.build = function(){
		var tournament = new Tournament();
		tournament.events.push(new Event("Joust"));
		tournament.events.push(new Event("Melee"));

		tournament.attendees.push(new Attendee("Jamie"));

		tournament.prizes.push(new Prize("gold"));
		tournament.prizes.push(new Prize("More gold"));

		return tournament;
	};
	return LannisterTournamentBuilder;
})();
Westeros.LannisterTournamentBuilder = LannisterTournamentBuilder;

//빌더를 인자로 받아 생성하는 빌더. > 구현
var TournamentBuilder = (function(){
	function TournamentBuilder(){
	}
	TournamentBuilder.prototype.build = function(builder){
		return builder.build();
	}
	return TournamentBuilder;
})();

/*책 다이어그램 생각..
  Director : TournamentBuilder
  Builder : LannisterTournamentBuilder
			BaratheonTournamentBuilder
  사용자가 Director를 통해 빌더 접근하는걸로 생각했는데... 뭐징...??
*/

//3.팩토리 메소드 패턴
/*추상팩토리 : 관련성을 갖는 클래스들의 집합을 생성
  빌더패턴 : 복합객체를 만듦
  팩토리 메소드 : 인터페이스를 어떻게 구현할지에 대한 결정 없이 클래스가 인터페이스의 새로운 인스턴스를 요청할 수 있도록 허용함.
*/

// 구현
var WaterGod = (function() {
	function WaterGod(){
	}
	WaterGod.prototype.prayTo = function(){
		console.log("To WaterGod")
	};
	return WaterGod;
})();
var AncientGod = (function(){
	function AncientGod(){
	}
	AncientGod.prototype.prayTo = function(){
		console.log("To AncientGod")
	}
	return AncientGod;
})();
var DefaultGod = (function(){
	function DefaultGod(){
	}
	DefaultGod.prototype.prayTo = function(){
		console.log("To DefaultGod")
	}
	return DefaultGod;
})();
Religion.WaterGod = WaterGod;
Religion.AncientGod = AncientGod;
Religion.DefaultGod = DefaultGod;

//팩토리 필요 > 분기
var GodFactory = (function() {
	function GodFactory(){
	};
	GodFactory.Build = function(godName){
		if(godName ==="water")
			return new WaterGod();
		if(godName ==="ancient")
			return new AncientGod();
		return new DefaultGod();
	}
	return GodFactory;
})();

var GodDeterminant = (function(){
	function GodDeterminant(religionName, prayerPurpose){
		this.religionName = religionName;
		this.prayerPurpose = prayerPurpose;
	}
	return GodDeterminant;
})();

var Prayer = (function() {
	function Prayer(){
	}
	Prayer.prototype.pray = function (godName) {
		GodFactory.Build(godName).prayTo();
	}
	return Prayer;
})();

//단일체 (싱글톤)
/*가장 많이 쓰이는 패턴중 하나.
  최근 몇년간 선호도 떨어짐. 
  이유> 1.전역변수..
*/
//구현
var Westeros;
(function (Westeros) {
	var Wall = (function() {
		console.log("Wall")
		function Wall(){
			this.height = 0;
			if(Wall._instance)
				return Wall._instance;
			Wall._instance = this;
	}
	Wall.prototype.setHeight = function(height){
				console.log("setHeight")
		this.height = height
	};
	Wall.prototype.getStatus = function(){
				console.log("getStatus")
		console.log("Wall is " + this.height + " meter tall");
	};
	Wall.getInstance = function(){
				console.log("getInstance")
		if(!Wall._instance){
			Wall._instance = new Wall();
		}
		return Wall._instance;
	};
	Wall._instance = null;
	return Wall;
})();
Westeros.Wall = Wall;
})(Westeros || (Westeros = {}));

//단점 : 전역변수 사용 미화, 단위 테스트로 테스트하기 어려움, 단일체가 너무 많은 권한을 가짐(단일 책임의 원칙 위반)

//5)프로토타입 > js상속을 지원하는데 사용되는 매커니즘
/*기존 객체의 복사

*/
//구현
//기존의 객체를 복사해 새로운 객체를 생성
function clone(source, destination) {
	for(var attr in source.prototype){
		destination.prototype[attr] = source.prototype[attr];
	}
}
//자신의 복사본을 반환하는데 사용할 수 있도록 변경
var Westeros;
	(function (Westeros){
		(function (Families){
			var Lannister = (function () {
				function Lannister(){
				}
				Lannister.prototype.clone = function(){
					var clone = new Lannister();
					for (var attr in this){
						clone[attr] = this[attr];
					}
					return clone;
				};
				return Lannister;
			})();
			Families.Lannister = Lannister;
		})(Westeros.Families||Westeros.Families = {}));
		var Families = Westeros.Families;
	})(Westeros || Westeros = {}));