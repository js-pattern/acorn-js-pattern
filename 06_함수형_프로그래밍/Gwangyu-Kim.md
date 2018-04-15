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
var xmlhttp = new XMLHttpRequest()
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
$.getJSON('http://some.external.resource', function(json)){
    //반환된 데이터  처리
}
//3-2) Jquery 방식 일 때 ,준비 상태 변화를 처리하는 보일러 플레이트(boiler plate)  사용 ( 보일러플레이트 : 템플릿 코드)
$.ajax('http://some.external.resource',
    {sucess : function(json){
    //반환 처리
    },
    error : function(json){
        //에러처리
    },
    dataType : 'json'
});     //성공과 실패에 대한 콜백

`````````````````````````````````````````````````````````ㄴㅁㅇㅁㄴㅇㅁㄴㅇㅁㄴㅇ
-----------------------------------------
