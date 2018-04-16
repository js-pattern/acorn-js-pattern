# 6장 함수형 프로그래밍

### 요약  
 - 자바스크립트는 함수평 프로그래밍 언어는 아니다. 하지만 함수형 프로그램을 적용할수 없음을 의미하지는 않음.
 - 함수형 프로그램의 접근 방법은 깔끔하고, 디버그 하기 쉬운 코드의 작성이 가능하게 해줌.
----------------------
## 목차
1. [함수 전달]             - 행동패턴 & 구조패턴
2. [필터와 파이프]         - 스타일 패턴(GoF 범주 외)
3. [어큐물레이터]          - 행동패턴
4. [메모이제이션]          - 생성패턴
5. [불변성]                - 생성패턴
6. [지연 인스턴스 생성]     - 생성패턴

----------------------


------------------------

### 함수형 프로그래밍(Functional Programming)
 자료 처리를 수학적 함수의 계산으로 취급하고, 상태와 가변 데이터를 멀리하는 프로그래밍 패러다임의 하나. 함수의 응용을 강조하는 프로그래밍 기법.
 - 객체지향 접근 방식과는 다른 개발 방법

### 객체지향 프로그래밍의 문제점
 1) 병렬 프로그래밍이 어려움 ( 상태가 여러 다른 스레드에서 변경될 수 있으며 예측할 수 없는 부작용이 발생할 수 있기 때문. )
    -함수형 프로그래밍은 상태변화와 가변 변수(mutable variable)를 허용하지 않음

### 부작용이 없는 함수형 함수
  1) 함수형 프로그램의 핵심은 함수가 상태를 변경하지 않는다는 점.
  - 함수의 내부값은 설정할 수 있지만 함수 외부의 어떤 값도 변경하지 못한다. > 코드를 유지 관리하는데 아주 유용함
  - 어플리케이션의 코드를 함수로 분리하는 것이 바람직 함
  - ex. 명령 쿼리 분리 (command query separation) : 메소드를 두가지 범주(읽는 함수, 값을 설정하는 함수)로 분류하도록 제안.
  2) 동일한 입력값으로 몇 번이고 호출 되어도 실행속도 외에 다른 부작용이 발생하지 않음.
  ----------------------------------------------
## 1.  [함수 전달]
 - 자바스크립트는 기본적으로 함수형 프로그램으로 볼 수 있음 ( 함수를 변수에 할당하고 다른변수와 마찬가지로 전달 할 수 있으니깐.)
 - * 단일 스레드 특성으로 인하여 콜백 사용은 일반적이고 모든곳에 다 사용됨.
 - 자바스크립트 환경에 관계없이 콜백 사용 회피는 불가능으로 보임

```javascript
//1) 콜백   기본 자바스크립트에서 코드
var xmlhttp = new XMLHttpRequest();
xmlhttp.onreadystatechange = function(){
    if(xmlhttp.readyState == 4 && xmlhttp.status == 200){
        //반환된 데이터를 처리
    }    
};
xmlhttp.open("GET",http://some.external.resource, true);
xmlhttp.send(); //요청 보내기 전에 onreadystatechange를 할당함 ( 경합조건 race condition 때문에)
//2) 콜백 변경 - 함수는 퍼스트 클래스다!
var xmlhttp;
function requestData(){
    xmlhttp = new XMLHttpRequest();
    xml.onreadystatechange =processData();
    xml.open("GET", http://some.external.resource, true);
    xmlhttp.send();
}
function processData(){
    if(xml.readyState == 4 && xmlhttp.status == 200){
        //반환 데이터 처리
    }
}
//3-1) JQUery 방식
$.getJSON("http://some.external.resource", function(json)){
    //반환된 데이터  처리
});
//3-2) Jquery 방식 일 때 ,준비 상태 변화를 처리하는 보일러 플레이트(boiler plate)  사용 ( 보일러플레이트 : 템플릿 코드)
$.ajax("http://some.external.resource",
    {sucess : function(json){
        //반환 처리
    },
    error : function(json){
        //에러처리
    },
    dataType : 'json'
    //성공과 실패에 대한 콜백
});
```

