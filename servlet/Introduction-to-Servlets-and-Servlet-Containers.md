# Introduction-to-Servlets-and-Servlet-Containers 
[Introduction to Servlets and Servlet Containers | Baeldung](https://www.baeldung.com/java-servlets-containers-intro)


### 1. Overview 

이번 튜토리얼에서 서블릿과 서블릿 컨테이너의 개념과 어떻게 동작하는 지를 공부해보겠습니다. 이번 토픽에서 중요한 키워드는 `request`, `response`, `session objects` , `shared variables` , `multithreading`  입니다. 위 키워드를 통해서 서블릿과 서블릿 컨테이너가 무엇인지 한 번 알아봐볼까요? 

### 2. What Are Servlets and Their Containers

서블릿은 웹 개발을 위한 Java EE의 컴포넌트이고, 서블릿 컨테이너 내에서 실행되는 자바 프로그램으로 볼 수 있는데, 주로 클라이언트의 요청을 받아서 해당 요청을 처리하고 응답을 다시 클라이언트에 전송하는 역할을 수행합니다. 

서블릿을 사용하기 위해서는 먼저 서블릿 컨테이너에 서블릿이 등록돼야 하고 이를 위해 서블릿 인터페이스 `init()` 메소드를 통해서 초기화를 합니다. 

서블릿 초기화가 완료되면, 클라이언트의 요청을 처리할 수 있는 상태가 됩니다. 서블릿 컨테이너가 클라이언트의 요청을 처리할 서블릿을 찾은 뒤 서블릿의 `service()` 메소드에서 처리될 수 있도록 합니다. `service()` 메소드에서는 HTTP Request Method Type (GET, POST 등)에 대응되는 doGet(), doPost() 등의 메소드에 요청 처리를 위임하게 됩니다. 
