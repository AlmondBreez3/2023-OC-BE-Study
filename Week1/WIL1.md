Week1 백엔드 스터디

----1주차 과제----

#1.MVC란?

Model, View,Controller를 나타내는 약자로 SW 아키텍처 디자인 패턴 중 하나이다. 디버깅이나 코드의 가독성을 높이고, 기능 단위별로 나누어서 처리를 한다. 
Model은 데이터베이스와 소통하고 컨트롤러에게 데이터를 전달한다.
View는 유저가 보는 화면을 보여주게 하는 역할이다. 컨트롤러와만 소통한다
Controller
View에서 이벤트 값을 받아 Model에게 데이터를 넘겨준다. 이 과정에서 Model에게 전달할 데이터를 이름 그대로 control할 수 있다


#2.API와 서버란?

API:Application 소프트웨어를 구축하고 통합하기 위한 프로토콜이다. 정보 제공자와 정보 사용자간의 계약으로 볼 수 있다. 예를 들어 날씨 서비스용 API설계에서 사용자는 우편번호를 제공하고 생산자는(최고 기온,최저 기온)으로 구성된 응답으로 답하도록 지정하며 상호작용하는 것을 생각하변 된다.
서버:넓은 의미로는 클라이언트에게 여러가지 서비스를 제공하는 것으로 볼 수 있다.

##API 사용해보기

https://cloud.google.com/appengine/docs/admin-api/trying-the-api?hl=ko
를 들어가서 Admin API 사용해 보기를 해보았습니다

##인증 토큰을 요청하고 response까지 받는 부분:
Request:
POST /token HTTP/1.1
Host: oauth2.googleapis.com
Content-length: 261
content-type: application/x-www-form-urlencoded
user-agent: google-oauth-playground

Response:
HTTP/1.1 200 OK
Content-length: 469
X-xss-protection: 0
X-content-type-options: nosniff
Transfer-encoding: chunked
Expires: Mon, 01 Jan 1990 00:00:00 GMT
Vary: Origin, X-Origin, Referer
Server: scaffolding on HTTPServer2
-content-encoding: gzip
Pragma: no-cache
Cache-control: no-cache, no-store, max-age=0, must-revalidate
Date: Thu, 23 Mar 2023 04:27:48 GMT
X-frame-options: SAMEORIGIN
Alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000
Content-type: application/json; charset=utf-8

여기서 더 나아가 google의 서비스 중 gmail을 골라,
서버에서 mail을 보낼 수 있도록 도와주는 google oauth를 활성화 해볼 수 있는 방법을 공부해보았다.

#3.RESTful이란?

먼저 REST란 HTTP URI를 통해 자원을 명시하고 Method(GET,POST,PUT,DELETE,PATCH등)을 통해 URI에 대한 CRUD Operation을 적용하는 것을 의미한다.
특징
-Uniform
-Stateless
-Cacheable
-self-descriptiveness
-Client-Server Architecture
RESTful이란 이러한 REST의 원리를 따르는 시스템을 의미한다. REST의 원칙을 지키면서 API의 의미를 표현하고 파악하기 쉽게 하는 것이다.

--강의 보면서 필기한 내용--

<프로젝트 생성>
1.spring initializr
dependencies에서 add dependencies눌러서
Spring web, Thymeleaf
다운 받아서 inteli j에서 열기

<라이브러리 살펴보기>
1.build.gradle에서 당장 쓰고 있는 라이브러리는 3개
but! external libraries를 누르면 엄청 많이 다운로드 되어있는 것을 볼 수 있음
2.gradle같은 툴들은 의존관계를 다 관리해줌
3.실무에서는 로그를 써야함, but강의에서는 println사용

<View 환경설정>
1.src/static/index.html에서 view설정해주기
코드:
<!DOCTYPE HTML>
<html>
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content=text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>
그러면 localhost:8080에서 Hello라고 뜬다
2.spring Welcome페이지 가져오기 Spring웹사이트가서 필요한 것을 찾는 능력 기르기!
'static/index.html'올려두면 Welcome Page기능을 제공
3.thymeleaf 템플릿 엔진 존재..필요한거 찾아서 쓰기!
4.hello.hellospring/controller/HelloController만들기
여기서
    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data", "hi!!");
        //resources/templates 폴더안에서 return값 여기서는 'hello'를 찾아 파일 실행을 함 여기선 hello.html을 찾아 렌더링한다
        return "hello";
    }

컨트롤러에서 리턴값으로 문자를 반환하면 뷰 리졸버('viewResolver')가 화면을 찾아서 처리한다
-스프링 부트 템플릿엔진 기본 viewName 매핑
-resources:templates/' +(ViewName)+.html

<빌드하고 실행하기>
1.콘솔로 이동해서 ./gradlew build
2.cd build/libs
3.java -jar hello-spring-0.0.1-SNAPSHOT.jar
4.실행확인

<정적 컨텐츠>
-웹 개발할 때 세가지 방법이 있음 정적컨텐츠, MVC와 템플릿 엔진, API
-정적컨텐츠방식은 파일 그대로 웹에 내려주는것
-MVC와 템플릿 엔진 방식은 서버에서 변형을 해서 웹에 내려주는 것
-JSON형식으로 Client에 내려주는 방식이 API,서버 끼리 통신할때 API방식

1.resources/static/hello-static.html
localhost:8080/hello-static.html
들어가면 그대로 html파일에 나와있는것이 웹에 그대로 반영됨!

<MVC와 템플릿 엔진>
1.MVC:model,view,controller
2.HelloController에다가
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam(value="name") String name, Model model){
        model.addAttribute("name",name);
        return "hello-template";
    }
만들고, 
templates폴더 안에 hello-template.html작성
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello. ' +${name}">hello! empty</p>
</body>
</html>
localhost:8080/hello-mvc하면 에러가 뜬다
Why?
HelloController에서 param을 넘겨줘야되는데 안넘겨줘서 에러가 뜬거임. 에러 안뜨게 하려면
localhost:8080/hello-mvc?name=spring
이런식으로 하면 안뜬다
작동방식
1.param에서 name값(spring)을 가져오면 HelloController에서 attributeName에 이 값이 넣어진다. 이렇게 모델에 담긴 후 html파일로 넘어가 웹에 띄워진다! 
중요한점!model이 (name:spring)!!

<API>
data를 웹에 띄워야 하는 경우에는
 @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name){
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }
    static class Hello{
        private String name;
        public String getName(){
            return name;
        }
        public void setName(String name){
            this.name=name;
        }
    }
이렇게 코드를 짜면,
소스코드를 보면 {'name':'spring'}이런 형식으로 찍힌다
@ResponseBody 사용원리
@ResponseBody가 있으면 응답을 그대로 넘겨야겠다는 방식(HTTP의 BODY에 문자 내용을 직접 반환). 객체가 오면 JSON형식으로 변환해서 나타내야되는게 정책이다.객체 반환이 키워드!!
viewresolver대신에 HttplessageConverter가 동작한다


