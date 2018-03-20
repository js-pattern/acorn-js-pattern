# 3. 생성 패턴


## 질문
1.0 추상 팩토리와 빌더의 차이점
- [디자인 패턴 builder](https://www.zerocho.com/category/JavaScript/post/57bab0ee5abe0c17006fe22f)

1.1 빌더 사용시 Director는 필요한가?
- 없어도 무방 직접 build 메소드를 호출해도 된다 (누군가 말씀하신거 같은데..추가 주석 좀)

1.2 AOP 가 뭔가요? (Aspect Oriented Programming)
- 기능을 핵심 비지니스 로직과 공통 모듈(보안 인증, 로깅)로 구분하고, 핵심 로직에 영향을 미치지 않고 공통 모듈을 효과적으로 끼워넣기 위한 개발 방법
- [자바스크립트 AOP 맛보기(http://meetup.toast.com/posts/109)

1.3  DB connect 시 Singleton Instance 대신 Module pattern 식으로 반환하면 안되는건가?
- 초기화된 설정을 외부에서 변경 가능하지 못하게 만든다면 어떤 패턴을 사용해도 상관없을 것 같음(김병진)
- 객체 리터럴로 만든 단순한 객체 또한 싱글톤의 예이다, 동일한 객체에 대한 새로운 포인터만 반환하는 것이면 된다(참고 자료 - 자바스크립트 패턴 7장 디자인 패턴)
