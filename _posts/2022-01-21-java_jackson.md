# Jackson 라이브러리
 * Spring 개발을 하다 보면, 컨트롤러 text/html 형식이 아닌 데이터 전달 목적으로 사용하고 싶을 때가 있습니다. 물론, 쌩 문자열인 plain/text 형식으로 보내도 상관은 없습니다만, 보통은 데이터 구조를 표현하는 방식인 XML 또는 JSON 형태로 많이 보냅니다. 

 * 데이터의 구조를 표현하는 이유는 데이터 표현도 있지만, 사실상 데이터를 사용하는 대상이 편하게 사용하기 위해서 입니다.

 * Jackson은 JSON 데이터 구조를 처리해주는 라이브러리 입니다. 만약 JSON으로 데이터 구조를 표현 한다면,  아래와 같습니다.

    ```json
      {
        "name": "Mommoo",
        "age": 28,
        "isDeveloper": true,
        "equipment": ["NoteBook", "Mouse", "SmartPhone"],
        "etc": {
            "favoriteFood": 'Coffee'
        }
      }
    ```

 * Jackson 라이브러리의 없이, JSON 데이터를 작성해보자. Java에서 데이터를 저장할 때는, 아래와 같이 인스턴스를 사용합니다.

    ```java
      public class Person {
        private String name;
        private String job;
        
        public Person(String name, String job) {
            this.name = name;
            this.job = job;
        }
        
        public String getName(){
            return name;
        }
        
        public String getJob() {
            return job;
        }
      }
      ​
      public static void main(String[] args) {
        Person person = new Person("Mommoo", "Developer");
      }
    ```
    ```javascript
      String JSON = "\"{"+
        "\"name\": \"" + person.getName() + "\","+
        "\"job\": \"" + person.getJob() + "\""+
      "}\"";
    ```
 * Java는 Single Quotes즉, 쌍따옴표("")를 정말 문자열로 사용하고 싶을때는 위와 같이 \문자를 붙여줘야 합니다. 끔찍한 코딩은 덤. 항상 저렇게 코딩 할 순 없으니, JSON 변환용 클래스를 따로 만들고 그 클래스안에 저장된 멤버변수를 이용하여 JSON 데이터를 출력하는 클래스를 생각하게 됩니다.

 * 대표적인 클래스가 Google 이 만든 GSON 또는 SimpleJSON 등이 있습니다. SimpleJSON을 예로 든다면, 윗 코딩이 아래와 같이 바뀝니다.

    ```java
      JSONObject jsonObject = new JSONObject();
      jsonObject.put("name", person.getName());
      jsonObject.put("job", person.getJob());
      String JSON = jsonObject.toString();
    ```
 * 해당 방식은 지금도 많이 쓰입니다. 물론 Spring 컨트롤러에 위와 같이 코딩하여 리턴하더라도, 충분합니다.그렇다면, Jackson은 무엇을 더 제공하길래 Spring은 Jackson을 더 선호하는 것일까요?

## Jackson과 기존 GSON or SimpeJSON과의 차이?
 * 차이는 없습니다. Jackson도 ObjectMapper API를 사용하여, 여타 GSON or SimpeJSON과 같이 객체에 데이터를 셋팅해줘야 하는건 마찬가지 입니다. 특별한 점은 Spring 프레임워크와 Jackson의 관계로부터 장점이 있습니다.

 * Spring 3.0 이후로부터, Jacskon과 관련된 API를 제공함으로써, Jackson라이브러리를 사용할때, 자동화 처리가 가능하게 되었습니다. 덕분에, JSON데이터를 직접 만들던가, GSON or SimpleJSON방식과 같이 직접 키와 벨류를 셋팅하는 방식에서 한단계 더 발전한 방식이 가능해졌습니다. 어떤 방식이길래 발전했을까요? 아래의 Jackson의 동작 원리와 함께 설명할까 합니다.

## Jackson 은 어떻게 동작하는가?
 * Spring3.0 이후로 컨트롤러의 리턴 방식이 @RequestBody 형식이라면,  Spring은 MessageConverter API 를 통해, 컨트롤러가 리턴하는 객체를 후킹 할 수 있습니다.

 * Jackson은 JSON데이터를 출력하기 위한  MappingJacksonHttpMessageConverter를 제공합니다. 만약 우리가 스프링 MessageConverter를 위의 MappingJacksonHttpMessageConverter으로 등록한다면, 컨트롤러가 리턴하는 객체를 다시 뜯어(자바 리플렉션 사용), Jackson의 ObjectMapper API로 JSON 객체를 만들고 난 후, 출력하여 JSON데이터를 완성합니다.

 * 더욱 편리해진 점은, Spring 3.1 이후로 만약 클래스패스에 Jackson 라이브러리가 존재한다면, ( 쉽게 말해Jackson을 설치했느냐 안했느냐 ) 자동적으로 MessageConverter가 등록된다는 점입니다. 덕분에 우리는 아래와 같이 매우 편리하게 사용할 수 있습니다.

    ```java
      @RequestMapping("/json")
      @ResponseBody()
      public Object printJSON() {
        Person person = new Person("Mommoo", "Developer");
        return person;
      }
    ```
 * 이제는 그냥 데이터 인스턴스만 리턴 하더라도 JSON 데이터가 출력됩니다. 위에서 설명한 방식보다 매우 진보한 방식인걸 알 수 있습니다. 다만, Jackson을 더 잘쓰기 위해서는 알아야 하는 기본 지식이 몇가지 존재합니다.

## Jackson을 사용하기 위해 알아야 하는 기본지식

 * Jackson은 기본적으로 프로퍼티로 동작합니다. Java는 프로퍼티를 제공하는 문법이 없습니다. ( 멤버변수랑은 다릅니다.) Java의 프로퍼티는 보통 Getter 와 Setter의 이름 명명 규칙으로 정해집니다. 문법적으로 정해지는것이 아니다 보니, 개발자가 일일이 신경써줘야 하는게 자바의 단점 중 하나입니다.

  - Person 같은 경우는 Getter만 존재 하므로, Getter를 기준으로 프로퍼티를 도출 할 수 있습니다. 즉 Name 과 Job이 Person 프로퍼티입니다. 

  - Person의 멤버변수 이름도 똑같이 name, job이지만, 앞서 설명했드시 프로퍼티는 Getter, Setter기준이므로 멤버변수 이름을 변경하더라도 상관 없습니다. 갑자기 프로퍼티를 설명한 이유는 많은 라이브러리가 해당 프로퍼티 개념으로 작동하기 때문입니다.

 * Jackson라이브러리도 마찬가지 입니다. JSON데이터로 출력되기 위해서는 멤버변수의 유무가 아닌 프로퍼티 즉, Getter, Setter를 기준으로 작동합니다. 예로 아래와 같이 코딩하더라도 전혀 문제가 없습니다.

    ```java
      public class Person {
        public String getName() {
            return "Mommoo";
        }
        
        public String getJob() {
            return "Developer";
        }
      }

      @RequestMapping("/json")
      @ResponseBody()
      public Object printJSON() {
        return new Person();
      }
​   ```

 * 결론적으로 Jackson을 사용한다면, Getter에 신경쓰셔야 합니다!!.

  
 * Jackson의 데이터 매핑을 Getter가 아닌 멤버변수로 하고 싶다면?
  - 그렇다면, 이번에는 Jackson의 매핑을 프로퍼티가 아닌 멤버변수로 할 수 있는 방법은 무엇일까요? Jackson은 이와 관련하여 @JsonProperty 어노테이션 API를 제공합니다. 아래와 같이 멤버변수 위에 프로퍼티 이름과 함께 선언해준다면, JSON데이터로 출력 됩니다.

    ```java
        public class Person {
          @JsonProperty("name")
            private String myName = "Mommoo";
          }
    ```
 * 위의 예시는 {"name": "Mommoo"}로 출력 됩니다.
 * 그렇다면 JSON 매핑을 멤버변수로 하고 싶다면, 매번 @JsonProperty를 선언 해야 할까요? 귀찮습니다. 애초에 Jackson 매핑 구조를 바꾸면 어떨까 하는 생각이 듭니다.

