# 4. 구조패턴
----------------
객체들이 상호작용 할 수 있는 간단한 방법들을 기술
* 적응자(Adapter)
* 가교(Bridge)
* 복합체(Composite)
* 장식자(Decorator)
* 퍼사드(Facade)
* 플라이급(Flyweight)
* 프록시(Proxy)

## 적응자(Adapter)
인터페이스에 완벽하게 맞지 않는 클래스를 사용할 때가 많다 > 우리 인터페이스에 맞추기 위해 추가 적응자(adapter)가 필요.

### 사용되는 케이스.
1) 사용하려는 클래스가 필요한 메소드가 없는 경우 > 어댑터 클래스에서 추가구현
2) 숨기고 싶은 메소드를 추가로 가지고 있는 경우 > 어댑터 클래스에서 제외시키거나.

### 클래스구현
adapter가 요구된 인터페이스를 구현(> 어댑터는 구현한 모델), 어댑터를 구현클래스가 hasing하는 구조.
흔히 적응자 패턴은 코드의 추상화 레벨을 변경하는 데 사용됨.

### 코드예제
-실제 배의 인터페이스
interface Ship{
	SetRudderAngleTo(angle: number);
	SetSailConfiguration(configuration: SailConfiguration);
	SetSailAngle(sailId: number, sailAngle: number);
	GetCurrentBearing(): number;
	GetCurrentSpeedEstimate(): number;
	ShiftCrewWeightTo(weightToShift: number, locationId: number);
}
-추상화 한 인터페이스
interface SimpleShip{
	TurnLeft();
	TurnRight();
	GoFoward();
	}	//모든 세부사항들을 추상화한 인터페이스. 이를 통해 누구나 이해하고 읽기 쉬워짐

-어댑터 코드 ( SimpleShip 인터페이스를 가지지만, 실제론 래핑된 Ship인스턴스서 동작함)
var ShipAdapter = (function() {
	funtion ShipAdapter() {
	this._ship = new Ship();
	}
	ShipAdapter.prototype.TurnLeft = function(){
		this._ship.SetRudderAngleTo(-30);
		this._ship.SetSailAngle(3,12);
	}
	ShipAdapter.prototype.TurnRight = function(){
		this._ship.SetRudderAngleTo(30);
		this._ship.SetSailAngle(5,-9);
	}
	ShipAdapter.prototype.GoForward = function(){
		//...
		}
	return ShipAdapter;
})();	//라이브러리 코드를 빌드 할 때, 적응자는 내부 메소드를 마스킹하여 사용자에게 필요한 제한된 함수만을 보여줌.

-패턴 사용 코드
var ship = new ShipAdapter();
ship.goForward();
ship.TurnLeft();

*적응자는 코드 인터페이스를 단순화 할 수 있는 매우 강력한 패턴.
*라이브러리를 적응자로 래핑하여 직접호출해야 하는 경우를 줄일 수 있다.
*적응자 패턴을 변형하면 여러 다른 구현에서 일관된 인터페이스를 제공할 수 있다. 이는 가교패턴으로 알려져 있음

## 가교(Bridge) 패턴
하나의 인터페이스를 가지고 각각이 다른 구현의 중간자 역할을 하는 여러 적응자를 만들 수 있음 ex.)장애방지 구성에 사용되는 두 개의 서로 다른 서비스
적응자는 일관된 인터페이스를 제공.

###구현 코드
var OldGods = (function() {
	function OldGods(){
	}
	OldGods.prototype.prayTo = function (sacrifice){	//sacrifice라는 파라미터
		console.log("We Old Gods hear your prayer");
	}
	return OldGods;
})();
Religion.OldGods = OldGods;

var DrownedGod = (function() {
	function DrownedGod(){
	}
	DrownedGod.prototype.prayTo = function (humanSacrifice){	//humanSacrifice라는 파라미터
		console.log("BUBBLE GURGLE");
	}
	return DrownedGod;
})();
Religion.DrownedGod = DrownedGod;

