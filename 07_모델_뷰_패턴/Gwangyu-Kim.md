# 7. 모델 뷰 패턴
----------------------
MV 패밀리 : MVC, MVP, MVVP, PAC

----------------------
## 1. MVC 패턴
* 1) 모델
    * 모델은 일반적으로 정보에 대한 컨테이너로 모델링됨. 기능x 단순히 데이터베이스 필드나 검증부를 가림. 메타데이터 필드를 포함하기도 함.
    * 일반적으로 컨트롤러나 뷰에 대해 알지 못함.
```javascript
//기본적인 모델
class LoginModel{
    UserName : string;
    Password : string;
    RememberMe : bool;

    LoginSuccessful : bool;
    LoginErrMessage : string;
}   
```

* 2) 뷰 (View)
    * 모델의 상태가 업데이트 될 때 마다 대상에 모델의 상태를 전달함.
    * 보통 감시자 패턴을 통해 이루어짐.
    * xml,json 등의 형식으로도 통신을 하기 때문에 반드시 그래픽적일 필요는 없음.
    * 모델의 상태는 컨트롤러에 의해 업데이트 됨.

* 3) 컨트롤러(Controller)
    * 모델 뿐 아니라 뷰의 존재도 알고 있음. 둘을 조정함
    * 하나 이상의 뷰를 초기화 할 책임이 있음.
    * 컨트롤러는 CRUD동작을 가짐.
```javascript
//기본 컨트롤러
var LoginController = (function(){
    function LoginController(model) {
        this.model = model;
    }
    LoginController.prototype.Login = function(userName, password, rememberMe) {
        this.model.UserName = userName;
        this.model.Password = password;
        this.model.RememberMe = rememberMe;
        if(this.checkPassword(userName, password)) {
            this.model.LoginSuccessful;
        } else {
            this.model.LoginSuccessful = false;
            this.model.LoginErrMessage = "Incorrect userName or Password";
        }
    };
    return LoginController;
})();
```
* * 누드 객체 패턴(Naked Object Pattern)
* * PAC 패턴(Presentation-Abstraction-Control)
-----------------------------

### MVC 패턴 코드
```javascript
//View
var CreateCastleView = (function () {
    function CreateCastleView(document, controller, model, validationResult) {
        this.document = document;        //문서에 대한 참조
        this.controller = controller;    //컨트롤러에 대한 참조
        this.model = model;
        this.validationResult = validationResult;
        var _this = this;
        this.document.getElementById("saveButton").addEventListener("click", function(){return _this.saveCastle();});
        this.document.getElementById("castleName").value = model.name;
        this.document.getElementById("description").value = model.description;
        this.document.getElementById("outerWallThickness").value = model.outerWallThickness;
        this.document.getElementById("numberOfTowers").value = model.numberOfTowers;
        this.document.getElementById("moat").value = model.moat;
    }
    CreateCastleView.prototype.saveCastle = function() {
        var data = {    //경량의 객체를 구현하고 이를 전달함.
            name : this.document.getElementById("castelName").value,
            description : this.document.getElementById("description").value,
            outerWallThickness : this.document.getElementById("outerWallThickness").value,
            numberOfTowers : this.document.getElementById("numberOfTowers").value,
            moat : this.document.getElementById("moat").value
        };
        this.controller.saveCastle(data);
    }
    return CreateCastleView;
})();

class ValidataionResult{
    public isValid : boolean;
    public Errors : Array<String>;
    public constructor(){
        this.Errors = new Array<String>();
    }
}

//controller
var Controller = (function(){
    function Controller(document){
        this.document = document;
    }
    Controller.prototype.createCatle = function () {
        this.setView(new CreateCastleView(this.document, this));
    };
    Controller.prototype.saveCastle = function (data) {
        var validationResult =  this.validate(date);
        if (validationResult.IsValid) {
            //castle을 저장장치에 저장
            this.saveCastleSuccess(data);
        } else {
            this.setView(new CreateCastleView(this.document, this, data, validationResult));
        }
    };
    Controller.prototype.saveCastleSuccess = function(data) {
        this.setView(new CreateCaslteSuccess(this.document, this, data));   //브라우저의 뷰를 현재 뷰로 설정 지시함
    };
    Controller.prototype.validate = function (model) {  //유효성 검사
        var validationResult = new vaildationResult();
        if(!model.name || model.name === ""){
            validationResult.IsValid = false ;
            validationResult.Errors.push("Name is required");
        }
        return ;
    };
    return Controller;
})();

//model
var Model = (function () {
    function Model(name, description, outerWallThickness, numberOfTowers, moat) {
        this.name = name;
        this.description = description;
        this.outerWallThickness = outerWallThickness;
        this.numberOfTowers = numberOfTowers;
        this.moat = moat;
    }
    return Model;
})();
```

