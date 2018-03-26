# 4 구조 패턴

<U>**객체들이 상호작용할 수 있는 방법**</U>

- **적응자** _Adapter_
- **가교** _Bridge_ 
- **복합체** _Composite_ 
- **장식자** _Decorator_
- **퍼사드** _Facade_
- **플라이급** _Flyweight_
- **프록시** _Proxy_


## 4.1 적응자

- 인터페이스에 완벽하게 맞지 않는 클래스를 사용할 경우가 생길 수 있다.
- 클래스는 필요한 메소드가 없을 수도, 숨기고 싶은 메소드를 가진 경우도 있을 수 있다.
- 보통 제 3자가 작업한 코드를 이용할 경우

**우리의 인터페이스에 맞추기 위해 적응자가 필요**

- 적응자 클래스는 요구되는 인터페이스를 구현한 작은 코드 조각
- 일반적으로 구현 클래스의 비공개 복사본을 래핑, 이를 통해 프록시로 호출
- 이로써 코드의 추상화 레벨을 변경 가능

### 구현

`Ship` 인터페이스

```typescript
interface Ship {
    SetRudderAngleTo (angle: number);
    SetSailConfiguration (configuration: SailConfiguration);
    SetSailAngle (sailId: number, sailAngle: number);
    GetCurrentBearing () : number;
    GetCurrentSpeedEstimate () : number;
    ShiftCrewWeightTo (weghtToShift: number, locationId: number);
}

//세부 사항을 추상화한 단순화 인터페이스
interface SimpleShip {
    TurnLeft();
    TurnRight();
    GoForward();
}
```

`Ship` 인터페이스의 높은 수준의 추상화를 얻어냈다. 

이처럼 단순한 선박으로 바뀌기 위해 적응자가 필요하다.

```js
const ShipAdapter = (() => {
    function ShipAdapter() {
        this._ship = new Ship();
    }

    ShipAdapter.prototype.TurnLeft = () => {
        this._ship.SetRudderAngleTo(-30);
        this._ship.SetSailAngle(3, 12);
    }

    ShipAdapter.prototype.TurnRight = () => {
        this._ship.SetRudderAngleTo(30);
        this._ship.SetSailAngle(5, -9);
    }

    ShipAdapter.prototype.GoForward = () => {
        // _ship에 대한 다른 일 수행
    }

    return ShipAdapter;
})();
```

실제로 더 복잡하겠지만 사용자에게 간단한 인터페이스를 보여줄 목적이므로 괜찮다.

제시되는 인터페이스는 일부 내보 메서드가 제한되도록 설정할 수 있다.

```js
const ship = new ShipAdapter();
ship.GoForward();
ship.TurnLeft();
```

> `adapter`라는 이름을 사용하는 것은 사용자가 적응자를 인식하게 하므로 권장하지 않는다.

인터페이스를 다른 인터페이스에 맞출 경우, 적응자 패턴이 자칫 복잡해질 수 있다.

**적응자가 너무 복잡해 지지 않도록 주의를 요한다.**

하여 여러 적응자를 차곡차곡 쌓아 올리듯 구축할 수도 있다.

적응자가 너무 커진다는 판단이 들면, 우선 작업을 멈추고 적응자가 단일 책임 원칙을 따르고 있는지 검토한다.

만약 적응자가 너무 복잡해진다면 이후 나올 복합체 객체로 대체할 수 있다.

---

테스트 관점에서 적응자는 외부 모듈의 종속성을 감추는 데도 유용하다.

단위 테스트에서 라이브러리 테스트를 피하는 것이 좋지만, 적응자를 통해

호출이 바르게 전달되고 있는지 확인하기 위해 적응자를 테스트할 수 있다.

---

- 코드 인터페이스를 단순화할 수 있는 패턴. 요구사항에 맞게 인터페이스를 조작할 시 유용
- 여러 작은 라이브러리를 사용할 시, 라이브러리들을 적응자로 래핑하면 라이브러리 교체가 쉬워진다.
- 약간 변형하면 여러 다른 구현에서 일관된 인터페이스를 제공하는 가교 패턴이 된다.