var SevenGods = (function() {
	function SevenGods(){
	}
	SevenGods.prototype.prayTo = function (prayerPurpose){		//prayerPurpose
		console.log("~~~");
	}
	return SevenGods;
})();
Religion.SevenGods = SevenGods;

//prayTo에 대한 파라미터가 서로 달라서. 앞서 했던 팩토리 메소드 패턴으로 하면 인터페이스 문제가 있을 수 있음
//prayTo(sacrifice), prayTo(humanSacrifice), prayTo(prayerPurpose)	>>일관된 호출이 아님.

interface God
{
	prayTo():void;
}	//이 인터페이스 만으론 위의 클래스와 문제가 있음.

//>>클래스와 서명(? 파라미터) 사이 가교역할 할 수 있는 적응자(adapter 클래스) 만들기.

var OldGodsAdapter = (function () {
	function OldGodsAdapter() {
		this._oldGods = new OldGods();
	}
	OldGodsAdapter.prototype.prayTo = function () {
		var sacrifice = new Sacrifice();
		this._oldGods.prayTo(sacrifice);	//외부에서 보면 prayTo()로 보임, 파라미터 없이 일관되게 사용 가능.?
	};
	return OldGodsAdapter;
})();
Religion.OldGodsAdapter = OldGodsAdapter;

var DrownedGodAdapter = (function () {
	function DrownedGodAdapter() {
		this._drownedGod = new DrownedGod();
	}
	DrownedGodAdapter.prototype.prayTo = function () {
		var sacrifice = new HumanSacrifice();
		this._drownedGod.prayTo(sacrifice);	//외부에서 보면 prayTo()로 보임, 파라미터 없이 일관되게 사용 가능.?
	};
	return DrownedGodAdapter;
})();
Religion.DrownedGodAdapter = DrownedGodAdapter;

var SevenGodsAdapter = (function () {
	function SevenGodsAdapter() {
		this.prayerPurposeProvider = new PrayerPurposeProvider();	//어댑터 설정
		this._sevenGods = new SevenGods();
	}
	SevenGodsAdapter.prototype.prayTo = function () {
		this._sevenGods.prayTo(this.prayerPurposeProvider.GetPurpose());
	};
	return SevenGodsAdapter;
})();
Religion.SevenGodsAdapter = SevenGodsAdapter;

var PrayerPurposeProvider = (function () {
	function PrayerPurposeProvider() {
	}
	PrayerPurposeProvider.prototype.GetPurpose = function () {
	};
	return PrayerPurposeProvider;
})();
Religion.PrayerPurposeProvider = PrayerPurposeProvider;
var Sacrifice = (function () {
	function Sacrifice() {
	}
	return Sacrifice;
})();
Religion.Sacrifice = Sacrifice;

var HumanSacrifice = (function () {
	function HumanSacrifice() {
	}
	return HumanSacrifice;
})();
Religion.HumanSacrifice = HumanSacrifice;
})(Westeros.Religion || (Westeros.Religion = {}));
var Religion = Westeros.Religion;
})(Westeros || (Westeros = {}));

//일관된 인터페이스를 제공(prayTo)해 동일하게 취급할 수 있음.
//인터페이스가 달랐다면 prayTo(~~)안에 파라미터를 선언해줘야 했을 것. > 코드의 복잡성 & 강한 연결정도를 띄었을 것.
//팩토리 메소드 패턴과 동일하게 사용할 수 있음

> At first sight, the Bridge pattern looks a lot like the Adapter pattern in that a class is used to convert one kind of interface to another.
> However, the intent of the Adapter pattern is to make one or more classes' interfaces look the same as that of a particular class.
> The Bridge pattern is designed to separate a class's interface from its implementation
> so you can vary or replace the implementation without changing the client code.