## Jackson의 데이터 매핑 법칙 변경하기
 * Jackson은 매핑 법칙을 바꿀 수 있는 @JsonAutoDetect API를 제공합니다.
 * 위 예시와 같이 멤버변수로만 Jackson을 구성하고 싶은 경우 @JsonProperty를 일일이 붙이는 것보다 아래와 같이 설정하는 것이 더 편리합니다.
    ```java
      @JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY)
      public class Person {
        private String myName = "Mommoo";
      }
    ```
 * @JsonAutoDetect는 멤버변수 뿐만 아니라, Getter, Setter의 데이터 매핑 정책도 정할 수 있습니다. 만약 아래의 경우는 멤버변수 뿐만 아니라, 기본정책인 Getter역시 데이터 매핑이 진행됩니다.

    ```java
      @JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY)
      public class Person {
        private String myName = "Mommoo";
        
        public String getJob() {
            return "Developer";
        }
      }
  ```
 * Getter를 제외하고 싶다면, @JsonIgnore API를 쓰셔도 됩니다.

    ```java
      @JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY)
      public class Person {
        private String myName = "Mommoo";
        
        @JsonIgnore
        public String getJob() {
            return "Developer";
        }
      }
    ```
 * 하지만, 역시 일일이 붙여야 하는 상황이 온다면 매핑 정책을 바꾸시는게 좋습니다.

    ```java
      @JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY, getterVisibility = JsonAutoDetect.Visibility.NON_PRIVATE)
      public class Person {
        private String myName = "Mommoo";
        
        public String getJob() {
            return "Developer";
        }
      }
    ```
 * Getter정책으로 private 만 데이터 바인딩에 제외 하였습니다. 이렇듯, 제외 범위를 설정할 수 있습니다.

