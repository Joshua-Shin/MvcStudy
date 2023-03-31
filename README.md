# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
-------------
### 단원별 요점 정리

#### 웹 애플리케이션의 이해
- 모든게 http
- 웹 서버: 정적 페이지 처리
- 웹 애플리케이션 서버(WAS) : 동적 페이지 처리. 비즈니스 로직 같은거. 주로.
- 서블렛: http 요청 메시지를 받아 의미있는 단어로 파싱하고 객체로 만들어주고, 다시 응답할 객체를 응답 http 메시지로 만들어주는걸 대신 해줌
- 서블렛 컨테이너: 서블렛 객체를 관리. 생애주기 관리. 서블릿 객체는 싱글톤. 멀티쓰레드 지원. 쓰레드풀 관리.
- 톰캣 : WAS중 하나로, 서블릿 컨테이너만 있는 있는 WAS
- 서버에서의 통신은 결국 정적페이지, 동적페이지, api 이 3가지를 신경쓰면 돼.
  - 정적페이지는 순수 html 그냥 그대로 보내주면 되는거.
  - 동적페이지는 html를 조작해서 보내줘야함. 
    - SSR: 서버쪽에서 랜더링 다 해서 보내주는거. 과거에는 JSP 사용, 요즘에는 타임리프 사용.
    - CSR: 클라이언트쪽에서 랜더링. react나 vue.js 사용하는거.
      - 1_ 클 : 웹 페이지 get 요청
      - 2_ 섭 : html 내용 없고, js 링크 보내줌
      - 3_ 클 : js 파일 요청
      - 4_ 섭 : js, react소스코드, html 랜더링코드 등등 모든 화면 로직 소스코드 보내줌
      - 5_ 클 : (필요하다면) 여기 들어갈 데이터도 줘
      - 6_ 섭 : json 형태의 api로 데이터 보내줌.
      - 7_ 클 : 클라이언트쪽에서 html, js, api로 받은 데이터 잘 섞어서 화면 랜더링!
  - API는 클라이언트쪽이나 다른 서버쪽에 html를 보내는게 아니라 데이터, 객체를 보내는거야.

#### 서블릿
- **클라이언트쪽에서 보내는 http 요청 메시지의 종류는 크게 3가지**
  - 1_ get 쿼리파라미터로 보내는 경우. uri에 ? 다음으로 쿼리 넣어서 보내는거.
  - 2_ html form 으로 submit 하는 경우. 쿼리파라미터 형태의 데이터가 http 바디에 담겨져서 보내짐.
  - 3_ http api로 보내는 경우. http message body에 직접 데이터를 담아 보냄. 주로 json 형식.
   - 1과 2는 request.getParameter("username") 를 통해 value 가져올 수 있음.
   - 3은 jackson 라이브러리를 사용, objectMapper를 통해 string을 내가 만든 클래스 객체에 매핑시킬 수 있음
- http 응답 메시지
  - 넘길 content로는 단순 text, html, api 세가지 보낼 수 있어.
  - 그밖에 상태코드나, 인코딩방식, 여러가지 헤더값들을 지정해서 보낼 수 있음.
  - 주로 api 보낼텐데, 내가 만든 클래스 객체를 역시 objectMapper를 통해 json형태의 string 타입으로 변환해서 전달.