##복합체
복합체를 컴포넌트로 교환하여 처리하는 특별한 패턴.
복합체를 구축하는 두가지 방법 :
1) 고정된 개수의 다양한 컴포넌트로 부터 구축되는 경우.
2) 정해지지 않은 개수의 컴포넌트로 부터 구축되는 경우.
주요기능 : 자식요소들과 컴포넌트의 상호교환

### 예제 : 트리구조 생각
노드 , 간선, 리프

### 구현
리프노드 구현
var SimpleIngredient = (function (){
	function SimpleIngredient(name, calories, ironContent, vitaminCContent){
		this.name = name;
		this.calories = calories;
		this.ironContent = ironContent;
		this.vitaminCContent = vitaminCContent;
	}	//생성
	SimpleIngredient.prototype.GetName = function() {
		return this.name;
	};
	SimpleIngredient.prototype.GetCalories = function() {
		return this.calories;
	}
	SimpleIngredient.prototype.GetIronContent = function() {
		return	this.ironContent;
	}
	SimpleIngredient.prototype.GetVitaminCContent = function() {
		return this.vitaminCContent;
	}
	return SimpleIngredient;
	})();
	Food.SimpleIngredient = SimpleIngredient;

var CompoundIngredient = (function() {//리프노드들을 이용하여 합칠 수 있는 클래스
	function CompoundIngredient(name){
		this.name = name;
		this.ingredients = new Array();
	}
	CompoundIngredient.prototype.AddIngredient = function(ingredient) {
		this.ingredients.push(ingredient);
	};	//고정되지 않은 컴포넌트들을 추가.
	CompoundIngredient.prototype.GetName = function(){
		return this.name;
	}
	CompoundIngredient.prototype.GetCalories = function(){
		var total = 0;
		for(var i=0; i <this.ingredients.lenth; i++) {
			total += this.ingredients[i].GetCalories();
		}
		return total;
	}
	CompoundIngredient.prototype.GetIronContent = function(){
		var total = 0;
		for(var i=0; i <this.ingredients.lenth; i++) {
			total += this.ingredients[i].GetIronContent();
		}
		return total;
	}
	CompoundIngredient.prototype.GetVitaminCContent = function(){
		var total = 0;
		for(var i=0; i <this.ingredients.lenth; i++) {
			total += this.ingredients[i].GetVitaminCContent();
		}
		return total;
	}
	return CompoundIngredient;
	})();

	//CompoundIngredient라는 클래스(?)가 각각 노드들이 똑같은 일을 수행할 수 있는 인터페이스를 제공

	var egg = new SimpleIngredient("Egg",155,6,0);
	var milk = new SimpleIngredient("Milk",42,0,0);
	var sugar = new SimpleIngredient("Sugar",387,0,0);
	var rice = new SimpleIngredient("Rice",370,8,0);

	var ricePudding = new CompoundIngredient("Rice Pudding");

	ricePudding.AddIngredient(egg);
	ricePudding.AddIngredient(milk);
	ricePudding.AddIngredient(sugar);
	ricePudding.AddIngredient(rice);

	//복합체는 트리구조기 때문에, 노드를 다루기 쉬움.
	//단일객체(노드)와 복합체도 한종류의 클래스로 설계. 복합체를 단일객체로도 다룰 수 있는 구조로 설계해서 똑같은 인터페이스로 똑같이 다룰 수 있게 함.
	//복합객체와 단일객체에 할 수 있는 모든 기능을 인터페이스로 약속해놔야지 사용 가능함.

## 장식자(Decorator) 패턴
기존의 클래스를 포장하거나 확장하는 데 사용됨
기존 컴포넌트의 서브클래싱의 대안으로 사용됨
*서브클래싱 : 기존의 컨트롤 형태나 동작을 변경하는 기법. > 일반적으로 컴파일 시 실행되고, 때문에 런타임 시에 변경할 수 있는 방법이 없음.

명시적으로 코드를 작성하기가 쉽지 않을 때, 장식자 패턴을 사용해서 전달되는 파라미터 변경하거나 래핑된 인스턴스를 호출하기 전에 추가적 동작 수행.

