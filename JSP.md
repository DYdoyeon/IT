#### JSP

요청이 들어오면 서버단에서 실행해서 결과를 만들어 결과를 응답함. html 처럼 생겼으나 내부적인 실행은 서블릿으로 변환되어 돌아감

####  JSP 스크립트 요소

##### 내장객체

JSP 페이지 내에서 제공하는 특수한 레퍼런스 타입의 변수로 사용자가 객체를 생성하지 않아도 사용이 가능함.

아무런 선언과 객체 생성 없이 사용할 수 있는 이유는 JSP가 서블릿으로 변환될 때 JSP 컨테이너가 자동적으로 제공하기 때문에

![JSP내장객체](/Users/kimdoyeon/study/스크린샷폴더/JSP_내장객체.png)

###### scope

웹 어플리케이션의 영역 : page, request, session, application

기본 객체의 영역은 객체의 유효기간이라고도 불리며, 객체를 누구와 공유할 것인가를 나타냄

1. page 영역

   한 번의 웹 브라우저(클라이언트)의 요청에 대해 하나의 JSP 페이지가 호출됨

   웹 브라우저의 요청이 들어오면 단 한 개의 페이지만 대응이 됨. 

   page 영역은 pageContext 기본 객체를 사용하며 객체를 하나의 페이지 내에서만 공유함.

   > pageContext 내장 객체
   >
   > 현재 jsp 페이지의 컨텍스트를 나타내며, 주로 다른 내장 객체를 구하거나 페이지의 흐름 제어 그리고 에러 데이터를 얻어낼 때 사용함.

2. request 영역

   한 번의 웹 브라우저의 요청에 대해 같은 요청을 공유하는 페이지가 대응됨

   웹 브라우저의 한 번의 요청에 단지 한 개의 페이지만 요청될 수 있고, 때에 따라 같은 request 영역에 두 개의 페이지가 같은 요청을 공유할 수 있음.

   request 영역은 객체를 하나 또는 두 개의 페이지 내에서 공유할 수 있음.

   include 액션 태그, forward 액션 태그를 사용하면 request 기본 객체를 공유하게 되어 같은 request 영역이 되며 주로 페이지 모듈화에 사용됨.

3. session 영역

   하나의 웹 브라우저 당 1개의 session 객체가 생성됨

   같은 웹 브라우저 내에서는 요청되는 페이지들은 같은 객체를 공유하게 됨

4. application 영역

   하나의 웹 애플리케이션 당 1개의 application 객체가 생성됨

   같은 웹 애플리케이션에 요청되는 페이지들은 같은 객체를 공유함

   웹 애플리케이션의 설정 정보를 갖는 context와 관련이 있는 객체. 서버의 설정 정보 및 자원에 대한 정보를 얻어내거나 어플리케이션 실행되고 있는 동안에 발생할 수 있는 이벤트 로그 정보와 관련된 기능들을 제공

##### 액션 태크

스크립트, 주석, 디렉티브와 함께 JSP 페이지를 이루고 있는 요소

페이지와 페이지 사이의 제어를 이동시킬 수 있고, 다른 페이지의 실행 결과를 현재의 페이지에 포함시킬 수 있으며, 자바빈도 JSP 페이지에서 사용할 수 있는 기능을 제공하고 웹 브라우저에서 자바 애플릿을 실행시킬 수 있음



##### 요청재지정

실제 요청된 application에서 새로운 서블릿이나 jsp를 다시 요청하는 것. 서블릿에서 view 단을 분리시키기 위해서 응답화면을 별도의 jsp 파일로 작성한 후 요청재지정을 통해 응답화면이 실행되도록 작업

- response      객체를 이용하거나 RequestDispacher 객체를 이용해 작업 필요

1. HTTPServletResponse 객체의 sendRedirect() 를 이용

   **데이터 공유는 불가**하나 데이터를 넘겨주는 것은 가능함

   - response.sendRedirect("요청재지정할 application의 경로")

   처음 요청되었던 페이지가 응답된 후 다시 새롭게 요청을 함. 두 번의 요청을 통해 작업 수행

   http 프로토콜의 특성 중 stateless 특성으로 인해 두 번의 요청이 들어가면서 저장하고 있던 모든 데이터들이 clear되는데, 두 번의 요청으로 응답화면이 생성되는 것이므로 주소표시줄이 요청재시정한 url로 변경됨.

   한글은 encode를 호출하여 인코딩하는 작업 필요

2. RequestDispatcher 객체를 이용하여 요청재지정

   실제 요청재지정할 객체의 정보를 갖고 있는 객체로 forward()와 include()를 갖고 있으며 **데이터 공유 가능**

   forward()는 요청 재지정 후 바로 클라이언트로 response.

   ~~~javascript
   <jsp:forward page="경로"/> // 액션 태그
   ~~~

   include()는 요청 재지정 후 다시 되돌아와서 response

   ~~~javascript
   <%@ include file = "file_name.jsp"%> // 지시어
   <jsp:include page="file_name.jsp"/> // 액션 태그
   ~~~

   - RequestDispatcher rd = request.getRequestDispatcher("요청재지정될 경로")

     rd.forword(request,response);

     rd.include(request,response);

   >forward와 include의 차이 - Request 제어권의 차이
   >
   >1. forward : 제어권이 다른 페이지로 넘어감.
   >
   >   url1 에서 forward해서 url2에 전달해 화면에 url2의 내용이 보임
   >
   >2. include : 제어권을 다른 페이지에 잠시 빌려주는 것.
   >
   >   url1에서 Include하면 url2로 제어권을 잠시 빌려주고 처리를 하면 url1로 돌아옴. 클라이언트 화면에서는 url1이 보임

3. 데이터 공유

   같은 request scope에서 실행되는 모든 application은 데이터를 공유해서 사용할 수 있으며, 다음과 같은 메소드를 이용할 수 있음. 

   request 객체가 갖고 있는 메소드를 이용해서 작업함

   - request.setAttribute("공유명",공유할 객체)
   - request.getAttribute("공유명") : casting 필요
   - request.removeAttribute("공유명")













내장객체

https://hyeonstorage.tistory.com/78

요청재지정

https://m.blog.naver.com/PostView.nhn?blogId=robinnw&logNo=220003864754&proxyReferer=https:%2F%2Fwww.google.com%2F













