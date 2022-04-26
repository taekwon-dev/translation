# Introduction-to-Servlets-and-Servlet-Containers
[Introduction to Servlets and Servlet Containers | Baeldung](https://www.baeldung.com/java-servlets-containers-intro)

### 1. Overview

이번 튜토리얼에서 서블릿과 서블릿 컨테이너의 개념과 어떻게 동작하는 지를 공부해보겠습니다. 이번 토픽에서 중요한 키워드는 `request`, `response`, `session objects` , `shared variables` , `multithreading`  입니다. 위 키워드를 통해서 서블릿과 서블릿 컨테이너가 무엇인지 한 번 알아봐볼까요?

### 2. What Are Servlets and Their Containers

서블릿은 웹 개발을 위한 Java EE의 컴포넌트이고, 서블릿 컨테이너 내에서 실행되는 자바 프로그램으로 볼 수 있는데, 주로 클라이언트의 요청을 받아서 해당 요청을 처리하고 응답을 다시 클라이언트에 전송하는 역할을 수행한다.

서블릿을 사용하기 위해서는 먼저 서블릿 컨테이너에 서블릿이 등록돼야 하고 이를 위해 서블릿 인터페이스 `init()` 메소드를 통해서 초기화 한다.

서블릿 초기화가 완료되면, 클라이언트의 요청을 처리할 수 있는 상태가 된다. 서블릿 컨테이너가 클라이언트의 요청을 처리할 서블릿을 찾은 뒤 서블릿의 `service()` 메소드에서 처리될 수 있도록 한다. `service()` 메소드에서는 HTTP Request Method Type (GET, POST 등)에 대응되는 doGet(), doPost() 등의 메소드에 요청 처리를 위임한다.

`destory()` 메소드를 통해서 서블릿 컨테이너가 대상 서블릿을 관리 대상에서 제거할 수 있고 이 시점부터는 더 이상 클라이언트의 HTTP 요청을 처리할 수 없게 된다. `init() -  service() - destory()`  순서로 서블릿의 생명주기를 설명할 수 있다.

자, 이제 서블릿 컨테이너 관점에서 위에서 말한 서블릿의 생명주기를 보자.  참고로 서블릿 컨테이너의 예로는  [Apache Tomcat](https://www.baeldung.com/tomcat)  or  [Jetty](https://www.baeldung.com/deploy-to-jetty)  등이 있다. 서블릿 컨테이너가 생성되면,  [서블릿 컨텍스트(ServletContext)](https://www.baeldung.com/context-servlet-initialization-param)  객체를 생성한다.서블릿 컨텍스트는 마치 서버 또는 서블릿 컨테이너의 메모리 역할을 하는데, 웹 애플리케이션과 관련된 서블릿, 서블릿 필터, 리스너 등을 모두 저장 및  관리합니다. 이 때 저장할 데이터에 대한 정보는 `web.xml` 또는 `annotation` 방식을 통해 정의할 수 있다. 서블릿 컨텍스트는 서블릿 컨테이너의 생성 ~ 소멸까지의 생명주기에 종속되어 있다.

기본적으로 서블릿 컨테이너는 서블릿 초기화를 진행하는 시점은 해당 서블릿에 대한 첫 클라이언트의 HTTP 요청이다. 하지만 `load-on-statup` 파라미터 값을 통해서 서블릿 컨테이너의 생성 시점에 서블릿 초기화를 진행할 수 있다.

### 3. Request, Response, and Session

클라이언트 - 서버 애플리케이션에서 가장 핵심이되는 것은 클라이언트의 요청과 서버의 요청 처리 및 응답입니다. 서블릿이 위 역할을 어떻게 수행하는 지 알아보자.

웹 브라우저 또는 `curl` 명령어를 통한 클라이언트가 서버에 요청을 보낼 때 마다 서블릿 컨테이너는 `HttpServletRequest`, `HttpServletResponse` 객체를 생성한다. 그리고 이 객체들을 해당 요청을 처리할 서블릿의 `service()` 메소드로 전달한다. `HttpServletRequest`  인터페이스 내에서 정의된 `getMethod()` 를 통해서 `GET` 또는 `POST`  등 HTTP 요청의 메소드를 확인할 수 있고, 이를 통해서 서블릿 내에서 어떤 메소드를 통해서 처리할 지 결정할 수 있다.

HTTP method 뿐만 아니라, `HttpServletRequest` 객체에는 요청 헤더, 파라미터 그리고 요청 바디 정보도 갖고 있다. 이와 같은 맥락으로 `HttpServletResponse` 객체 역시 응답 헤더, 파라미터, 그리고 응답 바디 정보를 포함하고 있다. 그리고 이러한 정보들을 각 서블릿의 doXXX() 메소드에서 설정 및 조작할 수 있다.

서블릿 컨테이너에 의해 생성된 두 객체 `HttpServletRequest` , `HttpServletResponse`는 클라이언트에 응답을 준 뒤 소멸된다. 앞서 위에서 서블릿의 생명주기에서 `destroy()` 메소드가 있었는데, 이 메소드가 호출되면서 JVM의 가비지 컬렉터에 의해 처리된다.

매 요청 마다 서블릿 컨테이너가 두 객체를 생성하고, 응답 한 뒤 소멸되면 클라이언트의 상태를 유지할 수 없을 것이다.  `HttpSession`을 사용하면 클라이언트의 상태를 유지해서 연속적인 클라이언트의 요청을 처리할 수 있다.

`HttpSession`은 기본적으로 유저 정보와 연결하여, 여러 번의 요청 간에 특정 유저의 정보를 유지한 채 처리할 수 있다. 이 경우 일반적으로 `쿠키` 에 `JSESSIONID` 키 값에 식별 값을 둔다. 각 세션의 유효 기간은 `web.xml`에서 설정할 수 있다.

```
<session-config>
	<session-timeout>10	</session-timeout> // 분 단위
</session-config>
```

위에서 정의한 세션이 만료된 경우 세션이 `idle` 상태가 되어 서버에서 곧 제거되고, 제거된 이후에 또 다른 요청에 의해 새로운 세션이 생성될 수 있다.


### 4. How do  Servlets Share Data

서블릿 내에서 데이터를 공유하는 방법이 다양하게 있는데, `Scope` 에 영향을 받는다.

앞서 위에서 `HttpServletRequest`, `HttpServletResponse` 가 생성 - 소멸되는 생명주기를 갖는 것과 `HttpSession`이 `web.xml`에서 정의한 시간(분 단위) 내에서만 유효한 것을 알 수 있었다. 이를 통해서 각각의 객체가 서로 다른 생명주기를 갖는 다는 것을 알 수 있는데, 이를 각각의 `Scope`를 갖는다고 표현할 수 있다.

`ServletContext`의 생명주기가 가장 길다. 웹 애플리케이션이 구동되고 종료되기 전까지의 상대적으로 긴 생명주기를 갖는다. 서블릿, 서블릿 필터, 그리고 리스너 객체는 `ServletContext`에서 관리되기 때문에, 같은 생명주기를 갖는다.

우리 웹 사이트에 방문한 유저 수를 계산하고 싶은 경우 웹 애플리케이션 컴포넌트 중 어디에 `방문한 유저 수` 변수를 선언해야 할까?  이 상황에서는 서블릿 컨테이너에 속한 모든 서블릿에서 위 변수에 접근할 수 있어야 하고, 결과적으로 `ServletContext`에서 변수를 관리해서 요구사항을 처리할 수 있을 것이다.

반면, 우리 웹 사이트에 로그인한 유저의 정보는 `HttpSession`을 통해서 관리하면 좋을 것이다. (Session scope) 마지막으로 매 요청 시마다 서버에 전달되는 정보(헤더, 파라미터, 바디 등)은 서블릿 컨테이너가 생성한 `HttpServletRequest`가 생성 - 소멸되는 과정에서 관리된다.  (Request Scope)

### 5. Handling Multithreading

서블릿 컨테이너는 클라이언트의 요청에 대해서 처리할 스레드를 생성하고, `HttpServletRequset`, `HttpServletResponse` 객체를 해당 요청을 처리할 서블릿의 `service()` 메소드에 전달한다.

만약 여러 개의 요청이 오는 상황을 가정하면, 여러 개의 스레드에서 동시에 같은 서블릿을 참조할 수 있게 된다. 이는 곧 `thread-safety` 을 고려해야 하고, `Session Scope`, `Request Scope`  데이터가 서블릿에서 관리되지 않도록 하는 것이 중요하다. 즉, 서블릿이 상태를 가지면 `thread-safety`하지 않을 수 있다.

```
public class ExampleThree extends HttpServlet {
    
    private String instanceMessage; 

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
      throws ServletException, IOException {
        String message = request.getParameter("message");
        instanceMessage = request.getParameter("message");
        request.setAttribute("text", message);
        request.setAttribute("unsafeText", instanceMessage);
        request.getRequestDispatcher("/jsp/ExampleThree.jsp").forward(request, response);
    }
}
```

`ExampleThree` 클래스의 필드인 `instanceMessage`는 세션 유효 기간 내 모든 요청 간 공유되는 변수다. 반면, `doGet()` 메소드 내에서 정의된 지역변수인 `message`는 각 요청마다 값이 바뀔 수 있고 따라서 이 경우 `instanceMessage`는 매 요청 마다 값이 바뀔 수 있으므로 일관적이지 않게 된다.
