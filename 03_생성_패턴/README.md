# 3. 생성 패턴


## 질문
1.0 추상 팩토리와 빌더의 차이점
- [디자인 패턴 builder](https://www.zerocho.com/category/JavaScript/post/57bab0ee5abe0c17006fe22f)

1.1 Builder Pattern 사용시 Director는 필요한가?
- (https://github.com/js-pattern/acorn-js-pattern/blob/master/03_%EC%83%9D%EC%84%B1_%ED%8C%A8%ED%84%B4/y0c.md#%EB%B9%8C%EB%8D%94-%ED%8C%A8%ED%84%B4-%EB%8B%A4%EB%A5%B8-%EC%98%88%EC%A0%9C) 에서 Director 는 보이지 않음.
- [Builder design pattern  why do we need a Director  - Stack Overflow](https://stackoverflow.com/questions/4313172/builder-design-pattern-why-do-we-need-a-director)
  > The core portion of the Builder pattern concerns the Abstract Builder and its subclasses (concrete builders). According to GoF's Design Patterns, director simply "notifies the builder whenever a part of the product should be built", which can be perfectly done by the client.

  > The StringBuilder class in the Java API is an example of a builder without the respective director -- typically the client class "directs" it.

  > Also, in Effective Java and Creating and Destroying Java Objects, Joshua Bloch suggests the use of the builder pattern, and he does not include a director.


1.2 AOP 가 뭔가요? (Aspect Oriented Programming)
- 기능을 핵심 비지니스 로직과 공통 모듈(보안 인증, 로깅)로 구분하고, 핵심 로직에 영향을 미치지 않고 공통 모듈을 효과적으로 끼워넣기 위한 개발 방법
- [자바스크립트 AOP 맛보기](http://meetup.toast.com/posts/109)

1.3  DB connect 시 Singleton Instance 대신 Module pattern 식으로 반환하면 안되는건가?
- 초기화된 설정을 외부에서 변경 가능하지 못하게 만든다면 어떤 패턴을 사용해도 상관없을 것 같음(김병진)
- 객체 리터럴로 만든 단순한 객체 또한 싱글톤의 예이다, 동일한 객체에 대한 새로운 포인터만 반환하는 것이면 된다(참고 자료 - 자바스크립트 패턴 7장 디자인 패턴)
