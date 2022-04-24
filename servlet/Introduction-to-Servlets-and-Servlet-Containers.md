# Introduction-to-Servlets-and-Servlet-Containers 
[Introduction to Servlets and Servlet Containers | Baeldung](https://www.baeldung.com/java-servlets-containers-intro)


### 1. Overview 

이번 튜토리얼에서 서블릿과 서블릿 컨테이너의 개념과 어떻게 동작하는 지를 공부해보겠습니다. 이번 토픽에서 중요한 키워드는 `request`, `response`, `session objects` , `shared variables` , `multithreading`  입니다. 위 키워드를 통해서 서블릿과 서블릿 컨테이너가 무엇인지 한 번 알아봐볼까요? 

### 2. What Are Servlets and Their Containers

서블릿은 웹 개발을 위한 Java EE의 컴포넌트이고, 서블릿 컨테이너 내에서 실행되는 자바 프로그램으로 볼 수 있는데, 주로 클라이언트의 요청을 받아서 해당 요청을 처리하고 응답을 다시 클라이언트에 전송하는 역할을 수행합니다. 

서블릿을 사용하기 위해서는 먼저 서블릿 컨테이너에 서블릿이 등록돼야 하고 이를 위해 서블릿 인터페이스 `init()` 메소드를 통해서 초기화를 합니다. 

서블릿 초기화가 완료되면, 클라이언트의 요청을 처리할 수 있는 상태가 됩니다. 서블릿 컨테이너가 클라이언트의 요청을 처리할 서블릿을 찾은 뒤 서블릿의 `service()` 메소드에서 처리될 수 있도록 합니다. `service()` 메소드에서는 HTTP Request Method Type (GET, POST 등)에 대응되는 doGet(), doPost() 등의 메소드에 요청 처리를 위임하게 됩니다.

`destory()` 메소드를 통해서 서블릿 컨테이너가 대상 서블릿을 관리 대상에서 제거할 수 있고 이 시점부터는 더 이상 클라이언트의 HTTP 요청을 처리할 수 없게 됩니다. `init() -  service() - destory()`  순서로 서블릿의 생명주기를 설명할 수 있습니다. 

자, 이제 서블릿 컨테이너 관점에서 위에서 말한 서블릿의 생명주기를 다뤄보겠습니다. 참고로 서블릿 컨테이너의 예로는  [Apache Tomcat](https://www.baeldung.com/tomcat)  or  [Jetty](https://www.baeldung.com/deploy-to-jetty)  등이 있습니다. 서블릿 컨테이너가 생성되면,  [서블릿 컨텍스트(ServletContext)](https://www.baeldung.com/context-servlet-initialization-param)  객체를 생성합니다. 서블릿 컨텍스트는 마치 서버 또는 서블릿 컨테이너의 메모리 역할을 하는데, 웹 애플리케이션과 관련된 서블릿, 서블릿 필터, 리스너 등을 모두 저장 및  관리합니다. 이 때 저장할 데이터에 대한 정보는 `web.xml` 또는 `annotation` 방식을 통해 정의할 수 있습니다. 서블릿 컨텍스트는 서블릿 컨테이너의 생성 ~ 소멸까지의 생명주기에 종속되어 있습니다. 

기본적으로 서블릿 컨테이너는 서블릿 초기화를 진행하는 시점은 해당 서블릿에 대한 첫 클라이언트의 HTTP 요청입니다. 하지만 `load-on-statup` 파라미터 값을 통해서 서블릿 컨테이너의 생성 시점에 서블릿 초기화를 진행할 수 있습니다. 

### 3. Request, Response, and Session

클라이언트 - 서버 애플리케이션에서 가장 핵심이되는 것은 클라이언트의 요청과 서버의 요청 처리 및 응답입니다. 서블릿이 위 역할을 어떻게 수행하는 지 알아보겠습니다. 

 
