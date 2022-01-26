---
layout: single
title:  "[WEB.] - Web관련 기타 잡동사니"
excerpt: "서블릿 버젼별 사양"

categories:
  - ETC
tags:
  - [Servelt]

toc: true
toc_sticky: true
 
date: 2022-01-21
last_modified_at: 2022-01-21
---

## 서블릿 버젼별 사양정리

|---|---|---|---|
|버전|발표일|스펙|주요내용|
|2.4|2003/11|J2EE 1.4| J2SE 1.3	web.xml에 XML 스키마 사용|
|2.5|2005/09|자바EE 5| 자바SE 5	JavaSE 5가 필수, annotation 지원|
|3.0|2009/12|자바EE 6| 자바SE 6	개발 용이성 실현, 동적 설정, login/logout 메소드 지원, 비동기 Servlet, 어노테이션Security, File 업로드|
|3.1|2013/05|자바EE 7| 클라우드 지원, 논블록킹 처리용 I/O API 추가, WebSocket 등으로의 프로토콜 업그레이드 대응, 보안 기능 강화|
|4.0|2017/09|자바EE 8| HTTP/2 지원|
|5.0|2020/10|자카르타 EE 9| API가 패키지 javax.servlet에서 jakarta.servlet로 이동|
|6.0|2021/10|자카르타 EE 10| 사용되지 않는 기능 삭제 및 요청된 확장 기능 구현|