## 4.2 가교

- 적응자 패턴을 새로운 수준으로 만든다.
- 하나의 인터페이스로 각각 다른 구현의 중간자 역할을 하는 여러 적응자를 만들 수 있다.

장애방지 구성에 사용되는 두 개의 서로 다른 서비스를 사용할 경우,

1. 두 서비스는 다른 API를 제공, 애플리케이션에서 필요로 하는 정확한 인터페이스는 제공하지 못함.
2. 코드 산순화를 위해 적응자로 일관된 인터페이스 제공.
3. 이를 통해 각 API가 일관되게 호출되도록 부족한 부분을 메움.

### 구현

종교별 다른 방법으로 기도와 봉헌을 한다. 각 신자들이 정해진 시간에 정확한 방법으로

기도를 하게 하기위한 복잡한 과정을 외부에 노출하지 않고 적응자를 이용해 단순화 시킨다.

```js
const OldGods = (() => {
    function OldGods () {}

    OldGods.prototype.prayTo = sacrifice => {
        console.log('We Old Gods hear your prayer');
    }

    return OldGods;
})();

const DrownedGod = (() => {
    function DrownedGod () {}

    DrownedGod.prototype.prayTo = humanSacrifice => {
        console.log('*BUBBLE* GURGLE');
    }

    return DrownedGod;
})();

const SevenGods = (() => {
    function SevenGods () {}

    SevenGods.prototype.prayTo = prayerPurpose => {
        console.log('Sorry there are a lot of us, it gets confusing here...');
    }

    return SevenGods;
})();
```

`prayTo` 메서드에 대한 서명이 다른데 다음 의사 코드와 같이 일관된 언터페이스를 구축할 때 문제가 될 수 있다.

```typescript
interface God {
    prayTo () :void;
}
```

클래스와 서명 사이에 가교 역할을 할 수 있는 몇 가지 적응자를 만들어 보자.

```js
const OldGodsAdapter = (() => {
    function OldGodsAdapter () {
        this._oldGods = new OldGods();
    }

    OldGodsAdapter.prototype.prayTo = () => {
        const sacrifice = new Sacrifice();
        this._oldGods.prayTo(sacrifice);
    }

    return OldGodsAdapter;
})();

const DrownedGodAdapter = (() => {
    function DrownedGodAdapter () {
        this._DrownedGod = new DrownedGod();
    }

    DrownedGodAdapter.prototype.prayTo = () => {
        const sacrifice = new HumanSacrifice();
        this._DrownedGod.prayTo(sacrifice);
    }

    return DrownedGodAdapter;
})();

const SevenGodsAdapter = (() => {
    function SevenGodsAdapter () {
        this.prayerPurposeProvider = new PrayerPurposeProvider();
        this._SevenGods = new SevenGods();
    }

    SevenGodsAdapter.prototype.prayTo = () => {
        this._SevenGods.prayTo(this.prayerPurposeProvier.GetPurpose());
    }

    return SevenGodsAdapter;
})();
```

세 개의 서로 다른 인터페이스의 복잡성을 추상화.

```js
const god1 = new SevenGodsAdapter();
const god2 = new DrownedGodAdapter();
const god3 = new OldGodsAdapter();
const gods = [god1, god2, god3];

for(let i = 0; i < gods.length; i++){
    gods[i].prayTo();
}
```

JS에는 `interface`가 없기 때문에 다른 언어에 비해 가교와 적응자 패턴이 동일 하다.

테스트 또한 용이하여, 가짜 가교를 구현해 가교의 호출이 올바르게 동작하는지 확인하면 된다.

## 4.3 복합체

상속은 강한 결합의 한 종류이며, 따라서 상속 대신 복합체를 사용을 권한다.

### 구현

