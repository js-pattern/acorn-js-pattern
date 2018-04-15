# 어큐뮬레이터

## 개념

- 집합을 반복하여 하나의 결과를 만들어냄
- 배열 요소의 합산 같은 일반적 작업은 루프 대신 어큐뮬레이터로 구현 가능

## 재귀, 꼬리 호출 최적화

함수형 프로그래밍에서 재귀는 인기있는 방법이며 **꼬리 호출 최적화**를 제공함 - [꼬리호출 관련 링크](http://homoefficio.github.io/2015/07/27/%EC%9E%AC%EA%B7%80-%EB%B0%98%EB%B3%B5-Tail-Recursion/)

- 이를 제공하는 언어는 스택 프레임이 재사용되는 재귀를 사용해 함수의 최적화를 제공
 - 효율적, 대부분의 루프를 쉽게 교체할 수 있음
 - 모든 JS 인터프리터에서 지원되지는 않지만 재귀는 사용 가능

## for 루프의 문제점

**문제** : for 루프를 통한 제어 흐름이 가변적

```js
var result = '';
var multiArray = [[1,2,3], ['a', 'b', 'c']];
for(var i = 0; i  < multiArray.length; i++){
    for(var j = 0; i < multiArray[i].length; j++){
        result += multiArray[i][j];
    }
}

// 두번째 루프의 카운터에 문제가 있다!

var result = '';
var multiArray = [[1,2,3], ['a', 'b', 'c']];
for(var i = 0; i  < multiArray.length; i++){
    for(var j = 0; j < multiArray[i].length; j++){
        result += multiArray[i][j];
    }
}
```

변수 이름을 잘 정하여 문제를 완화시킬 순 있지만, 우린 이를 완전 차단하여야 한다.

웨스테로스의 예제로 돌아가 보자

## 구현

> 전쟁은 많은 비용이 들지만, 농민의 세금과 영주의 재정 지원이 있다.

```js
const peasants = [
    {name: 'Jory', taxesOwed: 11, bankBalance: 50},
    {name: 'Vardis', taxesOwed: 15, bankBalance: 20}
];

TaxCollector.prototype.collect = (items, value, projection) => {
    if (items.length > 1){
        return projection(items[0]) + this.collect(items.slice(1), value, projection);
    }

    return projection(itms[0]);
}
```
위 코드는 항목과 어큐뮬레이터 값, 어큐뮬레이터에 통합되는 값을 투사하는 함수를 인자로 받는다.

```js
// 프로젝션 함수
item => Math.min(item.moneyOwed, item.bankBalance);
```

이 함수를 준비시키기 위해 단순히 배열 및 프로젝션과 함께 어큐뮬레이터의 초기값을 전달해야 한다.

어큐뮬레이터를 통한 각 패스는 배열 크기를 축소시킨다.

이 모든 것이 가변 변수 없이 진행된다.

---

내부 어큐뮬레이션은 문자열 첨부나 추가, 좀 더 복잡한 함수도 될 수 있다.

어큐뮬레이터는 어큐뮬레이터 내부 집합의 값의 변경이 어렵다는 점만 제외하고 방문자 패턴과 유사하다.

**함수형 프로그램은 부작용이 없다**

