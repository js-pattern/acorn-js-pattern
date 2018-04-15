### 부작용이 없는 함수형 함수 
- 함수형 프로그래밍: 함수가 *상태*를 변경하지 않음 
- 명령 쿼리 분리 command query separation pattern 
  - setter 
  - getter 
  
## 함수 전달 
- 자바스립트 일급 함수First class: 함수를 변수에 할당하고 다른 변수와 마찬가지로 전달 할 수 있음
- 보일러 플레이트boiler plate: 일종의 템플릿으로 반복적으로 재사용되는 코드 
- 구현 

 ```js
 exprot class HamiltonianTourOptions {
  onTourStart: Function;
  onEntryToAttration: Function;
  onExitFormAttration: Function;
  onTourCompletion: Function;
 }
 
 var HamiltonianTour = (function() {
  function HamiltonianTour(options) {
    this.options = options;
  }
  
  
  HamiltonianTour.prototype.StartTour = function() {
    if (this.options.onTourStart && typeof this.options.onTourStart === 'function') {
      this.options.onTourStart();
      this.VisitAttraction("King's Landing");
    }
    if (this.options.onTourCompletion && typeof this.options.onTourCompletion === 'function') {
      this.options.onTourCompletion();
    }
  };
  
  HamiltonianTour.prototype.VisitAttraction = function(AttractionName) {
    if (this.options.onEntryToAttraction && typeof this.options.onEntryToAttraction === 'function') {
      this.options.onEntryToAttraction(AttractionName);
    }
  };
  
  
  return HamiltonianTour;
 })();
 
 var tour = new HamiltonianTour({
  onTourStart: function() {},
  onEntryToAttraction: function(cityName) {
    console.log(`I'm delighted to be in ${cityName}`);
  }
});

tour.StartTour();
```