```js
// 리프 노드로 표현될 간단한 재료
function SimpleIngredient(name, calories, ironContent, vitaminCContent){
    this.name = name;
    this.calories = calories;
    this.ironContent = ironContent;
    this.vitaminCContent = vitaminCContent;
}
SimpleIngredient.prototype.getName = () => this.name;
SimpleIngredient.prototype.getCalories = () => this.calories;
SimpleIngredient.prototype.getIronContent = () => this.ironContent;
SimpleIngredient.prototype.getVitaminCContent = () => this.vitaminCContent;

// 복합 재료
function CompoundIngredient(name){
    this.name = name;
    this.ingredients = [];
}
CompoundIngredient.prototype.getName = () => this.name;
CompoundIngredient.prototype.addIngredient = ingredient => {
    this.ingredients.push(ingredient);
}
CompoundIngredient.prototype.getCalories = () => {
    let total = 0;
    for(let i = 0; i < this.ingredients.length; i++){
        total += this.ingredients[i].getCalories();
    }
    return total;
};
CompoundIngredient.prototype.getIronontent = () => {
    let total = 0;
    for(let i = 0; i < this.ingredients.length; i++){
        total += this.ingredients[i].getIronContent();
    }
    return total;
};
CompoundIngredient.prototype.getVitaminCContent = () => {
    let total = 0;
    for(let i = 0; i < this.ingredients.length; i++){
        total += this.ingredients[i].getVitaminCContent();
    }
    return total;
};

const egg = new SimpleIngredient('Egg', 155, 6, 0);
const milk = new SimpleIngredient('Milk', 42, 0, 0);
const sugar = new SimpleIngredient('Sugar', 387, 0, 0);
const rice = new SimpleIngredient('Rice', 370, 8, 0);

const ricePudding = new CompoundIngredient('Rice Pudding');
ricePudding.addIngredient(egg);
ricePudding.addIngredient(milk);
ricePudding.addIngredient(rice);
ricePudding.addIngredient(sugar);

console.log(ricePudding.getCalories() + ' calories');
```

복합체는 트리 구조이기 때문에 HTML을 다루는 JS에서 자주 사용된다.

```js
// 단일 요소든, 집합이든 공통 인터페이스를 제공하여
// 모든 링크가 사라지게 만든다
$('a').hide();
```

## 4.4 장식자

- 기존 클래스를 포장하거나 확장하는데 사용
- 기존 컴포넌트의 서브클래싱의 대안으로 사용
    + 서브클래싱은 일반적으로 컴파일 시 실행
    + 이는 런타임 시에 변경할 수 없음을 의미함
    + 함께 동작할 수 있는 다수의 서브클래싱이 있는 경우, 조합 가능한 서브클래싱의 수는 기하급수적으로 증가
> 서브클래싱 : 기존의 컨트롤의 형태나 동작을 변경하는 기법

---

기사가 착용하는 갑옵은 다양한 구성이 가능하다. 너무 다양해서 명시적 코드를 작성하기 어렵다.

이때 장식자 패턴으로 구현할 수 있다. 적응자/가교 패턴과 유사한 원리로 동작한다.

즉, 다른 인스턴스를 래핑하고 이를 통해 프록시처럼 호출된다.

하지만 장식자 패턴은 인스턴스를 래핑하여 전달함으로써, 런타임 시에 리다이렉션을 수행한다.

일반적으로 장식자는 통로 역할을 하며 일부 내용을 수정하여 전달한다.

하지만 이러한 수정은 파라미터를 변경하거나 래핑된 인스턴스를 호출하기 전에 

추가적인 동작을 수행하는 것에만 한정된다.

**장식자에 의해 변경된 메서드와 단순히 통과되는 메서드에 대한 제어가 가능하게 해준다**

