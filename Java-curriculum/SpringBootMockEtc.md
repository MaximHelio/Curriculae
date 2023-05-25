# Spring Boot - Mock JSON

src/main/resources/json/User.json
```json
{
    "name": "홍길동",
    "age": 39
}
```

src/main/resources/json/Users.json
```json
[
    {
        "name": "홍길동",
        "age": 39
    },
    {
        "name": "김삼순",
        "age": 33
    },
    {
        "name": "홍명보",
        "age": 44
    },
    {
        "name": "박지삼",
        "age": 22
    },
    {
        "name": "권명순",
        "age": 10
    }
]
```

<!--
## Gson
src/test/java/패키지/{프로젝트명}Tests.java
```java
import java.lang.reflect.Type;
```
```java
@Test
void contextLoads() {
    User user = getMockJSON("json/User.json", User.class);
    System.out.println(user);

    List<User> users = getMockJSON(
            "json/Users.json",
            new TypeToken<List<User>>(){}.getType()
    );
    System.out.println(users);
}

static <T>T getMockJSON(String filePath, Type type) {
    try {
        ClassPathResource resource = new ClassPathResource(filePath);
        Path path = Paths.get(resource.getURI());
        Reader reader = new FileReader(path.toString());
        Gson gson = new Gson();
        return gson.fromJson(reader, type);
    } catch (Exception exception) {
        exception.printStackTrace();
        return null;
    }
}
```
-->

## Jackson
src/test/java/패키지/{프로젝트명}Tests.java
```java
static Path getResource(String filePath) {
    try {
        ClassPathResource resource = new ClassPathResource(filePath);
        return Paths.get(resource.getURI());
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

static String getMockJSON(String filePath) {
    try {
        return new String(Files.readAllBytes(getResource(filePath)));
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

static <T>T getMockJSON(String filePath, Class<T> valueType) {
    ObjectMapper mapper = new ObjectMapper();
    try {
        return mapper.readValue(new File(getResource(filePath).toUri()), valueType);
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

static <T>T getMockJSON(String filePath, TypeReference<T> valueTypeRef) {
    ObjectMapper mapper = new ObjectMapper();
    try {
        return mapper.readValue(new File(getResource(filePath).toUri()), valueTypeRef);
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

@Test
void testUser() {
    String jsonUser = getMockJSON("json/User.json");
    System.out.println(jsonUser);

    User user = getMockJSON("json/User.json", User.class);
    System.out.println(user);

    List<User> users = getMockJSON(
            "json/Users.json",
            new TypeReference<List<User>>(){}
    );
    System.out.println(users);
}
```
* ❕ `Model`의 `멤버 변수`들이 `private`일 경우, `public 빈 생성자` 또는 `get`, `set` 메서드가 있어야 한다.
* 또는 `mapper.setVisibility(PropertyAccessor.FIELD, JsonAutoDetect.Visibility.ANY);`
<!--
Map<String, String> 변경하는 방법
Map<String, String> map = mapper.convertValue(object, HashMap.class);
-->

### Java Lambda Expression
```java
users.forEach((User user) ->
    System.out.println(user)
);

users.forEach(user -> {
    System.out.println(user);
});
```
* `Python`의 `lambda` 형식으로 실행 가능

## Lombok 어노테이션
* `@ToString(exclude = "password")` toString()에서 password만 제외 시킨다.
* `@NoArgsConstructor` 파라미터가 없는 기본 생성자를 만들어 준다.
* `@AllArgsConstructor` 모든 필드를 파라미터로 받는 생성자를 만들어 준다.
* `@RequiredArgsConstructor` final 또는 @NonNull 필드만 파라미터로 받는 생성자를 만들어 준다.
* `@EqualsAndHashCode` equals(내용이 동일), hashCode(동일 객체) 함수를 만들어준다.
* `@EqualsAndHashCode(callSuper = true)` 부모 클래스 필드도 동일한지 비교한다.
* `@Data` @Getter, @Setter, @RequiredArgsConstructor, @ToString, @EqualsAndHashCode 한번에 설정 해준다.

