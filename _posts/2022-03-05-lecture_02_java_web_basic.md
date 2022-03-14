---
layout: single
title:  "[Lecture] - JAVA 및 Servlet, Spring, Spring Boot 정리"
excerpt: "JAVA 및 서블릿, Spring 버젼별 스택정리"

categories:
  - Lecture
tags:
  - [Lecture, Java, Servlet]

toc: true
toc_sticky: true
 
date: 2022-03-05
last_modified_at: 2022-03-05
---

## Spring - JDK 간 버전 호환

|:---:|:---:|
|스프링 버전|지원JDK|
|:---:|:---:|
|Spring Framework 6.0.x | JDK 17-21 (expected) |
|Spring Framework 5.3.x | JDK 8 - 19 (expected) |
|Spring Framework 5.2.x | JDK 8 - 15 |
|Spring Framework 5.1.x | JDK 8 - 12 |
|Spring Framework 5.0.x | JDK 8 - 10 |
|Spring Framework 4.3.x | JDK 6 -  8 (its official EOL(end-of-life)) |
 
## Spring Boot - JDK 간 버전 호환

|:---:|:---:|
|스프링부트 버전|지원JDK|
|:---:|:---:|
|Spring Boot 2.3↑ | Java 9 and above|
|Spring Boot 2.1↓ | Java 8 - 11|

## 서블릿 버젼별 사양정리

|:---:|:---:|:---:|:---:|:---:|
|버전|발표일|Java|Tomcat|주요내용|
|:---:|:---:|:---:|:---:|:---|
|6.0|2021/10|JakartaEE 10(8이후)|-|사용되지 않는 기능 삭제 및 요청된 확장 기능 구현|
|4.0|2017/09|8이후|9.0.X|HTTP/2 지원|
|5.0|2020/10|JakartaEE 9(8이후)|10.0.X|API가 패키지 javax.servlet에서 jakarta.servlet로 이동|
|3.1|2013/05|7이후|8.5.X|논블록킹 처리용 I/O API 추가, WebSocket 대응, 클라우드 지원|
|3.0|2009/12|6이후|7.0.X|동적 설정(web.xml 대신 어노테이션 사용), 비동기 Servlet, 멀티파트 파일업로드|
|2.5|2005/09|5이후|6.0.X|자바SE 5	JavaSE 5가 필수, annotation 지원|
|2.4|2003/11|J2EE 1.4|5.5.X|J2SE 1.3	web.xml에 XML 스키마 사용|


## Spring MVC

![Spring MVC 개념1](./../../images/lecture/spring_mvc01.png)

1. 핸들러 조회 : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
2. 핸들러 어댑터 조회 : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. 핸들러 어댑터 실행 : 핸들러 어댑터를 실행한다.
4. 핸들러 실행 : 핸들러 어댑터가 실제 핸들러를 실행한다.
5. ModelAndView 반환 : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서
반환한다.
6. viewResolver 호출 : 뷰 리졸버를 찾고 실행한다.
 - JSP의 경우: InternalResourceViewResolver 가 자동 등록되고, 사용된다.
7. View반환 :뷰 리졸버는 뷰의 논리이름을 물리이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
 - JSP의 경우 InternalResourceView(JstlView) 를 반환하는데, 내부에 forward() 로직이 있다.
8. 뷰 렌더링 : 뷰를 통해서 뷰를 렌더링한다.

## Dispatcher Servlet

![Dispatcher Servlet 개념1](./../../images/lecture/dispatcher_servlet_01.jpg)

![Dispatcher Servlet 개념2](./../../images/lecture/dispatcher_servlet_02.png)

### Dispatcher Servlet 역할
1. 클라이언트의 요청을 디스패처 서블릿이 받음
2. 요청 정보를 통해 요청을 위임할 핸들러(컨트롤러)를 찾음
3. 요청을 컨트롤러로 위임 처리할 핸들러 어댑터를 찾음
4. 핸들러 어댑터가 핸들러(컨트롤러)로 요청을 위임함
5. 비지니스 로직이 처리됨
6. 컨트롤러가 ResponseEntity를 반환함
7. HandlerAdpater가 반환받은 ResponseEntity를 통해 Response 처리를 진행함
8. 서버의 응답을 클라이언트로 반환함

- 디스패처 서블릿은 어느 컨트롤러가 해당 요청을 처리할 수 있는지를 식별해야 하는데, 디스패처 서블릿은 모든 컨트롤러를 파싱하여 HashMap으로 (요청 정보, 요청을 처리할 대상)을 관리 (요청을 처리할 대상은 요청을 처리할 컨트롤러와 요청에 매핑되는 메소드)

- 요청이 들어오면 요청 정보 객체를 만들어 Map에서 요청을 처리하는 컨트롤러 및 메소드를 찾습니다. 그리고 디스패처 서블릿에서 컨트롤러로 요청을 위임할 Adapter(RequestMappingHandlerAdapter)를 찾은 후에 해당 adapter를 통해 컨트롤러로 요청을 위임합니다. 이때 요청을 처리할 대상 정보에 컨트롤러의 메소드가 있으므로 Reflection으로 컨트롤러의 메소드를 호출

- 컨트롤러가 ResponseEntity를 반환하면 MessageConverter 등을 통해 응답 처리를 진행한 후에 결과를 클라이언트에게 반환