```js
function BasicArmor () {}
BasicArmor.prototype.calculateDamageFromHit = hit => 1;
BasicArmor.prototype.getArmorIntegrity = () => 1;

function ChainMail(decoratedArmor) {
    this.decorateArmor = decoratedArmor;
}
ChainMail.prototype.calculateDamageFromHit = hit => {
    hit.strength = hit.strength * 8;
    return this.decoratedArmor.calculateDamageFromHit(hit);
}
ChainMail.prototype.getArmorIntegrity = () => {
    return .9 * this.decoratedArmor.getArmorIntegrity();
}
```

`ChainMail` 갑옷은 다음의 인터페이스를 준수하는 `armor`의 인스턴스를 취한다.

```typescript
export interface IArmor {
    calculateDamageFromHit (hit: Hit) :number;
    getArmorIntegrity() :number;
}
```

이 인스턴스는 프록시로 래핑돼 있고, 프록시를 통해 호출된다.

- `calculateDamageFromHit` 메서드는 장식된 클래스로 전달된 인수를 수정하
- `getArmorIntegrity` 메서드는 밑에 있는 클래스의 결과를 수정
- 메서드의 긴 체인이 실제로 각 메서드 호출에 의해 호출될 때까지, `ChainMail` 클래스는 여러 겹의 장식자로 장식될 수 있음.(이 동작은 외부의 호출자에게는 보이지 않음)

```js
// usage
const armor = new ChainMail(new BasicArmor());
console.log(armor.calculateDamageFromHit({
    Location: 'head', 
    Weapon: 'Sword', 
    strength: 12
}));
```

**장식자 패턴은 상속이 매우 제한적인 시나리오에 적합**

## 4.5 퍼사드

클래스 집합의 간략화된 인터페이스를 제공하는 적응자 패턴의 특이 케이스

클래스들의 그룹이나 서브시스템 전체의 추상화를 제공하는 데 확장할 수 있다.

### 구현

앞선 `SimpleShip` 클래스를 전체 함대로 확장하면 퍼사드를 사용하는 좋은 예가 된다.

```js
function Ship() {}
Ship.prototype.TurnLeft = () => {}
Ship.prototype.TurnRight = () => {}
Ship.prototype.GoForward = () => {}

function Admiral() {}

function SupplyCoordinator() {}

// 다음으로 퍼사드를 생성
function Fleet() {}
Fleet.prototype.setDestination = destination => {
    // 선박들에 명령을 전달
}
Fleet.prototype.resupply = () => {}
Fleet.prototype.attack = () => {}
```

퍼사드는 특히 API를 다룰 때 매우 유용한 추상화 방법이다.

세분화된 API에 퍼사드를 사용하여, API가 동작하는 추상화의 수준을 상향시켜

어플리케이션의 동작과 동기화를 쉽게 만들 수 있다.

예를 들어 애저 블랍 스토리지 API와 상호작용하는 경우, 개별 파일로 작업하는 대신

파일들의 집합과 작업함으로써 추상화의 수준을 올릴 수 있다.

```js
$.ajax({
    method: 'put',
    url: 'https:/setting.blob.core.window.net/container/set1',
    data: 'setting data 1'
});

$.ajax({
    method: 'put',
    url: 'https:/setting.blob.core.window.net/container/set2',
    data: 'setting data 2'
});

$.ajax({
    method: 'put',
    url: 'https:/setting.blob.core.window.net/container/set3',
    data: 'setting data 3'
});
```

퍼사드는 이러한 호출을 모두 캡슐화한 인터페이스를 제공한다.

```typescript
public interface SettingSaver {
    Save(settings: Settings); // 이전 코드
    Retrieve() : Settings;
}
```

## 4.6 플라이급

인스턴스의 개수는 많지만 각 인스턴스의 변화는 아주 작은 경우 사용.(객체 생성 비용에 따라 많고 적음을 결정)

객체가 너무 고가일 경우 플라이급을 도입하는 것이 유리하다.

플라이급은 각 인스턴스에서 프로토타입의 다른 값들만 관리함으로써, 데이터를 압축할 수 있는 방법을 제공한다.