#### 서블릿, jsp, MVC패턴
- 서블릿으로 비즈니스 로직 처리하고 response body에 자바코드내에서 억지로 억지로 html을 한줄한줄 println 해서 보내는건, 너무 말도 안될정도로 번거로워. 오타날 가능성도 너무 크고.
- jsp에서는 <%  %> 이 문법을 통해서 상단에는 자바코드를, 하단에는 html을 만들어서 뷰를 그릴 수 있고, request와 responce도 바로 쓸 수 있긴한데, 그럼에도 한 페이지 안에서 뷰와 비즈니스로직을 다 짬뽕시켜서 코딩하는게 말이 안돼.
- 이러한 한계를 바탕으로 뷰와 비즈니스로직을 처리하는것을 분리하자는 아이디어가 나왔음. 이게 MVC 패턴임. 초기 MVC패턴은 뷰는 jsp으로, 비즈니스로직은 서블릿으로 진행.
- **MVC 패턴** <br> <img width="626" alt="스크린샷 2023-03-15 오후 9 37 50" src="https://user-images.githubusercontent.com/93418349/225310882-51ba0a27-f77f-4025-89ad-106855e6defd.png">
- controller에는 비즈니스 로직을 직접 담는것보다는 비즈니스 로직을 호출을 담당. 비즈니스 로직은 service, repository가 담당.
- 훨씬 깔끔해졌지만, 그럼에도 서블릿에서 처리해서 jsp쪽으로 포워드 시키는 과정이 여러 서블릿 클래스에서 반복되기에 수문장 역할을 하는 프론트 컨트롤러의 필요성이 느껴짐.

<!--
#### MVC 프레임워크 만들기
- <br> <img width="816" alt="스크린샷 2023-03-22 오후 6 53 45" src="https://user-images.githubusercontent.com/93418349/226866546-6bad35b4-0335-4952-b9e8-bc58d6c29ed3.png">
- 이렇게 구조를 다형성으로 만들면, 수정 확장에 용이. 핸들러어댑터에서 각각의 핸들러에 맞춰 호출과 반환을 진행.
-->

#### 스프링 MVC - 구조 이해
<img width="878" alt="스크린샷 2023-03-23 오후 6 23 58" src="https://user-images.githubusercontent.com/93418349/227159133-00b3cb80-37e6-422d-a61d-23ae3fb4f3bb.png">
<img width="901" alt="스크린샷 2023-03-23 오후 6 24 06" src="https://user-images.githubusercontent.com/93418349/227159189-dd9f205d-0d27-4da0-a1b8-faa9cbbe274e.png">



#### 스프링 MVC - 기본 기능
- @RequestMapping : DispatcherServlet이 핸들러매핑정보와 핸들러어댑터매핑정보를 확인하여 이에 대응되는 핸들러를 호출하게 해주는 url를 지정할 수 있음.
  - 메소드 레벨에서 지정하며, 해당 메소드들을 담은 클래스에는 @Controller를 선언하고 있음. 이는 @Component도 포함된 어노테이션으로 스프링 빈 등록을 가능케 해줌.
    - @RestController
      - @Controller에서는 반환하는 문자열이 viewname으로 전달되지만. 얘는 응답 바디에다가 해당 문자열을 바로 넣어 응답함. 
      - 결국 @Controller + @ResponseBody 기능임. 실제 에노테이션 들어가면 그렇게 되어있음.
  - @GetMapping, @PostMappint ... : http 메소드를 더 분명히 명시하여 지정함.
  - @RequestParam : 서블릿request로 인자를 얻어오는게 아니라, 간단히 인자값을 가져올 수 있음
  - @PathVariable : url에 {}로 들어온 값들을 가져올 수 있음.
  - 그밖에 쿼리, 헤더타입 등등을 제한시켜서 해당 타입과 매칭되지 않으면 오류를 발생시키는 기능들도 세세하게 있긴 함.
- @Slf4j : 클래스레벨에 선언. 로그 출력하는 인스턴스 log 만들어줌
  - 실무에서는 println으로 하는게 아니라, 로그를 찍어야함. 로그에는 쓰래드명, 클래스명, 시각, 이런것들이 다 담겨있음.
  - slf4j는 인터페이스, 이의 구현체가 logback임. Logger log = Logger.getLogger(getClass()) 뭐 이리 해서 log을 주입받는데, 해당 에노테이션으로 간단히 해결