### 구현
```javascript
export class HamiltonianTourOptions{
    onTourStart : Function;
    onEntryToAttraction : Function;
    onExitFromAttraction : Function;
    onTourCompletion : Function;
}

var HamiltonianTour = (function(){
    function HamiltonianTour(options) {
        this.options = options;
    }
    HamiltonianTour.prototype.StartTour = function (){
        if(this.options.onTourStart && typeof (this.options.onTourStart) === "function")
            this.options.onStart();
            this.VisitAttraction("King's Landing");
            this.VisitAttraction("WinterFell");
            this.VisitAttraction("Mountains of Dorne");
            this.VisitAttraction("Eyrie");

        if(thos.options.onTourCompletion && typeof(this.options.onTourCompletion) ==="function")
            this.options.onTourCompletion();
    };

    HamiltonianTour.prototype.VisitAttraction = function(AttractionName){
        if(this.options.onEntryToAttraction && typeof (this.options.onEntryToAttraction) ==="function")
            this.options.onEntryToAttraction(AttractionName);

        //관광명소에서 하는 일
        if(this.options.onExitFromAttraction&&typeof(this.options.onExitFromAttraction) ==="function")
            this.options.onExitFromAttraction(AttractionName);
    }
    return HamiltonianTour;
})();

//2) 간단 구현
var tour = new HamiltonianTour({
    onEntryToAttraction: function(cityname) { console.log("I'm delighted to be in " + cityname)}});
tour.StartTour();

```
----------------------
## 2.[필터와 파이프]
 파이프(|) : A의 출력을 B이 입력으로 사용하는 것.

  * 함수형 프로그래밍 언어에서는 함수들 사이에 제공하는 특별한 구문을 제공함.
  - ex ) [1..10]|>List.filter ( fun n -> n%2 = 0);;

  * 필터&파이프는 함수들의 긴 연쇄에서 아주 유용함.  
  - ex ) 숫자를 선택해 부동수(float number)로 변화후 제곱후 반올림함.
  - 10.5 |> float |> Math.Sqrt|> Math.Round

 자바스크립트의 메소드 체인을 사용하여 유사한 기능 제공이 가능함.

### 구현
```javascript
//배열의 구성원에 대해 일치하는지 검토하고 결과를 반환하는 함수 제공
Array.prototype.where = function (inclusionTest) {
    var results = [];
    for (var i = 0; i<this.length; i++) {
        if(inclusionTest(this[i]))  results.push(this[i]);
    }
    return results;
};

var items = [1,2,3,4,5,6,7,8,9,10];
items.where(function(things){ return things % 2 == 0});
//메소드 체이닝을 사용하여  
items.where(function(things){ return things % 2 == 0}).where(function(things){ return things % 3 == 0;});
```

플루언스 인터페이스(fluent interface) : 원래의 객체를 변경하지 않고 워본 객체의 수정된 버전을 리턴하는 방법.

```javascript
Array.prototype.select = function(projection) {
    var results = [];
    for(var i =0; i<this.length; i ++){
        results.push(projection(this[i]));
    }
    return results;
};
// 임의의 프로젝션 함수를 기반으로 월내 항목의 예측이 가능하게 함. 배열에 플루언트 확장을 사용.
```

```javascript
var children = [{ id: 1, Name : "Rob"},
{ id: 2, Name : "Sansa"},
{ id: 3, Name : "Arya"},
{ id: 4, Name : "Brandon"},
{ id: 5, Name : "Rickon"};
var filteredChildren = children.where(function (x) { return x.id % 2 ==0;}).select(function(x) { return x.Name;});
];
```

* *함수들의 체인을 연결하면 이해하기도 쉬울 뿐 아니라 구현도 쉬워짐. 코드 간결*

```javascript
var children = [{ id: 1, Name : "Rob"},
{ id: 2, Name : "Sansa"},
{ id: 3, Name : "Arya"},
{ id: 4, Name : "Brandon"},
{ id: 5, Name : "Rickon"};
var evenIds = [];
for(var i = 0; i<children.length; i++) {
    if(children[i].id%2 == 0)
        evenIds.push(children[i]);
}
var names = [];
for(var i= 0; i< evenIds.length; i++){
    names.push(evenIds[i].name);
}
];
```
>advantage
Simplicity – Allows designer to understand overall input/output behavior of a system in terms of individual filters.
Maintenance and reuse
Concurrent Execution –Each filter can be implemented as a separate task and be executed in parallel with other filters.