프로토타입에 가장 일반적인 값을 지정하고 필요에 따라 개별 인스턴스를 오버라이드한다.

### 구현

장군에게 병사는 얼마나 잘 싸우고, 건강하게 잘 먹고 있는가에만 관심이 있다.(개별 병사는 저마다 삶과 꿈이 있다)

```js
// 이런식으로 병사 10000명을 만들면 메모리에 부담이 많이 간다.
function Soldier() {
    this.Health = 10;
    this.FightingAbility = 5;
    this.Hunger = 0;
}

// 프로토타입으로 접근
function Soldier(){}
Soldier.prototype.health = 10;
Soldier.prototype.FightingAbility = 5;
Soldier.prototype.Hunger = 0;
```

개별 설정은 다음과 같다.

```js
const soldier1 = new Soldier();
const soldier2 = new Soldier();

console.log(soldier1.Health) // 10
soldier1.Health = 7

console.log(soldier1.Health) // 7
console.log(soldier2.Health) // 10

delete soldier1.Health;
console.log(soldier1.Health) // 10
// 재정의된 속성을 제거하고 호출하면 부모의 값을 다시 반환
```

## 4.7 프록시

값비싼 객체의 생성 및 사용을 제어할 수 있는 방법 제공

- 프록시는 실제 인스턴스의 인터페이스를 미러링
- 모든 클라이언트의 인스턴스를 대체
- 일반적으로 클래스의 비공개 인스턴스를 래핑

다음처럼 여러곳에서 사용됨. 
- 고가의 객체 지연 인스턴스 생성 _lazy instantiation_
- 비밀 데이터 보호
- 원격 메서드 호출 스터빙 _stubbing_
- 메서드 호출 전후에 추가 작업을 삽입

1. 객체의 인스턴스를 생성하는 비용은 비싸서 실제로 사용되지 않는 경우. 미리 인스턴스를 생성하지 않는 것이 좋다. 이런 경우 프록시 패턴은 내부 인스턴스를 확인하고, 인스턴스가 아직 생성되지 않은 경우 메서드 호출시 이를 생성하여 전달할 수 있다.
2. 클래스가 보안을 염두해 두지 않고 설계 됐더라도, 필요한 경우 프록시를 통해 제공할 수 있다. 호출을 검토하여 보안이 확인된 경우만 메서드 호출을 전달한다.
3. 단순히 어디선가에서 호출되는 메서드의 인터페이스를 제공하는 데 사용될 수 있다.(대다수 웹소켓 라이브러리 함수들이 웹 서버를 호출하고 있는 방법)
4. 메서드 호출에 추가 기능을 삽입할 때 유용하게 사용. 매개변수의 전달이나 매개변수의 유효성 검사, 결과변경이 포함될 수 있다.

### 구현

액체 측정 단위가 다를경우

프록시에서 측정의 클래스를 래핑하여 해결

```js
// 특정 액체를 채우기 위해 필요한 배럴을 예측해주는 배럴 계산기
// 매개변수로 파인트 사용
function BarrelCalculator() {}
BarrelCalculator.prototype.calculateNumberNeeded = volume => Math.ceil(volume357);

// 변환을 다루는 프록시 생성
function DragonBarrelCalculator() {}
DragonBarrelCalculator.prototype.calculateNumberNeeded = voluem => {
    if(this._barrelCaculator == null) {
        this._barrelCalculator = new BarrelCalculator();
    }
    return this._barrelCalculator.calcuateNumberNeeded(volume * .77);
}
```

단위 변환을 수행하여 혼란을 완화시킨다.

## 힌트와 팁

함수의 추상화와 원하는 대로 인터페이스를 변형하는 방법을 제공한다.

하지만 추상화에는 반드시 비용이 발생한다는 점을 명심해야 한다.

- 함수 호출시간이 오래 걸린다.
- 사람들이 더 복잡한 코드를 이해해야 한다.
- 너무 많은 일을 처리하는 퍼사드 패턴을 만들지 않도록 유의한다.