- 클라이언트쪽에서 보내는 http 요청 메시지 유형 3가지에 따른 요청 파라미터 대응 방법
  - get으로 url에 쿼리 보낼때 & html form 으로 post 할때.
    - 핸들러 메소드의 인자로 @RequestParam을 지정하여 해당 키에 따른 값을 받는다. 요청 파라미터의 이름과 메소드 매개변수의 이름을 동일하게 하면 @RequestParam 도 생략 가능
    - 핸들러 메소드의 인자로 @ModelAttribule를 지정하면 메소드 매개변수인 객체의 필드에 자동으로 바인딩됨. 바인딩에 오류가 없으려면 객체의 필드이름과 타입이 요청파라미터와 맞아야 되고, getter, setter 있어야함. @ModelAttribule도 생략 가능. 
    - 생략했을때, 메소드의 매개변수 타입이 int, String 이런 기본값이라면 @RequestParam으로 작동되고, 내가 만든 객체라면 @ModelAttribute로 작동함.
  - post인데 메시지바디에 메시지를 담아 보낼때.
    - 핸들러 메소드의 인자로 @RequestBody String messageBody를 명시하면 http 메시지의 body 내용이 그냥 바로 문자열로 컨버팅되어서 들어가짐.
    - 핸들러 메소드의 인자로 @RequestBody HelloData helloDate를 명시하면 http 메시지의 body 내용이 HelloData 객체 필드에 맞춰 바인딩됨.
    - 메소드 레벨에 @ResponseBody 달면, return 하는 문자열이 view조회를 안하고 바로 응답 http 메시지 바디에 바로 들어가짐. 
- 클라이언트쪽으로 보내는 http 응답 메시지
  - 정적페이지
  - 동적페이지. 템플릿.
  - http body 메시지.
    - 메소드 레벨에다가 @ResponseBody 달거나, 클래스 레벨에다가 @RestController 해주거나.
    - @ResponseBody 에서 객체를 바로 보내면 상태 코드를 따로 지정해서 보내기가 어려운데 이때 @ResponseStatus로 보내주면됨. 대신 고정값이겠지. 상태에따라 보낼려면 @ResponseBody쓰면 안돼
- 핸들러가 굉장히 다양한 방식으로 요청을 받고 다양한 방식으로 응답을 하는데, 이걸 가능케 해주는게 핸들러 어댑터야.
- 핸들러 어댑터에서 핸들러를 호출하고 값을 반환 받는 과정에 ArgumentResolver가 있는데 얘가 클래스와 컨텐츠타입을 잘 확인해서 이를 잘 처리할 수 있는 핸들러에게 보내주는거야. 그 과정에서 바디에 메시지가 들어가있으면 http메시지컨버터도 호출이 됨.
- 핸들러 어댑터와 핸들러 사이의 작동 구조 <br>
<img width="832" alt="스크린샷 2023-03-26 오후 8 47 21" src="https://user-images.githubusercontent.com/93418349/227773541-1b83a0e2-c6e0-4f48-aa57-5b061b1b7399.png">




#### 스프링 MVC - 웹 페이지 만들기
- 타임리프 문법 몇가지
   ```
   <html xmlns:th="http://www.thymeleaf.org">
   th:href="@{/css/bootstrap.min.css}"
   th:onclick="|location.href='@{/basic/items/add}'|"
   <tr th:each="item : ${items}">
   <td th:text="${item.price}">10000</td>
   th:href="@{/basic/items/{itemId}(itemId=${item.id})}"
   th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}" 
      => http://localhost:8080/basic/items/1?query=test
   th:href="@{|/basic/items/${item.id}|}"
   <h2 th:if="${param.status}" th:text="'저장 완료!'"></h2>
   ```
- @PostConstruct : 테스트용으로 데이터 init 느낌으로 넣어두는 메소드
- html action : form data를 서버로 보낼때 도착할 url을 명시해줌.
- @ModelAttribute: 이게 요청 파라미터를 객체로 받는것 뿐만 아니라, 클래스명의 앞글자를 소문자로 바꾼 이름을 키로 하는, model 기능을 하는 객체를 생성 저장해서 view에 넘겨줌
- post 이후 리다이렉트 하는 이유: 새로고침하면 post 메소드가 또 실행되어버리니까, 메소드를 get으로 바꾸려면 리다이렉트로 리턴해줘야 함.

-------------------
#### 최종 미션
- core.md 암기
- 3~7페이지에 있는 요구사항을 보고, 직접 구현. 단, static view 쪽은 복붙 허용
