#### 웹 애플리케이션의 이해
- Web Server vs Web Application Server
  - 웹 서버: 정적 페이지 처리 ex) NGINX(엔진엑스), APACHE
  - 웹 애플리케이션 서버(WAS) : 동적 페이지 처리. 코드 실행해서 비즈니스 로직 수행. 동적 html, http api, 서블릿, jsp, spring mvc.. ex) 톰캣
  - 사실, 둘의 경계가 딱 나누어 떨어지지 않아. 웹 서버도 로직 수행하게하려면 할 수 있고, WAS도 정적페이지를 처리하게 할 수 있어.
  - 그러나 그냥 정적 페이지 뿌려주는건 웹서버에서, 비즈니스로직 수행하고 동적페이지 뿌리는건 WAS에서 한다 정도로 정리.
  - WAS에다가 정적페이지 뿌리는것까지 다 몰아주면 스케일업이나 유지 보수등에 좋지 않음. 역할을 항상 분담하는게 트래픽이 몰리든 뭐하든 유지보수에 좋아.
- 서블렛: http 요청 메시지를 받아 의미있는 단어로 파싱하고 객체로 만들어주고, 다시 응답할 객체를 응답 http 메시지로 만들어주는걸 대신 해줌
- 서블렛 컨테이너: 서블렛 객체를 관리. 생애주기 관리. 서블릿 객체는 싱글톤으로 관리. 동시 요청을 위한 멀티쓰레드 지원. 쓰레드풀 관리.
  - 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라함.
  - request 객체, response 객체는 여러개더라도 서블릿 객체는 싱글톤이야.
  - 쓰레드: 애플리케이션 코드를 하나하나 순차적으로 실행하는 그것.
    - 한번에 하나의 코드 라인만 수행
    - 동시 처리가 필요하면 쓰레드를 추가로 생성
    - 서블릿을 호출하는것도 쓰레드가.
- 서버에서의 통신은 결국 정적페이지, 동적페이지, api. 이렇게 3가지를 신경쓰면 돼.
  - SSR: 서버쪽에서 랜더링 다 해서 보내주는거. 과거에는 JSP 사용, 요즘에는 타임리프 사용.
  - CSR: 클라이언트쪽에서 랜더링. react나 vue.js 사용하는거.
    - 1_ 클 : 웹 페이지 get 요청
    - 2_ 섭 : html 내용 없고, js 링크 보내줌
    - 3_ 클 : js 파일 요청
    - 4_ 섭 : js, react소스코드, html 랜더링코드 등등 모든 화면 로직 소스코드 보내줌
    - 5_ 클 : (필요하다면) 여기 들어갈 데이터도 줘
    - 6_ 섭 : json 형태의 api로 데이터 보내줌.
    - 7_ 클 : 클라이언트쪽에서 html, js, api로 받은 데이터 잘 섞어서 화면 랜더링!

#### 스프링 MVC - 구조 이해
<img width="800" alt="스크린샷 2023-04-03 오후 2 20 38" src="https://user-images.githubusercontent.com/93418349/229418146-2be8ae62-5ddc-4c67-b765-3970f27ccd1c.png">

#### 스프링 MVC - 기본 기능
- 클라이언트쪽에서 보내는 http 요청 메시지 유형 3가지에 따른 요청 파라미터 대응 방법
  - 1_get으로 url에 쿼리 보낼때 & 2_html form 으로 post 할때.
    - @ModelAttribule 또는 @RequestParam.
  - 3_post인데 메시지바디에 메시지를 담아 보낼때.
    - @RequestBody String messageBody를 명시하면 http 메시지의 body 내용이 그냥 바로 문자열로 컨버팅되어서 들어가짐.
    - @RequestBody HelloData helloDate를 명시하면 http 메시지의 body 내용이 HelloData 객체 필드에 맞춰 바인딩됨.
    - 메소드 레벨에 @ResponseBody 달면, return 하는 문자열이 view조회를 안하고 바로 응답 http 메시지 바디에 바로 들어가짐. 
- 클라이언트쪽으로 보내는 http 응답 메시지 3가지 유형
  - 1_정적페이지.
  - 2_동적페이지. 템플릿.
  - 3_http body 메시지.
    - 메소드 레벨에다가 @ResponseBody 달거나, 클래스 레벨에다가 @RestController 해주거나.
    - 상태 코드는 @ResponseStatus사용. 대신 고정값이라 동적으로 보낼려면 애초에 @ResponseBody 쓰면 안돼
- 실습 예제의 경우 요청은 1번 2번 유형, 응답은 2번 유형으로만 진행함.
- 핸들러 어댑터와 핸들러 사이의 작동 구조 <br>
<img width="800" alt="스크린샷 2023-03-26 오후 8 47 21" src="https://user-images.githubusercontent.com/93418349/227773541-1b83a0e2-c6e0-4f48-aa57-5b061b1b7399.png">

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
- html action 태그: form data를 서버로 보낼때 도착할 url을 명시해줌. th:action 만 쓰고 주소를 명시하지않으면 요청 들어온곳과 동일한곳으로 보내짐.
- @ModelAttribute: 이게 요청 파라미터를 객체로 받는것 뿐만 아니라, 클래스명의 앞글자를 소문자로 바꾼 이름을 키로 하는, model 객체를 생성 저장해서 view에 넘겨줌.
- post 이후 리다이렉트 하는 이유: 새로고침하면 post 메소드가 또 실행되어버리니까, 메소드를 get으로 바꾸려면 리다이렉트로 리턴해줘야 함.

-------------------
#### feedback
- 요구사항 보고 구현할때 주의할점.
  - gradle - groovy
  - 도메인객체 -> repository -> repositoryTest ->  view & Controller 
  - 백엔드 구현하는거니까 static html은 일단 복붙하고, 타임리프로 바꾸는건 직접하자.
  - private static final Map<Long, Item> store = new HashMap<>();
  - private static Long sequence = 0L;
  - findAll 에서 new ArrayList<>(store.values());
  - Test에서 Assertions.assertThat().isEqualTo() 로 검증.
  - Test에서 Assertions.assertThat().contains(item1, item2)로 검증
  - package이름에 ‘-‘ 같은 특수기호 들어가면 절대 안돼!!! 이것때문에 2시간 날림..
  - controller 막 만들기 전에 URI 부터 설계하는게 좋아.
  - 결국 Controller 구현의 핵심은 요청 URI와 응답할 html을 매핑해주고 적절한 model을 보내주는거야.
  - @ModelAttribute는 기능상 생략할 수 있지만, 명시해두는게 좋은듯.
  - 추후에 내가 웹사이트를 만든다면, static html을 먼저 만들어두고, 그것을 기반으로 controller와 동적 html을 만들어가는게 좋을듯.
  - 타임리프에서 @{} ${} 이런거 쓸때, 리터럴 문자 표현 || 이거 주의하고.
  - onclick=“location.href=‘ ‘”
  - post 후에는 redirect 해주고. 
  - redirect로 {itemId} 에 넣을 PathVariable로 가져온 id값이 없다면,
    RedirectAttribute redirectAttribute 사용해서 리다이렉트 주소에 넣을 값을 addAttribute 해주면 돼.
