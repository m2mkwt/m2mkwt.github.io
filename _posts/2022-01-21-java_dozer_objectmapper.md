---
title:  "[JAVA] - Object Mapping (feat - Dozer, Orika)"
excerpt: "[작업전] Java Bean To Java Bean 맵핑 라이브러리 정리"

categories:
  - Java
tags:
  - [java, library, mapper]

toc: true
toc_sticky: true
 
date: 2022-01-21
last_modified_at: 2022-01-21
---

# Java Bean To Java Bean 맵핑 라이브러리
 * object mapper 
   - 텍스트 형태의 JSON을 object로 변경해 주거나 object를 텍스트 형태의 JSON으로 변경해 주는 역활. 
   - ex1) Controller 에 요청이 오면 JSON을 object로 변경.
     ex2) 응답으로 object를 보내면 json으로 변경.



## 1. Java Bean To Java Bean 맵핑 라이브러리
# Dozer란 무엇인가?
 * Dozer는 한 객체 필드의 데이터를 다른 객체로 재귀적으로 복사해주는 Mapper이다. 그래서, "Dozer Mapper"라고 불리며, Java Bean to Java Bean 기능을 지원한다. Dozer는 단순 특성 매핑 및 복합 유형 매핑, 양방향 매핑, 암시적/명시적 매핑, 재귀적 매핑 등을 지원하는데, 다양한 데이터 필드를 가지고 있는 객체 A에서 객체 B로 매핑이 대부분 가능하다고 생각하면 된다. Collection 속성까지 되기 때문에 문서를 잘 읽어보고 활용해볼 수 있을 듯 하다.

# Dozer Mapper의 특징
 * 속성 이름 간 매핑을 지원한다.
 * 속성 이름뿐만 아니라 유형 간 자동 형변환도 지원한다.
 * 양방향 매핑을 지원한다.
 * 데이터베이스의 내부 도메인 객체가 외부 프리젠테이션 레이어나 외부 소비자로 번지지 않도록 도와준다.
   또한, 도메인 객체를 외부 API 호출에 매핑하거나 그 반대로 매핑 할 수 있다. (양방향 매핑 지원)
 * 리플렉션을 통해 대부분의 필드 매핑을 자동으로 수행하며, XML 혹은 @Mapping 어노테이션을 통해 사용자 정의 매핑을 설정 할 수 있다.
 
# 왜, Dozer를  도입했을까?
 * 객체 별로 관심사를 분리가 필요하여 도입하게 되었다. 예를 들어, REST API 서버를 만든다고 가정해보자. 사용자의 요청 처리를 하는 Controller가 존재할 것이다. Contoller는 사용자의 데이터를 받는 객체 A라는 엔티티가 있을 것이다. 보통은 이 객체는 DB Domain과 그대로 카멜기법으로 1:1 매칭시켜놨을 것이다. 과거를 회상해보자.

 * 요청 파라미터를 추가하거나 다른 데이터 가공을 위해 DB Domain 뿐만 아니라 xxxFlag, xxxDatetime 등등 다양하게 추가했던 과거를... 나중에 보면 뭐가 DB 도메인이고, 뭐가 추가된 부분인지 구분하기 어려울 뿐더러 변경할 때마다, Controller - Service - domain 까지 전부 영향을 받게 된다. 분명히 관심사 영역이 다름에도 이렇게 사용하고 있었다. 관심사대로 분리하기 위해서 WEB Request Data(DTO)와 DB Domain Entity로 나누고, 상호 간에 쉽게 매핑할 수 있도록 Dozer를 도입하게 되었다. 또한, Dozer는 변환시 자동 형변환을 지원하기 때문에 Number에서 String으로 변경돼도, Dozer Mapper가 자동으로 해결해주므로 애플리케이션에 별다른 타격 없이 계속 작동할 수 있다. 굳!

  **Dozer를 통해 Controller (DTO A) ↔ Service (Entity A') ↔ ORM(JPA) ↔ DB 의 흐름으로 만들 수 있게 된 것이다. 이러면, 사용자에게 주는 응답 데이터 항목에 실제 DB 테이블 컬럼명을 노출시키지 않아도 된다는 장점도 존재한다.**

#  왜, Dozer 여야 했을까?
 * 이미 여러 가지 Object Mapper에 대해 고수들은 성능 측정을 마친 상태이다. Dozer는 속도가 느린 편에 속한다고 대부분의 결과에서 보여진다. 그럼에도 Dozer는 @애노테이션을 통해 코드를 깔끔하게 만들 수 있다는 점이 도입의 가장 큰 이유이다. Getter/Setter를 통하여 수동으로 작업해주는게 가장 빠른 방법이라고 명시되어 있다. 하지만, 이 방법을 한 번 해보면 안다. 코드가 길어진다. 눈이 피곤해진다.

## Spring + Jersey + Dozer 예제
 * 웹서비스를 처리하는 그룹과 그것을 이용하는 그룹이 분리되어 있다면, 서로 다른 목적의 객체에 담기는게 유리하다. 그때 각각의 객체에 값을 이동할 필요가 있는데, 이렇게 복사해(매핑해) 주는 것이 Dozer다. 아래는 그 예제를 순서적으로 기록한 것이다.

1) Response 객체 (jersey 에서 사용)
```java
  @XmlType
  @MappedSuperclass
  @XmlRootElement(name="RESULT")
  public class AccountResponse {
    @XmlElement(name="RESULT_CODE")
    private String resultCode;
    @XmlElement(name="COUNT")
    private int count;
    @XmlElement(name="LINK_URL")
    private String linkUrl;
  ...
```

2) DTO 객체
```java
  public class AccountResult {
    private Long count;
    private String linkUrl;
  ...
```

3) Dozer 매핑 설정
```xml
  <mapping map-id="ws_account"> 
    <class-a>kr.company.ws.model.AccountResponse</class-a>
    <class-b>kr.company.service.model.AccountResult</class-b>   
    <field custom-converter="kr.company.service.model.converter.IntToLongConverter">
      <a>count</a>
      <b>count</b>
    </field>
    <field>
      <a>linkUrl</a>
      <b>linkUrl</b>
    </field>
  </mapping> 
```

[Converter] 
```java
public class IntToLongConverter implements CustomConverter {
	@Override
	public Object convert(Object existingDestinationFieldValue, Object sourceFieldValue, Class<?> destinationClass, Class<?> sourceClass) {
		Integer id = (Integer) sourceFieldValue;
		return Long.valueOf(id);
	}
}
```

4) Spring 설정
```xml
	<bean id="dozerMapper" class="org.dozer.DozerBeanMapper">
	    <property name="mappingFiles">
	        <list>
	            <value>META-INF/spring/dozer-bean-mappings.xml</value>
	        </list>
	    </property>
	</bean>
```

4) 서비스 호출
```java
public class AccountServiceProviderImpl ...
	@Override
	public AccountResult findAccount(Long id) {
		AccountResponse accountResponse = accountWebService.findAccount(id);
		return dozerMapper.map(accountResponse, AccountResult.class,"ws_account");
	}
```

5) 테스트
```java
    @Test
    public void 테스트(){
         ...

    	AccountResult accountResult = accountServiceProvider.findAccount(account.getId());
    	assertNotNull(accountResult);
    }
```

