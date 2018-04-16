# 6. 함수형 프로그래밍

180416 월 2000 ~ 2200

## 목차

  1. [함수 전달](#)
  1. [필터와 파이프](#)
  1. [어큐뮬레이터](#)
  1. [메모이제이션](#)
  1. [불변성](#)
  1. [지연 인스턴스 생성](#)

## 정리

### [함수 전달](#)
  - 명령 쿼리 분리command query separation => CQRS 패턴 
    - CQRS 패턴에서 update, write 하는 파라미터는 따로 관리하고 조회 하는 데는 DTO 객체를 사용한다 === JAVA VO 객체랑 흡사
  - 이벤트 바인딩 이벤트 콜백이 이 방식
	
### [필터와 파이프](#)
  - 스타일 패턴에 가까움
  - 메서드 체이닝이 가장 흡사한 구현
  - 플루언트 인터페이스 : 원본의 변경을 주지 않고 넘겨주는 방식 
  - No filter cooperation(동시실행): 해당 하는 사례는 없을까??
  - 실 사용 예: RXjs, colleaction 을 다룰 때, 스타일 작업 시

### [어큐뮬레이터](#)
  - reduce와 동일
  - [꼬리 호출 최적화](http://homoefficio.github.io/2015/07/27/%EC%9E%AC%EA%B7%80-%EB%B0%98%EB%B3%B5-Tail-Recursion/)
  - 함수 호출 비용과 Stack 은 같은 말인가???
  - Tail call이란 가장 마지막 위치에서 함수를 실행하는 것
  - 실 사용 예: Tree, composition, DB Self join 구현 시
  
### [메모이제이션](#)
  - 면접 꿀팁: DB에서 인덱스를 쓰는 이유? 메모리를 써서 성능을 최적화 한다.
  - 레코드를 읽는 비용을 줄이고 인덱싱을 해놓는 것도 비용 적은 테이블 수에서는 오히려 단점으로 적용
  
### [불변성](#)
  - call by reference는 변경 가능하기 때문에 불변을 유지 할 수 없음 
  - const, Object.freeze, Object.assign, Object.create(, {writable: false}), Class로 만들어서 변경 불가능하게 막음
  - [use strict](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode)
  - [참고](https://stackoverflow.com/questions/24369328/how-to-use-strict-mode-in-chrome-javascript-console)
  
### [지연 인스턴스 생성](#)

### 참고
- 함수형 프로그래밍를 사용하는 라이브러리 lodash, underscore 등이 있음 
 