### 구현 코드
var BasicArmor = (function() {
		function BasicArmor(){
		}
		BasicArmor.prototype.CalculateDamageFromHit = function(hit) {
			return  hit.Strength * .2;
		}
		BasicArmor.prototype.GetArmorIntergrity = function() {
			return 1;
		}
		return BasicArmor;
	})();
	Armor.BasicArmor - BasicArmor;

var ChainMail = function(){
	function ChainMail(decoratedArmor){
		this.decoratedArmor = decoratedArmor;
	}
	ChainMail.prototype.CalculateDamageFromHit = function(hit){
		hit.Strength = hit.Strength * .8;
		return this.decoratedArmor.CalculateDamageFromHit(hit);
	}
	ChainMail.prototype.GetArmorIntergrity = function(){
		return .9 * this.decoratedArmor.GetArmorIntergrity();
	};
	return ChainMail;
}();
Armor.ChainMail = ChainMail;

//기능 확장이 필요할 때 서브클래싱 대신 사용할 수 있는 유용패턴
참고 : http://jusungpark.tistory.com/9


##퍼사드(Facade)
클래스 집합의 간략화된 인터페이스를 제공, Adapter 패턴과 사용은 동일한데, 전체에 제공할 때 사용한다고 보면 될듯
퍼사드 뜻이 정면,표면 > 외관에 간략화된 인터페이스를 제공
**다형성이 필요할 땐 어댑터를 쓴다고 위키가 얘기함
API를 다룰 때 매우 유용한 추상화 방법.
세분화된 API에 퍼사드를 사용하면 쉬운 인터페이스를 만들 수 있음.

### 플라이급
객체의 인스턴스는 많지만, 각각의 인스턴스의 변화는 아주 작은 경우에 사용함.
객체의 생성 비용이 많이 들 때 사용한다. > 데이터 리소스절약 하는 구조 패턴.

### 구현코드
var Soldier = (function () {
	function Soldier() {
		this.Health = 10;
		this.FightingAbility = 5;
		this.Hunger = 0;
	}
	return Soldier;
	})

var Soldier = (function(){
	function Soldier(){}
	Soldier.prototype.Health = 10;
	Soldier.prototype.FightingAbility = 5;
	Soldier.prototype.Hunger = 0;
	});

## 프록시
객체를 생성하는 비용이 비싸므로, 객체의 생성 및 사용을 제어할 수 있는 방법으로 프록시 패턴을 보임.
프록시패턴은 실제 인스턴스의 인터페이스를 미러링함.
* 고가 객체의 지연 인스턴스 생성
 > 인스턴스가 실제로 사용되지 않는 경우 프록시가 내부 인스턴스를 확인하고 생성되지 않은 경우 호출하여 생성할 수 있음( 생성 리소스 제어 : Lazy instantiation 지연생성)
* 비밀 데이터 보호
 > 프록시를 통해 호출검토하여 보안 확인된 경우에 메소드 호출
* 원격 메소드 호출 스터빙
 > 단순 호출의 인터페이스로 프록시 이용
* 메소드 호출 전후에 추가 작업을 삽입.
 > 추가기능 삽입하여 유혀성검사, 결과변경등 포함

### 구현 코드
var BarrelCalculator - (function (){
	function BarrelCalculator() {
	}
	BarrelCalculator.prototype.calculateNumberNeeded = function(volume) {
		return Math.ceil(volume/357);
	}
	return BarrelCalculator;
	}) ();

	//프록시 코드
	var DragonarrelCalculator = (function (){
		function DragonBarrelCalculator(){

		}
		DragonBarrelCalculator.prototype.calculateNumberNeeded = function(volume){
			if(this._barrelCalculator ==null)
				this>_barrelCalculator = new BarrelCalculator();
				retrun this._barrelCalculator.calculateNumberNeeded(volume * .77);
		}
		return DragonarrelCalculator;
		})();
