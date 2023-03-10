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
      - 1. 클 : 웹 페이지 get 요청
      - 2. 서버 : html 내용 없고, js 링크 보내줌
      - 3. 클 : js 파일 요청
      - 4. 서버 : js, react소스코드, html 랜더링코드 등등 모든 화면 로직 소스코드 보내줌
      - 5. 클 : (필요하다면) 여기 들어갈 데이터도 줘
      - 6. 서버 : json 형태의 api로 데이터 보내줌.
      - 7. 클 : 클라이언트쪽에서 html, js, api로 받은 데이터 잘 섞어서 화면 랜더링!
  - API는 클라이언트쪽이나 다른 서버쪽에 html를 보내는게 아니라 데이터, 객체를 보내는거야.

#### 서블릿
- **클라이언트쪽에서 보내는 요청 메시지의 종류는 크게 3가지만 구분해서 처리하며 돼.**
  - get 쿼리파라미터로 보내는 경우
  - html form 으로 submit 하는 경우. 쿼리파라미터 형태의 데이터가 http 바디에 담겨져서 보내짐.
  - http api로 보내는 경우.