## Jackson의 데이터 상태에 따른 포함 관계 설정
 * 만약 Jackson데이터 매핑시 NULL 값 과 같은 특정 데이터 상태인 경우 제외하고 싶다면 어떻게 해야 할까요? Jackson은 이와 관련하여 @JsonIncludeAPI를 제공합니다.
 * NULL을 클래스 전반적으로 제외하고 싶다면, 클래스 위에 선언하면 됩니다. 또한 특정 프로퍼티가 NULL일때 해당 프로퍼티만을 제외하고 싶다면 역시 해당 프로퍼티위에 선언하면 됩니다.

    ```java
      @JsonInclude(JsonInclude.Include.NON_NULL)
      public class Person {
        private String myName = "Mommoo";
        
        public String getJob() {
            return "Developer";
        }
      }
      ​
      public class Person {
        private String myName = "Mommoo";
        
        @JsonInclude(JsonInclude.Include.NON_NULL)
        public String getJob() {
            return "Developer";
        }
      }
    ```
 * JsonInclude.Include 속성은 NON_NULL뿐만 아니라 몇몇 개가 더 존재합니다.
  - [참고](https://fasterxml.github.io/jackson-annotations/javadoc/2.9/com/fasterxml/jackson/annotation/JsonInclude.Include.html)


# Read + Write Annotations
## @JsonIgnore
  -> Jackson이 해당 프로퍼티를 무시하도록 하는 역할을 한다.
```java
public class Test {
    @JsonIgnore
    public long id = 0;
    public String name = null;
}
```
  <-->
  {"name":"circlee"}

## @JsonIgnoreProperties
  -> @JsonIgnore 와 같은 기능, 해당 클래스의 여러 필드리스트를 무시하기 위해 사용한다.
```java
@JsonIgnoreProperties({"firstName", "lastName"})
public class PersonIgnoreProperties {

    public long    personId = 0;

    public String  firstName = null;
    public String  lastName  = null;

}
```
  <-->
  {"firstName":"circlee7","lastName":"eldie"}

## @JsonIgnoreType
  -> JsonIgnoreType 은 해당타입이 사용되는 모든곳에서 무시되도록 한다.

## @JsonBackReference
  -> 부모자식 관계의 자식쪽에 작성함으로써 순환참조관계의 serialize 시 직렬화/역직렬화시 참조하여 동작한다. 
    [참고](https://stackoverflow.com/questions/37392733/difference-between-jsonignore-and-jsonbackreference-jsonmanagedreference)

## @JsonManagedReference
  -> 부모자식 관계의 부모쪽에 작성함으로써 순환참조관계의 serialize 시 직렬화/역직렬화시 참조하여 동작한다.
    [참고](https://stackoverflow.com/questions/37392733/difference-between-jsonignore-and-jsonbackreference-jsonmanagedreference)

## @JsonAutoDetect
  -> JsonAutoDetect는 특정접근제한자의 필드들을 추가하라고 Jackson에게 알려주는 역할을 한다.

```java
@JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY )
public class PersonAutoDetect {

    private long  personId = 123;
    public String name     = null;

}
```
  * JsonAutoDetect.Visibility 는 접근레벨에 맞는 상수가 정의되어있다.
      ANY
    , DEFAULT
    , NON_PRIVATE
    , NONE
    , PROTECTED_AND_PRIVATE
    , PUBLIC_ONLY

## @JsonView
 * JsonView 는 어노테이션 인자로 넘겨진 View 에 해당하는 클래스로 구분되어 직렬화/역직렬화 시 설정된 JsonView 에 따라 매칭되는 프로퍼티들이 역/직렬화 대상에 포함된다.
  ```java
    @AllArgsConstructor
    public @Data class Person {
      private String name;
      
      @JsonView(PersonView.NORMAL.class)
      private String gender;
      
      @JsonView(PersonView.PRIVATE.class)
      private int age;
      
      @JsonView(PersonView.ADDITIONAL.class)
      private String address;
      
      @JsonView({PersonView.ADDITIONAL.class, PersonView.HOBBIES.class})
      private String hobbies;
      
      public static class PersonView {  
        interface NORMAL{}
        interface ADDITIONAL extends NORMAL{}
        interface PRIVATE extends NORMAL{}
        interface HOBBIES extends NORMAL{}
      }
    }
  ```

 * 실행: ObjectMapper writer/reader를 사용시 원하는 View를 지정한다.
  ```java 
    ObjectMapper om = new ObjectMapper();
    Person p = new Person("eldi", "male", 10, "seoul", "basketball");
    System.out.println(om.writeValueAsString(p));
    System.out.println(om.writerWithView(PersonView.NORMAL.class).writeValueAsString(p));
    System.out.println(om.writerWithView(PersonView.ADDITIONAL.class).writeValueAsString(p));
    System.out.println(om.writerWithView(PersonView.PRIVATE.class).writeValueAsString(p));
    System.out.println(om.writerWithView(PersonView.HOBBIES.class).writeValueAsString(p));
  ```
 * 출력:
  {"name":"eldi","gender":"male","age":10,"address":"seoul","hobbies":"basketball"} 
    - view 지정 하지 않는경우 전체 프로퍼티가 직렬화된다.

  {"name":"eldi","gender":"male"}
    - NORMAL 로 직렬화시에는 JsonView를 지정하지 않은 name과 view 와 매칭되는 gender 만 노출된다.

  {"name":"eldi","gender":"male","address":"seoul","hobbies":"basketball"} 
    - ADDITIONAL 로 직렬화 시에는 NORMAL 을 상속받았기 때문에 NORMAL, ADDITIONAL 해당하는 전체를 포함한다.

  {"name":"eldi","gender":"male","age":10}
    - 위와 마찬가지로 PRIVATE 와 부모인 NORMAL 에 해당하는 프로퍼티를 포함한다.

  {"name":"eldi","hobbies":"basketball"}
    - hobbies 프로퍼티는 ADDITIONAL 과 HOBBIES VIEW 가 복수개로 정의되어 있어서 두개의 VIEW 모두에 포함된다.

# Read Annotations (Json Object to Java Object : Deserialize)
## @JsonSetter
  -> Jackson은 Json Data와 Class 의 프로퍼티 이름이 일치해야 하지만 JsonSetter 를 사용하여, Json Data의 특정 프로퍼티 이름으로 Class의 Setter 메소드에 지정할수있다.

  ```java  
    {
      "id"   : 1234,
      "name" : "John"
    }

    <-->

    public class Bag {
        private Map<String, Object> properties = new HashMap<>();

        @JsonAnySetter
        public void set(String fieldName, Object value){
            this.properties.put(fieldName, value);
        }

        public Object get(String fieldName){
            return this.properties.get(fieldName);
        }
    }
  ```
## @JsonAnySetter
 * JSON Object 의 모든 setter 메소드를 인지할수 없는 필드에 대해 JsonAnySetter 어노테이션을 통해 Map<String, Object> 변수로 담을 수 있습니다.

  ```java  
    {
      "id"   : 1234,
      "name" : "John"
    }

    <-->

    public class Bag {
        private Map<String, Object> properties = new HashMap<>();

        @JsonAnySetter
        public void set(String fieldName, Object value){
            this.properties.put(fieldName, value);
        }

        public Object get(String fieldName){
            return this.properties.get(fieldName);
        }
    }
  ```

## @JsonCreator
 * JsonCreater 는 생성자메소드에 지정되어 JSON Object 의 필드 와 메소드의 파라미터 매칭을 통해 Java Object를 생성가능하게 한다.
 * @JsonSetter를 사용할수 없거나, 불변객체이기 때문에 setter 메소드가 존재하지 않을때 초기화 데이터 주입을 위해 유용하다.
  ```java 
    {
      "id"   : 1234,
      "name" : "John"
    }
    <-->
    public class PersonImmutable {

        private long   id   = 0;
        private String name = null;

        @JsonCreator
        public PersonImmutable(
                @JsonProperty("id")  long id,
                @JsonProperty("name") String name  ) {

            this.id = id;
            this.name = name;
        }

        public long getId() {
            return id;
        }

        public String getName() {
            return name;
        }

    }
  ```

## @JacksonInject
 * JacksonInject 어노테이션은 Jackson 에의해 역직렬화된 Java Object에 공통정인 값을 주입할 수 있는 java Object의 프로퍼티를 지정한다.
 * 예를들어 여러 소스에서 Person Json Object를 파싱할때에 , 이 Json Object 의 소스가 어디인지 주입을 통해 저장할수 있다.
  ```java 
    public class PersonInject {
        public long   id   = 0;
        public String name = null;

        @JacksonInject
        public String source = null;
    }

    --- 

    InjectableValues inject = new InjectableValues.Std().addValue(String.class, "jenkov.com");
    PersonInject personInject = new ObjectMapper().reader(inject)
                            .forType(PersonInject.class)
                            .readValue(new File("data/person.json"));
  ```

 * inject 리더가 지정된 ObjectMapper 를 이용하여 파싱하게 되면 jacksonInject 어노테이션을 확인하여 필드의 타입기반 매칭으로 값을 주입한다.

## @JsonDeserialize
 * JsonDeserialize 는 필드가 커스텀한 Deserializer class를 사용할수 있도록 한다. 
  ```java 
    public class PersonDeserialize {
        public long    id      = 0;
        public String  name    = null;

        @JsonDeserialize(using = OptimizedBooleanDeserializer.class)
        public boolean enabled = false;
    }

    ---

    public class OptimizedBooleanDeserializer extends JsonDeserializer<Boolean> {

      @Override
      public Boolean deserialize(JsonParser jsonParser, DeserializationContext deserializationContext) throws IOException, JsonProcessingException {
        String text = jsonParser.getText();
        if("0".equals(text)) return false;
        return true;
      }      
    }
  ```
 * JsonDeserializer<T t> 를 상속을 통해 구현할때 필드에 맞는 genericType을 지정하여 상속받고, desirialze 메소드를 작성한다.


# Write Annotations (Java Object to Json Object : Serialize)
## @JsonInclude
 * JsonInclude 는 특정한 상황? 상태? 의 필드만 Json Object 변경시 포함되도록 지정할수 있다. 
  ```java 
    @JsonInclude(JsonInclude.Include.NON_EMPTY)
    public class PersonInclude {

        public long  personId = 0;
        public String name     = null;

    }
  ```

 * JsonInclude.Include 는 아래와 같은 include 상수들이 지정되어 있고 NON_EMPTY의 경우 not null과 not empty를 의미한다. java8 Optional 타입의 isAbsent 도 체크한다.
 *  ALWAYS
  , NON_NULL
  , NON_ABSENT
  , NON_EMPTY
  , NON_DEFAULT
  , USE_DEFAULTS

## @JsonGetter
 * @JsonSetter 와 반대로 setter메소드에 지정되며 해당하는 이름으로 Json Object 의 data field 가 생성된다.
  ```java 
    public class PersonGetter {
        private long  personId = 0;

        @JsonGetter("id")
        public long personId() { return this.personId; }

        @JsonSetter("id")
        public void personId(long personId) { this.personId = personId; }
    }
  ```

## @JsonAnyGetter
 * @JsonAnySetter 와 반대 역할로써 Key, Value 형식의 Map을 리턴하는 메소드에 지정함으로써  Json Object의 프로퍼티로 작성되게끔 한다.
  ```java 
    public class PersonAnyGetter {

        private Map<String, Object> properties = new HashMap<>();

        @JsonAnyGetter
        public Map<String, Object> properties() {
            return properties;
        }
    }

    <-->

    {
       "map.key1" : "map.key1.value"
      ,"map.key2" : map.key2.value
    ...
    }
  ```

## @JsonPropertyOrder
 * JsonPropertyOrder는 Java Object가 Json 으로 직렬화 될때 필드의 순서를 지정할 수 있다. (일반적으로 Jackson은 class 에서 필드를 발견하는 순서대록 직렬화 해버림)

  ```java
    @JsonPropertyOrder({"name", "personId"})
    public class PersonPropertyOrder {
        public long  personId  = 0;
        public String name     = null;
    }
  ```

## @JsonRawValue
 * JsonRawValue 는 특정필드에 지정되어, 해당 필드의 값이 raw하게 Json Output이 되도록 할 수 있습니다.
  ```java 
    public class PersonRawValue {
        public long   personId = 0;
        public String address  = "$#";
    }

  ```

 * 일반적으로 문자열은 Json Output 시에 쌍따옴표로 감싸진체 출려됩니다.
  ```java 
    {"personId":0,"address":"$#"}

    --- 
    
    public class PersonRawValue {
        public long   personId = 0;

        @JsonRawValue
        public String address  = "$#";
    }
  ```
@JsonRawValue를 사용하면 쌍따움표 없이 그대로 출력됩니다.
{"personId":0,"address":$#}
하지만, 이것은 잘못된 Json형식입니다.
---
@JsonRawValue 는 Raw Json 문자열을 String 타입변수에 담아 출력할때 사용될수 있습니다.
public class PersonRawValue {

    public long   personId = 0;

    @JsonRawValue
    public String address  =
            "{ \"street\" : \"Wall Street\", \"no\":1}";

}
{"personId":0,"address":{ "street" : "Wall Street", "no":1}}
@JsonValue
-> @JsonValue 를 지정함으로써 Jackson이 객체자체를 직렬화하지 않고 JsonValue 가 지정된 메소드를 호출하는것으로 대체할 수 있습니다.
public class PersonValue {

    public long   personId = 0;
    public String name = null;

    @JsonValue
    public String toJson(){
        return this.personId + "," + this.name;
    }

}
출력 : 
"0,null"
쌍따옴표는 Jackson에 의해 붙여지게 됩니다. 
반환된 문자열내의 쌍따옴표는 모두 이스케이프 처리됩니다.
@JsonSerialize
-> JsonSerialize를 이용하여 특정필드에 커스텀 serialzer를 지정할 수 있다.
public class PersonSerializer {

    public long   personId = 0;
    public String name     = "John";

    @JsonSerialize(using = OptimizedBooleanSerializer.class)
    public boolean enabled = false;
}
public class OptimizedBooleanSerializer extends JsonSerializer<Boolean> {

    @Override
    public void serialize(Boolean aBoolean, JsonGenerator jsonGenerator, 
        SerializerProvider serializerProvider) 
    throws IOException, JsonProcessingException {

        if(aBoolean){
            jsonGenerator.writeNumber(1);
        } else {
            jsonGenerator.writeNumber(0);
        }
    }
}
Jackson 에서 다양한 어노테이션을 지원하기 때문에
적재적소에 잘 사용한다면 보다 간결한 코딩이 가능할것으로 생각된다.
물론, 무분별한 가독성을 해치거나 복잡도를 증가시키는 사용은 피해야겠다.
JsonSerialize/JsonDeserialize 어노테이션을 살펴보니 Type 도 target으로 설정되어있다.
해당 날짜타입의 클래스도 Jackson 레벨에서 특정패턴의 날짜형식으로 처리가 가능할것으로 보인다.
(Spring Converter와 별개로 확인을 위해)

https://circlee7.medium.com/jackson-annotations-19886933a252





모든 jackson 어노테이션 종류 파악
1. 개요
이 글에서는 Jackson Annotations에 대해 자세히 살펴 보겠습니다 .

기존 어노테이션을 사용하는 방법, 사용자 정의 어노테이션을 만드는 방법 및 마지막으로 비활성화하는 방법을 살펴 보겠습니다

2. Jackson 직렬화 어노테이션
먼저 직렬화 어노테이션을 살펴 보겠습니다. 직렬화는 자바를 json현태로 변경하는것을 의미합니다.

2.1. @JsonAnyGetter
@JsonAnyGetter의 어노테이션은 사용의 유연성 수 있도록 지도 표준 속성으로 필드를.

다음은 간단한 예입니다. ExtendableBean 엔티티에는 name 특성과 키 / 값 쌍의 형태로 확장 가능한 속성 세트가 있습니다.

{
    "name":"My bean",
    "attr2":"val2",
    "attr1":"val1"
}
이 엔티티의 인스턴스를 직렬화하면 맵의 모든 키-값이 표준 일반 속성으로 가져옵니다 .

{
    "name":"My bean",
    "attr2":"val2",
    "attr1":"val1"
}
그리고이 엔티티의 직렬화가 실제로 어떻게 보이는지 :

@Test
public void whenSerializingUsingJsonAnyGetter_thenCorrect()
  throws JsonProcessingException {

    ExtendableBean bean = new ExtendableBean("My bean");
    bean.add("attr1", "val1");
    bean.add("attr2", "val2");

    String result = new ObjectMapper().writeValueAsString(bean);

    assertThat(result, containsString("attr1"));
    assertThat(result, containsString("val1"));
}
@JsonAnyGetter () 를 비활성화하기 위해 enabled 옵션 인수 를 false 로 사용할 수도 있습니다 . 이 경우 지도 는 JSON으로 변환되며 직렬화 후 속성 변수 아래에 나타납니다 .

2.2. JsonGetter
@JsonGetter의 어노테이션은의 대안 @JsonProperty의 게터 법 등을 표시하는 어노테이션.

다음 예제에서 – 우리는 getTheName () 메소드 를 MyBean 엔티티 의 name 속성의 getter 메소드로 지정 합니다 .

public class MyBean {
    public int id;
    private String name;

    @JsonGetter("name")
    public String getTheName() {
        return name;
    }
}
그리고 이것이 실제로 작동하는 방법은 다음과 같습니다.

@Test
public void whenSerializingUsingJsonGetter_thenCorrect()
  throws JsonProcessingException {

    MyBean bean = new MyBean(1, "My bean");

    String result = new ObjectMapper().writeValueAsString(bean);

    assertThat(result, containsString("My bean"));
    assertThat(result, containsString("1"));
}
2.3. @JsonPropertyOrder
@JsonPropertyOrder 어노테이션을 사용 하여 직렬화에서 특성 순서 를 지정할 수 있습니다 .

MyBean 엔티티 의 프로퍼티에 대한 커스텀 순서를 설정하자 :

@JsonPropertyOrder({ "name", "id" })
public class MyBean {
    public int id;
    public String name;
}
다음은 직렬화의 출력입니다.

{
    "name":"My bean",
    "id":1
}
그리고 간단한 테스트 :

@Test
public void whenSerializingUsingJsonPropertyOrder_thenCorrect()
  throws JsonProcessingException {

    MyBean bean = new MyBean(1, "My bean");

    String result = new ObjectMapper().writeValueAsString(bean);
    assertThat(result, containsString("My bean"));
    assertThat(result, containsString("1"));
}
@JsonPropertyOrder (alphabetic = true) 를 사용하여 속성을 알파벳순으로 정렬 할 수도 있습니다 . 이 경우 직렬화 출력은 다음과 같습니다.

{
    "id":1,
    "name":"My bean"
}
2.4. @JsonRawValue
@JsonRawValue의 어노테이션을 수행 할 수 있습니다 정확하게는 그대로 속성을 직렬화 잭슨을 지시합니다 .

다음 예제에서는 @JsonRawValue 를 사용 하여 일부 사용자 정의 JSON을 엔티티 값으로 임베드합니다.

public class RawBean {
    public String name;

    @JsonRawValue
    public String json;
}
엔티티를 직렬화 한 결과는 다음과 같습니다.

{
    "name":"My bean",
    "json":{
        "attr":false
    }
}
그리고 간단한 테스트 :

@Test
public void whenSerializingUsingJsonRawValue_thenCorrect()
  throws JsonProcessingException {

    RawBean bean = new RawBean("My bean", "{\"attr\":false}");

    String result = new ObjectMapper().writeValueAsString(bean);
    assertThat(result, containsString("My bean"));
    assertThat(result, containsString("{\"attr\":false}"));
}
이 어노테이션의 활성화 여부를 정의 하는 선택적 부울 인수 값 을 사용할 수도 있습니다 .

2.5. JsonValue
@JsonValue 는 라이브러리가 전체 인스턴스를 직렬화하는 데 사용할 단일 메소드를 나타냅니다.

예를 들어, 열거, 우리는 어노테이션 getName 와 @JsonValue을 그래서 그러한 단체가 그 이름을 통해 연재된다 :

public enum TypeEnumWithValue {
    TYPE1(1, "Type A"), TYPE2(2, "Type 2");

    private Integer id;
    private String name;

    // standard constructors

    @JsonValue
    public String getName() {
        return name;
    }
}
우리의 테스트 :

@Test
public void whenSerializingUsingJsonValue_thenCorrect()
  throws JsonParseException, IOException {

    String enumAsString = new ObjectMapper()
      .writeValueAsString(TypeEnumWithValue.TYPE1);

    assertThat(enumAsString, is(""Type A""));
}
2.6. @JsonRootName
@JsonRootName의 어노테이션이 사용됩니다 - 포장이 사용 가능한 경우 - 사용되는 루트 래퍼의 이름을 지정할 수 있습니다.

랩핑은 사용자 를 다음과 같이 직렬화하는 대신 다음을 의미합니다 .

{
    "id": 1,
    "name": "John"
}
다음과 같이 포장됩니다.

{
    "User": {
        "id": 1,
        "name": "John"
    }
}
예제를 보자 . @JsonRootName 어노테이션을 사용 하여이 잠재적 래퍼 엔티티의 이름을 표시 할 것이다 .

@JsonRootName(value = "user")
public class UserWithRoot {
    public int id;
    public String name;
}
기본적으로 랩퍼 이름은 UserWithRoot 클래스 이름입니다 .어노테이션을 사용하면 더 깔끔한 사용자를 얻을 수 있습니다.

@Test
public void whenSerializingUsingJsonRootName_thenCorrect()
  throws JsonProcessingException {

    UserWithRoot user = new User(1, "John");

    ObjectMapper mapper = new ObjectMapper();
    mapper.enable(SerializationFeature.WRAP_ROOT_VALUE);
    String result = mapper.writeValueAsString(user);

    assertThat(result, containsString("John"));
    assertThat(result, containsString("user"));
}
다음은 직렬화의 출력입니다.

{
    "user":{
        "id":1,
        "name":"John"
    }
}
Jackson 2.4부터는 XML과 같은 데이터 형식과 함께 사용할 수 있는 새로운 선택적 인수 네임 스페이스 를 사용할 수 있습니다. 추가하면 정규화 된 이름의 일부가됩니다.

@JsonRootName(value = "user", namespace="users")
public class UserWithRootNamespace {
    public int id;
    public String name;

    // ...
}
XmlMapper로 직렬화 하면 출력은 다음과 같습니다.

<user xmlns="users">
    <id xmlns="">1</id>
    <name xmlns="">John</name>
    <items xmlns=""/>
</user>
2.7. @JsonSerialize
@JsonSerialize 는 엔터티를 마샬링 할 때 사용할 사용자 지정 serializer를 나타냅니다 .

간단한 예를 봅시다. 우리가 사용하는거야 @JsonSerialize을 직렬화 EVENTDATE의 A의 특성 CustomDateSerializer를 :

public class EventWithSerializer {
    public String name;

    @JsonSerialize(using = CustomDateSerializer.class)
    public Date eventDate;
}
간단한 사용자 지정 Jackson 직렬 변환기는 다음과 같습니다.

public class CustomDateSerializer extends StdSerializer<Date> {

    private static SimpleDateFormat formatter 
      = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");

    public CustomDateSerializer() { 
        this(null); 
    } 

    public CustomDateSerializer(Class<Date> t) {
        super(t); 
    }

    @Override
    public void serialize(
      Date value, JsonGenerator gen, SerializerProvider arg2) 
      throws IOException, JsonProcessingException {
        gen.writeString(formatter.format(value));
    }
}
테스트에서 이것을 사용하자 :

@Test
public void whenSerializingUsingJsonSerialize_thenCorrect()
  throws JsonProcessingException, ParseException {

    SimpleDateFormat df
      = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");

    String toParse = "20-12-2014 02:30:00";
    Date date = df.parse(toParse);
    EventWithSerializer event = new EventWithSerializer("party", date);

    String result = new ObjectMapper().writeValueAsString(event);
    assertThat(result, containsString(toParse));
}
3. Jackson Deserialization 어노테이션
다음으로 Jackson deserialization 어노테이션을 살펴 보겠습니다.

3.1. JsonCreator
@JsonCreator 어노테이션을 사용하여 역 직렬화에 사용되는 생성자 / 공장을 조정할 수 있습니다 .

필요한 대상 엔터티와 정확히 일치하지 않는 일부 JSON을 역 직렬화해야 할 때 매우 유용합니다.

예를 보자. 다음 JSON을 역 직렬화해야한다고 가정 해보십시오.

{
    "id":1,
    "theName":"My bean"
}
그러나,이없는 theName의 목표 기업에 대한 필드 - 만이 이름 필드. 이제 엔티티 자체를 변경하고 싶지 않습니다. @JsonCreator로 생성자에 어노테이션을 달고 @JsonProperty 어노테이션을 사용하여 비 정렬 화 프로세스를 조금 더 제어하면 됩니다.

public class BeanWithCreator {
    public int id;
    public String name;

    @JsonCreator
    public BeanWithCreator(
      @JsonProperty("id") int id, 
      @JsonProperty("theName") String name) {
        this.id = id;
        this.name = name;
    }
}
이것을 실제로 보자.

@Test
public void whenDeserializingUsingJsonCreator_thenCorrect()
  throws IOException {

    String json = "{\"id\":1,\"theName\":\"My bean\"}";

    BeanWithCreator bean = new ObjectMapper()
      .readerFor(BeanWithCreator.class)
      .readValue(json);
    assertEquals("My bean", bean.name);
}
3.2. @JacksonInject
@JacksonInject 는 속성이 JSON 데이터가 아니라 주입에서 값을 가져옴을 나타냅니다.

다음 예에서 – @JacksonInject 를 사용하여 속성 ID 를 주입합니다 .

public class BeanWithInject {
    @JacksonInject
    public int id;

    public String name;
}
그리고 이것이 작동하는 방법은 다음과 같습니다.

@Test
public void whenDeserializingUsingJsonInject_thenCorrect()
  throws IOException {

    String json = "{\"name\":\"My bean\"}";

    InjectableValues inject = new InjectableValues.Std()
      .addValue(int.class, 1);
    BeanWithInject bean = new ObjectMapper().reader(inject)
      .forType(BeanWithInject.class)
      .readValue(json);

    assertEquals("My bean", bean.name);
    assertEquals(1, bean.id);
}
3.3. JsonAnySetter
@JsonAnySetter 는 Map 을 표준 속성으로 사용할 수있는 유연성을 제공합니다. 직렬화 해제시 JSON의 특성이 단순히 맵에 추가됩니다.

이것이 어떻게 작동하는지 봅시다 – @JsonAnySetter 를 사용 하여 엔터티 ExtendableBean 을 직렬화 해제합니다 :

public class ExtendableBean {
    public String name;
    private Map<String, String> properties;

    @JsonAnySetter
    public void add(String key, String value) {
        properties.put(key, value);
    }
}
이것은 직렬화 해제해야하는 JSON입니다.

{
    "name":"My bean",
    "attr2":"val2",
    "attr1":"val1"
}
그리고 이것이 어떻게이 모든 것이 함께 묶여 있는지를 보여줍니다 :

@Test
public void whenDeserializingUsingJsonAnySetter_thenCorrect()
  throws IOException {
    String json
      = "{\"name\":\"My bean\",\"attr2\":\"val2\",\"attr1\":\"val1\"}";

    ExtendableBean bean = new ObjectMapper()
      .readerFor(ExtendableBean.class)
      .readValue(json);

    assertEquals("My bean", bean.name);
    assertEquals("val2", bean.getProperties().get("attr2"));
}
3.4. JsonSetter
@JsonSetter는 대안이다 @JsonProperty - 세터 방법과 그 표시 방법.

JSON 데이터를 읽어야하지만 대상 엔터티 클래스가 해당 데이터 와 정확히 일치하지 않으므로 프로세스를 조정하여 적합하도록해야합니다.

다음 예제에서는 MyBean 엔티티 에서 s etTheName () 메소드를 name 특성 의 setter로 지정합니다 .

public class MyBean {
    public int id;
    private String name;

    @JsonSetter("name")
    public void setTheName(String name) {
        this.name = name;
    }
}
이제 JSON 데이터를 언 마샬링해야 할 때 완벽하게 작동합니다.

@Test
public void whenDeserializingUsingJsonSetter_thenCorrect()
  throws IOException {

    String json = "{\"id\":1,\"name\":\"My bean\"}";

    MyBean bean = new ObjectMapper()
      .readerFor(MyBean.class)
      .readValue(json);
    assertEquals("My bean", bean.getTheName());
}
3.5. JsonDeserialize
@JsonDeserialize 는 사용자 지정 디시리얼라이저 사용을 나타냅니다.

이것이 어떻게 나타나는지 보자. @JsonDeserialize 를 사용 하여 CustomDateDeserializer 와 함께 eventDate 속성 을 직렬화 해제합니다 .

public class EventWithSerializer {
    public String name;

    @JsonDeserialize(using = CustomDateDeserializer.class)
    public Date eventDate;
}
다음은 사용자 지정 디시리얼라이저입니다.

public class CustomDateDeserializer
  extends StdDeserializer<Date> {

    private static SimpleDateFormat formatter
      = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");

    public CustomDateDeserializer() { 
        this(null); 
    } 

    public CustomDateDeserializer(Class<?> vc) { 
        super(vc); 
    }

    @Override
    public Date deserialize(
      JsonParser jsonparser, DeserializationContext context) 
      throws IOException {

        String date = jsonparser.getText();
        try {
            return formatter.parse(date);
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
    }
}
그리고 연속 테스트는 다음과 같습니다.

@Test
public void whenDeserializingUsingJsonDeserialize_thenCorrect()
  throws IOException {

    String json
      = "{"name":"party","eventDate":"20-12-2014 02:30:00"}";

    SimpleDateFormat df
      = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
    EventWithSerializer event = new ObjectMapper()
      .readerFor(EventWithSerializer.class)
      .readValue(json);

    assertEquals(
      "20-12-2014 02:30:00", df.format(event.eventDate));
}
3.6 @JsonAlias
@JsonAlias는 정의 직렬화하는 동안 속성에 대한 하나 이상의 대체 이름을 .

이 어노테이션이 간단한 예에서 어떻게 작동하는지 봅시다 :

public class AliasBean {
    @JsonAlias({ "fName", "f_name" })
    private String firstName;   
    private String lastName;
}
여기에는 POJO가 있으며 fName , f_name 및 firstName 과 같은 값을 사용하여 JSON을 POJO 의 firstName 변수에 직렬화 해제하려고합니다 .

그리고이 어노테이션이 예상대로 작동하는지 확인하는 테스트가 있습니다.

@Test
public void whenDeserializingUsingJsonAlias_thenCorrect() throws IOException {
    String json = "{\"fName\": \"John\", \"lastName\": \"Green\"}";
    AliasBean aliasBean = new ObjectMapper().readerFor(AliasBean.class).readValue(json);
    assertEquals("John", aliasBean.getFirstName());
}
4. Jackson Property Inclusion Annotations
4.1. JsonIgnoreProperties
@JsonIgnoreProperties 는 Jackson이 무시할 속성 또는 속성 목록을 표시하는 클래스 수준 어노테이션입니다.

직렬화 에서 속성 ID 를 무시하는 간단한 예를 살펴 보겠습니다 .

@JsonIgnoreProperties({ "id" })
public class BeanWithIgnore {
    public int id;
    public String name;
}
다음은 무시가 발생하는지 확인하는 테스트입니다.

@Test
public void whenSerializingUsingJsonIgnoreProperties_thenCorrect()
  throws JsonProcessingException {

    BeanWithIgnore bean = new BeanWithIgnore(1, "My bean");

    String result = new ObjectMapper()
      .writeValueAsString(bean);

    assertThat(result, containsString("My bean"));
    assertThat(result, not(containsString("id")));
}
예외없이 JSON 입력에서 알 수없는 속성을 무시하기 위해 @JsonIgnoreProperties 어노테이션 의 ignoreUnknown = true 를 설정할 수 있습니다 .

4.2. JsonIgnore
@JsonIgnore의 어노테이션 필드 레벨에서 무시 될 수있는 속성을 표시하는 데 사용됩니다.

직렬화 에서 속성 ID 를 무시하기 위해 @JsonIgnore 를 사용 하십시오 .

public class BeanWithIgnore {
    @JsonIgnore
    public int id;

    public String name;
}
그리고 테스트는 id 가 성공적으로 무시 되었는지확인합니다.

@Test
public void whenSerializingUsingJsonIgnore_thenCorrect()
  throws JsonProcessingException {

    BeanWithIgnore bean = new BeanWithIgnore(1, "My bean");

    String result = new ObjectMapper()
      .writeValueAsString(bean);

    assertThat(result, containsString("My bean"));
    assertThat(result, not(containsString("id")));
}
4.3. @JsonIgnoreType
@JsonIgnoreType 은 어노테이션이있는 유형의 모든 특성이 무시 되도록 표시합니다.

어노테이션을 사용하여 Name 유형의 모든 속성을 무시 하도록 표시합시다 .

public class User {
    public int id;
    public Name name;

    @JsonIgnoreType
    public static class Name {
        public String firstName;
        public String lastName;
    }
}
무시가 올바르게 작동하는지 확인하는 간단한 테스트는 다음과 같습니다.

@Test
public void whenSerializingUsingJsonIgnoreType_thenCorrect()
  throws JsonProcessingException, ParseException {

    User.Name name = new User.Name("John", "Doe");
    User user = new User(1, name);

    String result = new ObjectMapper()
      .writeValueAsString(user);

    assertThat(result, containsString("1"));
    assertThat(result, not(containsString("name")));
    assertThat(result, not(containsString("John")));
}
4.4. @JsonInclude
@JsonInclude 를 사용 하여 비어 있거나 null / 기본값 인 속성을 제외 할 수 있습니다 .

직렬화에서 null을 제외하는 예를 살펴 보겠습니다.

@JsonInclude(Include.NON_NULL)
public class MyBean {
    public int id;
    public String name;
}
전체 테스트는 다음과 같습니다.

public void whenSerializingUsingJsonInclude_thenCorrect()
  throws JsonProcessingException {

    MyBean bean = new MyBean(1, null);

    String result = new ObjectMapper()
      .writeValueAsString(bean);

    assertThat(result, containsString("1"));
    assertThat(result, not(containsString("name")));
}
4.5. @JsonAutoDetect
@JsonAutoDetect 는 속성이 표시되고 표시되지 않는 기본 의미를 무시할 수 있습니다 .

간단한 예제를 통해 어노테이션이 어떻게 도움이되는지 살펴 보겠습니다. 개인 속성 직렬화를 활성화 해 보겠습니다.

@JsonAutoDetect(fieldVisibility = Visibility.ANY)
public class PrivateBean {
    private int id;
    private String name;
}
그리고 테스트 :

@Test
public void whenSerializingUsingJsonAutoDetect_thenCorrect()
  throws JsonProcessingException {

    PrivateBean bean = new PrivateBean(1, "My bean");

    String result = new ObjectMapper()
      .writeValueAsString(bean);

    assertThat(result, containsString("1"));
    assertThat(result, containsString("My bean"));
}
5. Jackson 다형성 유형 처리 어노테이션
다음으로 Jackson 다형성 유형 처리 어노테이션을 살펴 보겠습니다.

@JsonTypeInfo

– 직렬화에 포함 할 유형 정보의 세부 사항을 나타냅니다.

@JsonSubTypes

– 어노테이션이 달린 유형의 하위 유형을 나타냅니다.

@JsonTypeName

– 어노테이션이있는 클래스에 사용할 논리 유형 이름을 정의합니다.

더 복잡한 예제를보고 @JsonTypeInfo , @JsonSubTypes 및 @JsonTypeName 세 가지를 모두 사용 하여 엔티티 Zoo 를 직렬화 / 직렬화하십시오 .

public class Zoo {
    public Animal animal;

    @JsonTypeInfo(
      use = JsonTypeInfo.Id.NAME, 
      include = As.PROPERTY, 
      property = "type")
    @JsonSubTypes({
        @JsonSubTypes.Type(value = Dog.class, name = "dog"),
        @JsonSubTypes.Type(value = Cat.class, name = "cat")
    })
    public static class Animal {
        public String name;
    }

    @JsonTypeName("dog")
    public static class Dog extends Animal {
        public double barkVolume;
    }

    @JsonTypeName("cat")
    public static class Cat extends Animal {
        boolean likesCream;
        public int lives;
    }
}
직렬화를 수행 할 때 :

@Test
public void whenSerializingPolymorphic_thenCorrect()
  throws JsonProcessingException {
    Zoo.Dog dog = new Zoo.Dog("lacy");
    Zoo zoo = new Zoo(dog);

    String result = new ObjectMapper()
      .writeValueAsString(zoo);

    assertThat(result, containsString("type"));
    assertThat(result, containsString("dog"));
}
Zoo 인스턴스를 Dog로 직렬화하면 다음 과 같은 결과가 발생합니다.

{
    "animal": {
        "type": "dog",
        "name": "lacy",
        "barkVolume": 0
    }
}
이제 직렬화 해제를 위해 다음 JSON 입력으로 시작하겠습니다.

{
    "animal":{
        "name":"lacy",
        "type":"cat"
    }
}
그리고 이것이 어떻게 Zoo 인스턴스에 비 정렬 화되는지 봅시다 :

@Test
public void whenDeserializingPolymorphic_thenCorrect()
throws IOException {
    String json = "{\"animal\":{\"name\":\"lacy\",\"type\":\"cat\"}}";

    Zoo zoo = new ObjectMapper()
      .readerFor(Zoo.class)
      .readValue(json);

    assertEquals("lacy", zoo.animal.name);
    assertEquals(Zoo.Cat.class, zoo.animal.getClass());
}
6. Jackson 기본적인 어노테이션
다음은 Jackson의 일반적인 어노테이션에 대해 설명하겠습니다.

6.1. JsonProperty
@JsonProperty 어노테이션을 추가 하여 JSON으로 특성 이름을 표시 할 수 있습니다 .

비표준 게터 및 세터를 처리 할 때 @JsonProperty 를 사용 하여 속성 이름 을 직렬화 / 직렬화합니다 .

public class MyBean {
    public int id;
    private String name;

    @JsonProperty("name")
    public void setTheName(String name) {
        this.name = name;
    }

    @JsonProperty("name")
    public String getTheName() {
        return name;
    }
}
우리의 테스트 :

@Test
public void whenUsingJsonProperty_thenCorrect()
  throws IOException {
    MyBean bean = new MyBean(1, "My bean");

    String result = new ObjectMapper().writeValueAsString(bean);

    assertThat(result, containsString("My bean"));
    assertThat(result, containsString("1"));

    MyBean resultBean = new ObjectMapper()
      .readerFor(MyBean.class)
      .readValue(result);
    assertEquals("My bean", resultBean.getTheName());
}
6.2. JsonFormat
@JsonFormat의 날짜 / 시간 값을 직렬화 할 때 어노테이션은 형식을 지정합니다.

다음 예제에서 – @JsonFormat 을 사용 하여 eventDate 속성의 형식을 제어합니다 .

public class EventWithFormat {
    public String name;

    @JsonFormat(
      shape = JsonFormat.Shape.STRING,
      pattern = "dd-MM-yyyy hh:mm:ss")
    public Date eventDate;
}
그리고 여기 테스트가 있습니다 :

@Test
public void whenSerializingUsingJsonFormat_thenCorrect()
  throws JsonProcessingException, ParseException {
    SimpleDateFormat df = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
    df.setTimeZone(TimeZone.getTimeZone("UTC"));

    String toParse = "20-12-2014 02:30:00";
    Date date = df.parse(toParse);
    EventWithFormat event = new EventWithFormat("party", date);

    String result = new ObjectMapper().writeValueAsString(event);

    assertThat(result, containsString(toParse));
}
6.3. JsonUnwrapped
@JsonUnwrapped 는 직렬화 / 역 직렬화 될 때 랩핑 해제 / 플랫 닝해야하는 값을 정의합니다.

그것이 어떻게 작동하는지 정확히 보자. 우리는 속성 이름 을 풀기 위해 어노테이션을 사용할 것입니다 :

public class UnwrappedUser {
    public int id;

    @JsonUnwrapped
    public Name name;

    public static class Name {
        public String firstName;
        public String lastName;
    }
}
이제이 클래스의 인스턴스를 직렬화 해 봅시다 :

@Test
public void whenSerializingUsingJsonUnwrapped_thenCorrect()
  throws JsonProcessingException, ParseException {
    UnwrappedUser.Name name = new UnwrappedUser.Name("John", "Doe");
    UnwrappedUser user = new UnwrappedUser(1, name);

    String result = new ObjectMapper().writeValueAsString(user);

    assertThat(result, containsString("John"));
    assertThat(result, not(containsString("name")));
}
출력의 모양은 다음과 같습니다. 정적 중첩 클래스의 필드는 다른 필드와 함께 래핑되지 않습니다.

{
    "id":1,
    "firstName":"John",
    "lastName":"Doe"
}
6.4. JsonView
@JsonView 는 직렬화 / 역 직렬화에 속성이 포함될 뷰를 나타냅니다.

예제는 그 작동 방식을 정확하게 보여줍니다. @JsonView 를 사용 하여 Item 엔터티 의 인스턴스를 serialize합니다 .

뷰부터 시작하겠습니다 :

 public class Views {
    public static class Public {}
    public static class Internal extends Public {}
}
이제 뷰를 사용하여 Item 엔터티가 있습니다.

public class Item {
    @JsonView(Views.Public.class)
    public int id;

    @JsonView(Views.Public.class)
    public String itemName;

    @JsonView(Views.Internal.class)
    public String ownerName;
}
마지막으로 – 전체 테스트 :

@Test
public void whenSerializingUsingJsonView_thenCorrect()
  throws JsonProcessingException {
    Item item = new Item(2, "book", "John");

    String result = new ObjectMapper()
      .writerWithView(Views.Public.class)
      .writeValueAsString(item);

    assertThat(result, containsString("book"));
    assertThat(result, containsString("2"));
    assertThat(result, not(containsString("John")));
}
6.5. @JsonManagedReference, @JsonBackReference
@JsonManagedReference 및 @JsonBackReference 어노테이션 부모 / 자식 관계를 처리 할 수있는 루프 주변 일을.

다음 예제에서 – @JsonManagedReference 및 @JsonBackReference 를 사용 하여 ItemWithRef 엔티티 를 직렬화합니다 .

public class ItemWithRef {
    public int id;
    public String itemName;

    @JsonManagedReference
    public UserWithRef owner;
}
우리의 UserWithRef 엔티티 :

public class UserWithRef {
    public int id;
    public String name;

    @JsonBackReference
    public List<ItemWithRef> userItems;
}
그리고 테스트 :

@Test
public void whenSerializingUsingJacksonReferenceAnnotation_thenCorrect()
  throws JsonProcessingException {
    UserWithRef user = new UserWithRef(1, "John");
    ItemWithRef item = new ItemWithRef(2, "book", user);
    user.addItem(item);

    String result = new ObjectMapper().writeValueAsString(item);

    assertThat(result, containsString("book"));
    assertThat(result, containsString("John"));
    assertThat(result, not(containsString("userItems")));
}
6.6. JsonIdentityInfo
@JsonIdentityInfo 는 값을 직렬화 / 역 직렬화 할 때 (예 : 무한 재귀 유형의 문제를 처리 할 때) 개체 ID를 사용해야 함을 나타냅니다.

다음 예에서 - 우리는이 ItemWithIdentity의 와 엔티티 와 양방향 관계 UserWithIdentity의 실체를 :

@JsonIdentityInfo(
  generator = ObjectIdGenerators.PropertyGenerator.class,
  property = "id")
public class ItemWithIdentity {
    public int id;
    public String itemName;
    public UserWithIdentity owner;
}
그리고 UserWithIdentity 엔티티 :

@JsonIdentityInfo(
  generator = ObjectIdGenerators.PropertyGenerator.class,
  property = "id")
public class UserWithIdentity {
    public int id;
    public String name;
    public List<ItemWithIdentity> userItems;
}
이제 무한 재귀 문제가 어떻게 처리되는지 봅시다 :

@Test
public void whenSerializingUsingJsonIdentityInfo_thenCorrect()
  throws JsonProcessingException {
    UserWithIdentity user = new UserWithIdentity(1, "John");
    ItemWithIdentity item = new ItemWithIdentity(2, "book", user);
    user.addItem(item);

    String result = new ObjectMapper().writeValueAsString(item);

    assertThat(result, containsString("book"));
    assertThat(result, containsString("John"));
    assertThat(result, containsString("userItems"));
}
다음은 직렬화 된 항목 및 사용자의 전체 출력입니다.

{
    "id": 2,
    "itemName": "book",
    "owner": {
        "id": 1,
        "name": "John",
        "userItems": [
            2
        ]
    }
}
6.7. JsonFilter
@JsonFilter의 어노테이션 직렬화시에 사용하는 필터를 지정합니다.

예를 보자. 먼저 엔터티를 정의하고 필터를 가리 킵니다.

@JsonFilter("myFilter")
public class BeanWithFilter {
    public int id;
    public String name;
}
이제 전체 테스트에서 이름 을 제외한 다른 모든 속성 을 직렬화에서 제외하는 필터를 정의합니다 .

@Test
public void whenSerializingUsingJsonFilter_thenCorrect()
  throws JsonProcessingException {
    BeanWithFilter bean = new BeanWithFilter(1, "My bean");

    FilterProvider filters 
      = new SimpleFilterProvider().addFilter(
        "myFilter", 
        SimpleBeanPropertyFilter.filterOutAllExcept("name"));

    String result = new ObjectMapper()
      .writer(filters)
      .writeValueAsString(bean);

    assertThat(result, containsString("My bean"));
    assertThat(result, not(containsString("id")));
}
7. Custom Jackson 어노테이션
다음으로, 사용자 정의 Jackson 어노테이션을 작성하는 방법을 살펴 보겠습니다. @JacksonAnnotationsInside 어노테이션 을 사용할 수 있습니다 .

@Retention(RetentionPolicy.RUNTIME)
    @JacksonAnnotationsInside
    @JsonInclude(Include.NON_NULL)
    @JsonPropertyOrder({ "name", "id", "dateCreated" })
    public @interface CustomAnnotation {}
이제 엔티티에 새로운 어노테이션을 사용하면 :

@CustomAnnotation
public class BeanWithCustomAnnotation {
    public int id;
    public String name;
    public Date dateCreated;
}
기존 어노테이션을 단축형으로 사용할 수있는 더 간단한 사용자 정의 어노테이션으로 결합하는 방법을 볼 수 있습니다.

@Test
public void whenSerializingUsingCustomAnnotation_thenCorrect()
  throws JsonProcessingException {
    BeanWithCustomAnnotation bean 
      = new BeanWithCustomAnnotation(1, "My bean", null);

    String result = new ObjectMapper().writeValueAsString(bean);

    assertThat(result, containsString("My bean"));
    assertThat(result, containsString("1"));
    assertThat(result, not(containsString("dateCreated")));
}
직렬화 프로세스의 출력 :

{
    "name":"My bean",
    "id":1
}
8. Jackson MixIn 어노테이션
다음 – Jackson MixIn 어노테이션을 사용하는 방법을 살펴 보겠습니다.

예를 들어 User 유형의 속성을 무시하기 위해 MixIn 어노테이션을 사용합니다 .

public class Item {
    public int id;
    public String itemName;
    public User owner;
}

@JsonIgnoreType
public class MyMixInForIgnoreType {}
이것을 실제로 보자.

@Test
public void whenSerializingUsingMixInAnnotation_thenCorrect() 
  throws JsonProcessingException {
    Item item = new Item(1, "book", null);

    String result = new ObjectMapper().writeValueAsString(item);
    assertThat(result, containsString("owner"));

    ObjectMapper mapper = new ObjectMapper();
    mapper.addMixIn(User.class, MyMixInForIgnoreType.class);

    result = mapper.writeValueAsString(item);
    assertThat(result, not(containsString("owner")));
}
9. Jackson Annotation 비활성화
마지막으로 모든 Jackson 어노테이션 을 비활성화하는 방법을 알아 보겠습니다. MapperFeature 를 비활성화하면 됩니다. 다음 예제와 같이 USE_ANNOTATIONS :

@JsonInclude(Include.NON_NULL)
@JsonPropertyOrder({ "name", "id" })
public class MyBean {
    public int id;
    public String name;
}
이제 어노테이션을 비활성화 한 후에는 아무런 효과가 없으며 라이브러리의 기본값이 적용됩니다.

@Test
public void whenDisablingAllAnnotations_thenAllDisabled()
  throws IOException {
    MyBean bean = new MyBean(1, null);

    ObjectMapper mapper = new ObjectMapper();
    mapper.disable(MapperFeature.USE_ANNOTATIONS);
    String result = mapper.writeValueAsString(bean);

    assertThat(result, containsString("1"));
    assertThat(result, containsString("name"));
}
어노테이션을 비활성화하기 전의 직렬화 결과 :

{"id":1}
어노테이션을 비활성화 한 후 직렬화의 결과 :

{
    "id":1,
    "name":null
}
10. 결론
이 튜토리얼은 Jackson 어노테이션에 대해 심도있게 다루었으며, 어노테이션을 올바르게 사용할 수있는 유연성의 표면을 긁어 냈습니다.

이 모든 예제와 코드 스 니펫의 구현은 GitHub 프로젝트 에서 찾을 수 있습니다. 이 프로젝트 는 Maven 기반 프로젝트이므로 가져 와서 쉽게 실행할 수 있어야합니다.

참고

https://www.baeldung.com/jackson-annotations#1-jsonignoreproperties