-------------------------------
## 2. MVP 패턴
* MVC와의 차이점
    * 프레젠터(Presenter) : 뷰와 일대일로 매핑된다. > mvc에서 렌더링 할 올바른 뷰를 선택하던 로직이 존재하지 않음을 의미함.
    * 프레젠터는 뷰와 모델을 모두 알고 잇지만, 뷰는 모델을 인식하지 못하고 모델은 뷰를 인식하지 못한다.
    * 양방향 디스패치의 주요 기능을 가지고 있다.
    * MVP의 수동(passive) 버전과 능동(active) 버전

------------------
### MVP패턴 코드
```javascript
var CreateCastleView = (function () {
    function CreateCastleView(document, presenter) {
        this.document = document;
        this.presenter = presenter;
        this.document.getElementById("saveButton").addEventListener("click", this.saveCastle);
    }
    CreateCastleView.prototype.setCastleName = function (name) {
        this.document.getElementById("castleName").value = name;
    };
    CreateCastleView.prototype.getCastleName = function(){
        return this.document.getElementById("castleName").value;
    };
    CreateCastleView.prototype.setDescription = function (description) {
        this.document.getElementById("description").value = description;
    };
    CreateCastleView.prototype.getDesciption = function(){
        return this.document.getElementById("description").value;
    };
    CreateCastleView.prototype.setOuterWallThickness = function (outerWallThickness) {
        this.document.getElementById("outerWallThickness").value = outerWallThickness;
    };
    CreateCastleView.prototype.getOuterWallThickness = function(){
        return this.document.getElementById("outerWallThickness").value;
    };
    CreateCastleView.prototype.setNumberOfTowers = function (numberOfTowers) {
        this.document.getElementById("numberOfTowers").value = numberOfTowers;
    };
    CreateCastleView.prototype.getNumberOfTowers = function(){
        return this.document.getElementById("numberOfTowers").value;
    };
    CreateCastleView.prototype.setMoat = function (moat) {
        this.document.getElementById("moat").value = moat;
    };
    CreateCastleView.prototype.getMoat = function(){
        return this.document.getElementById("moat").value;
    };
    CreateCastleView.prototype.setValid = function (validationResult){
    };
    CreateCastleView.prototype.saveCastle = function () {
        this.presenter.saveCastle();
    };
    return CreateCastleView;
})();
CastleDesign.CreateCastleView = CreateCastleView;


//프레젠터 코드
var CreateCastlePresenter = (function () {
    function CreateCastlePresenter(document) {
        this.document = document;
        this.model = new CreateCastleModel();
        this.view = new CreateCastleView(document, this);
    }
    CreateCastlePresenter.prototype.saveCastle = function () {
        var data = {
            name : this.view.getCastleName(),
            description : this.view.getDesciption(),
            outerWallThickness : this.view.getOuterWallThickness(),
            numberOfTowers : this.view.getNumberOfTowers(),
            moat : this.view.getMoat()
        };

        var validationResult = this.validationResult(data);
        if(validationResult.IsValid) {
            //모델에 write
            this.saveCastleSuccess(data);
        } else {
            this.view.setValid(validationResult);
        }
    };
    CreateCastlePresenter.prototype.saveCastleSuccess = function (data) {
        //다른 프레젠테이션으로 리다이렉션
    };

    CreateCastlePresenter.prototype.validate = function  (model) {
        var validationResult = new validationResult();
        if(!model.name || model.name ===""){
            validationResult.IsValid = false;
            validationResult.Errors.push("Name is required");
        }
        return;
    };
    return CreateCastlePresenter;
})();
CastleDesign.CreateCastlePresenter = CreateCastlePresenter;
```
