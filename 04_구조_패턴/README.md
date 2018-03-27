# 4. 구조 패턴



## 질문

  1. Adapter
     - [goohooh.md](goohooh.md)
     - "적응자가 너무 복잡해 지지 않도록 주의를 요한다." 어떤 경우일까?
        - 맞추려는 인터페이스에 따라 코드량이 많아 지는 것이 아닐까.
        - 단일 책임 원칙에 위해되는 정도로 복잡성이 증가함.
        - 안드로이드 이벤트 리스너에서 패턴을 본 적 있음.
        - 
  1. Bridge
     - [goohooh.md](goohooh.md)
     - [브리지 패턴 - 위키백과, 우리 모두의 백과사전.html](https://ko.wikipedia.org/wiki/%EB%B8%8C%EB%A6%AC%EC%A7%80_%ED%8C%A8%ED%84%B4) BridgePattern 예제가 더 이해하는 데 좋아 보임.
     - JDBC(Oracle, MySQL) 도 이런식으로 구현 되어 있을 것 같음.
       - 하나의 인터페이스를 각각 구현.
  1. Composite
     - [Gwangyu-Kim.md](Gwangyu-Kim.md)
     - (판서)
     - specialize
     - View(HTML, MFC, Swing) 영역에서 상위 컴포넌트들을 구현 하는 것도 같은 방식으로 보임.
     - 공통 인터페이스를 구현하는 것이 필수적임.
     - 다른 예제) 파일 탐색기
  1. Decorator
     - [y0c.md](y0c.md)
     - AOP
     - 빌더 패턴과 비슷한 면도 보임.
     - Subclassing 을 대체할 수 있음.
     - OCP 에 위배되지 않도록 확장 할 수 있음.
     - JAVA Stream 
       - [JAVA 정리 - File Input_Output _ 감성 프로그래밍.html](http://programmingsummaries.tistory.com/64)
     - 실제 활용되는 곳
        - [y0c.md](y0c.md)
     - 디버깅을 하기 어렵다는 단점이 있음.
       - 데코레이터를 2개 이상 사용하지 말라고 제약을 두는 곳도 있음.
     - HOC(higher-order components)
       - [https://github.com/acdlite/recompose](https://github.com/acdlite/recompose)
       - [React + Decorator + HOC = Fantastic!!.html](http://blog-kr.zoyi.co/react-hoc/)
  1. Facade
     - [jjingK.md](jjingK.md)
     - Jquery.ajax ? (.post() : .get())
     - 뜻: 외관
  1. Flyweight
     - [jjingK.md](jjingK.md)
     - javascript prototype 자체가 이 패턴을 구현한 것으로 보임.
     - ChartJS 에서 기본값 셋팅이 되어있는 것을 보았음.
  1. Proxy
     -[https://en.wikipedia.org/wiki/Proxy_pattern](https://en.wikipedia.org/wiki/Proxy_pattern)
     - 디버깅을 하기 어렵다는 단점이 있음.
     - Proxy server
  1. Questions
     - Decorator vs Proxy
        - [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