### @Builder
```java
@Data
@Builder
public static class User {
    private String name;
    @Builder.Default
    private int age = 39;
}

@Test
void testBuilder() {
    User user = User.builder().name("홍길동").build();
    System.out.println(user);
}
```

### @SuperBuilder
```java
@SuperBuilder
public static class Parent {
    private String p1;
}

@SuperBuilder
public static class Child extends Parent {
    private String c1;
}

@Test
void testBuilder() {
    Child child = Child.builder().p1("p1").c1("c1").build();
    System.out.println(child);
}
```
* ❕ `@SuperBuilder` 대신 `@Builder`를 사용하면 .p1("p1") 사용할 수 없다.

### 예시: UsersResult
src/main/resources/json/UsersResult.json
```json
{
    "RESULT": {
        "USERS": [
            {
                "NAME": "홍길동",
                "AGE": 39
            },
            {
                "NAME": "김삼순",
                "AGE": 33
            }
        ]
    }
}
```

src/test/java/패키지/{프로젝트명}Tests.java
```java
@Data
public static class UsersResult {
    @JsonAlias("RESULT")
    private UserList result;

    @Data
    public static class UserList {
        @JsonAlias("USERS")
        private List<User> users;
    }

    @Data
    public static class User {
        @JsonAlias("NAME")
        private String name;
        @JsonAlias("AGE")
        private int age;
        private String from;
    }
}

@Test
void testUsersResult() {
    UsersResult users = getMockJSON("json/UsersResult.json", UsersResult.class);
    users.getResult().getUsers().stream().forEach(user -> user.setFrom("Korea"));
    System.out.println(users);
}
```

# ETC
## HashMap
### 키가 없을 경우 기본값 받기
```java
Map<String, Object> map = new HashMap<>();
// map.put("aB", null);
Integer aB = Integer.parseInt(map.getOrDefault("aB", "0").toString());
```
* ❕ `ab`키에 값`null`이 들어가 있으면 `getOrDefault`의 "0" 값이 적용 되지 않는다.
* ❕ `ab`키가 없을 경우만 적용

## LocalDateTime
### String 변환 LocalDateTime
```java
// LocalDateTime approvDatTime = LocalDateTime.parse("2022-01-01T00:00:01");
LocalDateTime localDateTime = LocalDateTime.parse("20220101000001", DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
String formatDateTime = LocalDateTime.from(localDateTime).format(DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
// String formatDateTimeNow = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
```

## Mybatis
### foreach
```java
@RequestParam String[] categories
```
```xml
<select id="read" resultType="Product">
    select * from products
    where 1 = 1
    <if test="categories != null and categories.length > 0">
        and category in
        <foreach collection="categories" item="category" index="index" separator=", " open="(" close=")">
            #{category}
        </foreach>
    </if>
</select>
```

## 오류들
### Spring
#### @org.springframework.beans.factory.annotation.Autowired(required=true)
* pom.xml 파일에 `라이브러리`를 추가 하지 않았거나, `@Service` 어노테이션을 붙이지 않았을때 발생

### Http
#### HttpMediaTypeNotSupportedException :: 415 status code
* https://blog.naver.com/writer0713/221853596497
```java
@RequestMapping(path = "", method = RequestMethod.POST)
public void usersCreate(@RequestBody MultiValueMap<String, Object> map) {
}
```
* `MultiValueMap`은 `Content type 'application/x-www-form-urlencoded;charset=UTF-8' not supported`에 유용 하다.

### java.lang.UnsupportedOperationException
* 배열, 인덱스에 관련된 오류

### Cannot construct instance of `java.time.LocalDateTime`
```java
@JsonDeserialize(using = LocalDateTimeDeserializer.class)
```

### SQL
#### com.microsoft.sqlserver.jdbc.SQLServerException: 데이터 형식 varbinary을(를) date(으)로 암시적으로 변환할 수 없습니다.
```diff
- #{date}
+ #{date, mode=IN, jdbcType=DATE}
```

#### Cannot determine value type from string ''
* 숫자형에 문자가 들어 올때 발생
* `select문`의 순서와 `Model 생성자`의 순서가 같은지 확인