>disAdvantage
Interactive transformations are difficult – Filters being independent entities designer has to think of each filter as providing a complete transformation of input data to output data.
No filter cooperation.
Performance – may force a lowest common denominator on data transmission
[https://www.coursehero.com/file/p3ercbp/Pipe-and-Filter-Style-Advantages-and-Disadvantages-Advantages-Simplicity-Allows/]
-------------------------

## 3.[어큐물레이터]
* 집합을 반복하여 하나의 결과를 만들어 냄
- 재귀(Recursion)
- 호출최적화(tail call optimization)
- 방문자 패턴과 비슷하다. ( 내부 집합의 값의 변경이 어렵다는 점을 제외하고 )

-----------------

## 4.[메모이제이션]
 * 함수에서 계산된 이진 값들을 저장하는데 사용하는 특정한 용어.

### 구현

```javascript

var Fibonacci = (function (){
    function Fibonacci(){
    }
    Fibonacci.prototype.NaieveFib = function (n) {
        if (n == 0 )    return 0;
        if (n <= 2 )    return 1;
        return this.NaieveFib(n-1) + this.NaieveFib(n-2);
    };
    return Fibonacci;
})();       //적은 수에선 빨리 동작해서 문제가 안되지만, 큰 수에서는 급격히 느려지는 코드. 향상방안 찾기

//향상방안

var Fibonacci = (function (){
    function Fibonacci(){
        this.memoizedValues = [];
    }
    Fibonacci.prototype.MementoFib = function(n) {
        if(n == 0 )     return 0;
        if(n <= 2 )     return 1;
        if(!this.memoizedValues[n])
            this.memoizedValues[n] = this.memoizedValues(n-1) + this.memoizedValues(n-2);
            return this.memoizedValues[n];
    };
    return Fibonacci;
})();
```

메모이제이션이 가장 좋은 효과를 발휘할 때의 좋은 예는 함수의 다음 호출이 동일한 매개변수로 진행될 때.

---------------
## 4.[불변성]
- 기존 ES5까지는 불변성을 지웧나지 않았지만, ES6이후 const 키워드로 불변성 제공.
- 1) Object.freeze 기능을 사용하기도 함.
```javascript
var consts = Object.freeze({pi : 3.14});
consts.pi = 7;
console.log(consts.pi);     //result  = 3.14

```
- 2) 위의 코드는 사용자 친화적이지 않음 > 오류&실패를 알려주지 않음.   >>스트릭트모드를 활성시켜 엄격한 파싱모드를 사용할 수 있음

```javascript
"use strict";
var consts = Object.freeze({pi : 3.141});
consts.pi =7;       //예외가 발생함.
```
- 3) Object.create 구문을 사용하여 객체의 속성을 writable : false를 지정할 수 있음.
```javascript
var t= Object.create(Object.prototype,
        {value : {
                writable : false,
                value : 10
                }
        };
    t.value = 7;
    console.log(t.value)        //result 10;
});
```

-----------------
## 5. [지연 인스턴스 생성]
 - 지연인스턴스 생성 ( lazy Instantiation )
 - 지연 초기화 ( lazy initialization )

 ```javascript
//bread 객체는 생성시 비용이 많이 들어 주문이 들어오기 전까지 생성을 지연하고 싶음.
var Bread = (function () {
    function Bread(breadType) {
        this.breadType = breadType;
        //작업들.....
        console.log("Bread" + breadType + "create.");
    }
    return Bread;
})();


var Bakery = (function () {
    function Bakery() {
        this.requiredBreads = [];
    }
    Bakery.prototype.orderBreadType = function(breadType) {
        this.requiredBreads.push(breadType);
    };

    Bakery.prototype.pickUpBread = function(breadType) {
        console.log("pickup of bread" + breadType + " requested");
        if(!this.breads) {
            this.createBreads();
        }
        for (var i=0; this.breads.length ; i ++) {
            if(this.breads[i].breadType == breadType)   return this.breads[i];
        }
    };
    Bakery.prototype.createBreads = function(){
        this.breads = [];
        for (var i = 0; i< this.requiredBreads.length; i++) {
            this.breads.push(new Bread(this.requiredBreads[i]));
        }       //생성이 pickup 할대로 미뤄짐 > 지연생성 인스턴스
    };
});


 ```
