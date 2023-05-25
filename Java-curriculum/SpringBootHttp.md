# Spring Boot - HTTP 통신

## 설치
* 프로젝트명: SpringBootHttpStudy
* Spring Boot - Rest API의 Swagger UI 설정까지 동일

### 서버 포트 바꾸기
src/main/resources/application.properties
```properties
server.port=18080
```

또는

```sh
Run -> Edit Configurations... -> Environment variables: `server.port=18080`
```
* http://localhost:18080/swagger-ui.html

## 회원(Users) Service 만들기
* ❕ Service를 생성하는 이유 (여러 Controller에서 사용 되거나, 또는 Test(JUnit)에서 사용될 비지니스 로직(DB의 CRUD등등)을 담을때 사용한다.)

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```java
@Service
public class UsersService {
    private static List<User> init() {
        List<User> users = new ArrayList<>();
        users.add(new User("홍길동", 39));
        users.add(new User("김삼순", 33));
        users.add(new User("홍명보", 44));
        users.add(new User("박지삼", 22));
        users.add(new User("권명순", 10));
        return users;
    }
    public static final List<User> users = init();

    public List<User> read() {
        return users;
    }

    public Integer create(User user) {
        users.add(user);
        return users.size();
    }

    public Integer delete(int index) {
        users.remove(index);
        return users.size();
    }

    public Integer update(int index, User user) {
        users.set(index, user);
        return users.size();
    }
}
```

### 회원(Users) Controller에서 Service 사용
src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersController.java
```java
@Autowired
UsersService usersService;
```
```diff
# Create
- users.add(user);
+ usersService.create(user);

# Read
- return new UsersResponse("read", users);
+ return new UsersResponse("read", usersService.read());

# Delete
- users.remove(index);
+ usersService.delete(index);

# Update
- users.set(index, user);
+ usersService.update(index, user);
```

```diff
- private static List<User> init() {
```

### 회원(Users) Service 테스트(JUnit)에서 호출 하기
* 테스트를 사용하는 이유 (테스트 하고 싶은 부분만 바로 실행 할 수 있다.)

src/test/java/com/example/SpringBootHttpStudy/SpringBootHttpStudyApplicationTests.java
```java
@Autowired
private UsersService usersService;

@Test
void users() {
    List<User> users = usersService.read();
    usersService.create(new User("semin", 20));
    usersService.delete(0);
    usersService.update(0, new User("min", 30));
    System.out.println(users);
}
```
* ❕ 테스트에서도 Service를 자유롭게 사용 할 수 있다.

## Service와 ServiceImpl
* 관습적으로 Service를 만들때 Service와 ServiceImpl로 나누어서 개발하는 곳이 많다.

UsersService.java 복사해서 UsersServiceImpl.java

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```diff
- @Service
- 밑으로 모두 삭제
```
```java
public interface UsersService {
    List<User> read();
    Integer create(User user);
    Integer delete(int index);
    Integer update(int index, User user);
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersServiceImpl.java
```diff
- @Service
- public class UsersService {
```
```java
@Service("UsersService")
public class UsersServiceImpl implements UsersService {
```

* ❕ 결론 Service와 ServiceImpl의 관계가 1:1인 경우는 service와 serviceImpl로 따로 나눌 필요 없다.
* 1:N 관계가 된다면 그때 생각해 보자 (하지만 필자는 아직 1:N 상황을 만난적 없음)
* 하지만 회사에서 `Service와 ServiceImpl`을 나누어 사용한다고 하면, 회사의 룰에 따른다.
* https://elvis-note.tistory.com/entry/9-Spring-MVC-2-Service%EC%99%80-ServiceImpl
* https://multifrontgarden.tistory.com/97

# httpclient5
## 설치
pom.xml
```xml
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
    <version>5.1</version>
</dependency>

<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
</dependency>
```

## 회원(Users) Read
src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```java
import org.apache.tomcat.util.json.JSONParser;

// @SuppressWarnings("unchecked")
public List<User> read() throws Exception {
    HttpGet httpGet = new HttpGet("http://localhost:8080/api/v1/users");
    httpGet.addHeader("Content-Type", "application/json");
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpGet);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("users"));
    httpClient.close();
    return (List<User>) linkedHashMap.get("users");
}
```
* `Controller`와 `Test` 모두 예외를 회피 한다. (`throws Exception`)

## 회원(Users) Create
```java
public Integer create(User user) throws Exception {
    HttpPost httpPost = new HttpPost("http://localhost:8080/api/v1/users");
    httpPost.addHeader("Content-Type", "application/json");
    Gson gson = new Gson();
    StringEntity stringEntity = new StringEntity(gson.toJson(user), StandardCharsets.UTF_8);
    httpPost.setEntity(stringEntity);
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpPost);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

## 회원(Users) Delete
```java
public Integer delete(int index) throws Exception {
    HttpDelete httpDelete = new HttpDelete("http://localhost:8080/api/v1/users/" + index);
    httpDelete.addHeader("Content-Type", "application/json");
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpDelete);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

## 회원(Users) Update
```java
public Integer update(int index, User user) throws Exception {
    HttpPatch httpPatch = new HttpPatch("http://localhost:8080/api/v1/users/" + index);
    httpPatch.addHeader("Content-Type", "application/json");
    Gson gson = new Gson();
    StringEntity stringEntity = new StringEntity(gson.toJson(user), StandardCharsets.UTF_8);
    httpPatch.setEntity(stringEntity);
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpPatch);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```
```diff
- private static List<User> init() {
```

## HttpClient5 Service 또는 공용 함수 만들기
src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```java
public class HttpClient5 {
    public static void connect(String method, String url) throws Exception {
        HttpUriRequestBase httpUriRequestBase;
        if ("POST".equals(method)) {
            httpUriRequestBase = new HttpPost(url);
        } else if ("PATCH".equals(method)) {
            httpUriRequestBase = new HttpPatch(url);
        } else if ("DELETE".equals(method)) {
            httpUriRequestBase = new HttpDelete(url);
        } else {
            httpUriRequestBase = new HttpGet(url);
        }
        httpUriRequestBase.addHeader("Content-Type", "application/json");
        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse httpResponse = httpClient.execute(httpUriRequestBase);
        System.out.println(httpResponse.getCode());
        String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
        System.out.println(jsonString);
        JSONParser jsonParser = new JSONParser(jsonString);
        LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
        httpClient.close();
    }
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```java
public List<User> read() throws Exception {
    HttpClient5.connect("GET", "http://localhost:8080/api/v1/users");
    return null;
}
```

### 통신 후에 결과를 담을 model 생성 
src/main/java/com/example/SpringBootHttpStudy/api/v1/models/HttpClient5Response.java
```java
public class HttpClient5Response {
    private int code = 0;
    private String responseJsonString;
    private LinkedHashMap<String, Object> responseMap;
}
```
* Generate... -> Getter and Setter -> 생성

src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```diff
- public static void connect(String method, String url) throws Exception {
```
```java
public static HttpClient5Response connect(String method, String url) throws Exception {
    HttpClient5Response httpClient5Response = new HttpClient5Response();
```
```java
    httpClient5Response.setCode(httpResponse.getCode());
    httpClient5Response.setResponseJsonString(jsonString);
    httpClient5Response.setResponseMap(linkedHashMap);
    return httpClient5Response;
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```diff
- HttpClient5.connect("GET", "http://localhost:8080/api/v1/users");
- return null;
```
```java
String url = "http://localhost:8080/api/v1/users";
HttpClient5Response httpClient5Response = HttpClient5.connect("GET", url);
return (List<User>) httpClient5Response.getResponseMap().get("users");
```

### 회원(Users) Service Delete
```java
public Integer delete(int index) throws Exception {
    String url = "http://localhost:8080/api/v1/users/" + index;
    HttpClient5.connect("DELETE", url);
    return null;
}
```

### 회원(Users) Service Create, Update
```java
public Integer create(User user) throws Exception {
    String url = "http://localhost:8080/api/v1/users";
    HttpClient5.connect("POST", url, user);
    return null;
}
```
* ❔ 아래 부분을 `HttpClient5.connect` 함수에 적용해 보기
```diff
- public static HttpClient5Response connect(String method, String url) throws Exception {
```
```java
public static HttpClient5Response connect(String method, String url, Object entry) throws Exception {
```
```java
Gson gson = new Gson();
StringEntity stringEntity = new StringEntity(gson.toJson(user), StandardCharsets.UTF_8);
httpPost.setEntity(stringEntity);
```
* `오류` 모두 수정 하기
* <details><summary>정답</summary>

  ```java
  if (entity != null) {
      Gson gson = new Gson();
      StringEntity stringEntity = new StringEntity(gson.toJson(entity), StandardCharsets.UTF_8);
      httpUriRequestBase.setEntity(stringEntity);
  }
  ```
</details>

* ❔ `update` 함수도 적용해 보기
* ❔ `String URL` private static 상수로 빼기

## HttpClient5 get, post, patch, delete 함수 만들기
src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```java
public static HttpClient5Response get(String url) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpGet(url);
    return connect(httpUriRequestBase, null);
}

public static HttpClient5Response post(String url, Object entity) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpPost(url);
    return connect(httpUriRequestBase, entity);
}

public static HttpClient5Response delete(String url) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpDelete(url);
    return connect(httpUriRequestBase, null);
}

public static HttpClient5Response patch(String url, Object entity) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpPatch(url);
    return connect(httpUriRequestBase, entity);
}
```
* ❔ HttpClient5Response 함수 수정 하기
* ❔ Service 수정 하기

## Query string 받아서 넘기기
src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersController.java
```java
public UsersResponse usersRead(
        @RequestParam("name") String name,
        @RequestParam("age") int age
) throws Exception {
    User user = new User(name, age);
    return new UsersResponse("read", usersService.read(user));
}
```
* 또는
```java
public UsersResponse usersRead(@ModelAttribute User user) throws Exception {
    return new UsersResponse("read", usersService.read(user));
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```diff
- public List<User> read() throws Exception {
+ public List<User> read(User user) throws Exception {
```
* ❕ Problem이 발생 하면 Problems 탭에서 확인
* Swagger에서 확인

### HttpClient5에서 Query string을 받을 수 있는 함수 만들기
```diff
- HttpClient5Response httpClient5Response = HttpClient5.get(URL);
+ HttpClient5Response httpClient5Response = HttpClient5.getQuery(URL, user);
```

src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```java
public static String uriBuilder(String url, Object query) throws Exception {
    URIBuilder uriBuilder = new URIBuilder(url);
    Gson gson = new Gson();
    JsonObject jsonObject = gson.toJsonTree(query).getAsJsonObject();
    for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
        System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue().getAsString());
        uriBuilder.addParameter(entry.getKey(), entry.getValue().getAsString());
    }
    url = uriBuilder.build().toString();
    System.out.println(url);
    return url;
}

public static HttpClient5Response getQuery(String url, Object query) throws Exception {
    url = uriBuilder(url, query);
    return get(url);
}
```

* ❔ `deleteQuery`, `postQuery`, `patchQuery` 함수 만들기
* ❕ `query` 객체를 `null`로 넘긴다면, 대신에 `delete`, `post`, `patch` 함수를 사용하자

## 커스텀 Properties Model로 받기
src/main/resources/custom.properties
```properties
a1=123
b1.b2=한글
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/models/CustomProperties.java
```java
@Component
@PropertySource(value="classpath:custom.properties", encoding="UTF-8")
public class CustomProperties {
    @Value("${a1}")
    public Integer a1;
    @Value("${b1.b2}")
    public String b2;

    private static Integer _a1;
    private static String _b2;

    private CustomProperties() {}

    @PostConstruct
    private void init() {
        _a1 = this.a1;
        _b2 = this.b2;
    }

    public static CustomProperties getAll() {
        CustomProperties properties = new CustomProperties();
        properties.a1 = _a1;
        properties.b2 = _b2;
        return properties;
    }
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```diff
- HttpClient5Response httpClient5Response = HttpClient5.getQuery(URL, user);
+ HttpClient5Response httpClient5Response = HttpClient5.getQuery(URL, CustomProperties.getAll());
```
* `@Component`와 `@PostConstruct` 동작 설명

## 객체 Merge 해주는 함수 만들기
src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```java
public static Map<String, Object> gsonMerge(Object[] objects) {
    Map<String, Object> map = new HashMap<>();
    for (Object object : objects) {
        Gson gson = new Gson();
        JsonObject jsonObject = gson.toJsonTree(object).getAsJsonObject();
        for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
            map.put(entry.getKey(), entry.getValue());
        }
    }
    return map;
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```java
Object[] objects = {user, CustomProperties.getAll()};
```
```diff
- HttpClient5Response httpClient5Response = HttpClient5.getQuery(URL, CustomProperties.getAll());
+ HttpClient5Response httpClient5Response = HttpClient5.getQuery(URL, HttpClient5.gsonMerge(objects));
```
* ❔ `user.name`만 빼고 넘기려면

## 예외 처리
* https://cheese10yun.github.io/spring-guide-exception
* https://supawer0728.github.io/2019/04/04/spring-error-handling

src/main/resources/application.properties
```properties
server.error.include-message=ALWAYS
server.error.include-exception=TRUE
#server.error.include-stacktrace=ALWAYS
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/UsersService.java
```diff
- return (List<User>) httpClient5Response.getResponseMap().get("users");
```
```java
int a = 1 / 0;
return (List<User>) httpClient5Response.getResponseMap().get("users");
```

### 공용 예외 처리
src/main/java/com/example/SpringBootHttpStudy/api/v1/ExceptionController.java
```java
import org.springframework.boot.web.servlet.error.DefaultErrorAttributes;

@ControllerAdvice
@Slf4j
public class ExceptionController {
    @ExceptionHandler({ Exception.class })
    public ResponseEntity<Map<String, Object>> handleException(final Exception exception, WebRequest webRequest) {
        log.error("Exception", exception);
        DefaultErrorAttributes defaultErrorAttributes = new DefaultErrorAttributes();
        Map<String, Object> map = defaultErrorAttributes.getErrorAttributes(webRequest, ErrorAttributeOptions.defaults());
        //  Map<String, Object> map = ErrorAttributes.getErrorAttributes(webRequest);
        map.put("key", "value");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(map);
    }
}
```
* `@Slf4j` <- Add 'lombok' to classpath
* pom.xml <- Reload project

#### application.properties 설정에한 에러 레벨 적용한
src/main/java/com/example/SpringBootHttpStudy/common/ErrorAttributes.java
```java
@Component
public class ErrorAttributes extends DefaultErrorAttributes {
    @Value("${server.error.include-message}")
    private String _message;
    @Value("${server.error.include-exception}")
    private Boolean _exception;

    private static String message;
    private static Boolean exception;

    @PostConstruct
    private void init() {
        message = this._message;
        exception = this._exception;
    }

    public static Map<String, Object> getErrorAttributes(WebRequest request) {
        List<ErrorAttributeOptions.Include> includes = new ArrayList<>();
        if ("ALWAYS".equals(message)) {
            includes.add(ErrorAttributeOptions.Include.MESSAGE);
        }
        if (exception) {
            includes.add(ErrorAttributeOptions.Include.EXCEPTION);
        }
        ErrorAttributeOptions errorAttributeOptions = ErrorAttributeOptions.of(includes);
        ErrorAttributes ErrorAttributes = new ErrorAttributes();
        return ErrorAttributes.getErrorAttributes(request, errorAttributeOptions);
    }
}
```
* `ExceptionController.java` <- 주석 풀기

<!--
    @Override
    public Map<String, Object> getErrorAttributes(WebRequest webRequest, ErrorAttributeOptions options) {
        return super.getErrorAttributes(webRequest, options);
    }
-->

### 예외 나누어 처리하기
src/main/java/com/example/SpringBootHttpStudy/api/v1/ExceptionController.java
```java
@ExceptionHandler({ HttpHostConnectException.class })
public ResponseEntity<Map<String, Object>> handleHttpHostConnectException(
        final HttpHostConnectException httpHostConnectException,
        WebRequest webRequest
) {
    log.error("HttpHostConnectException", httpHostConnectException);
    Map<String, Object> map = ErrorAttributes.getErrorAttributes(webRequest);
    map.put("message", httpHostConnectException.getMessage());
    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(map);
}
```
* 8080 서버 <- 끄기

### Custom 예외 처리
src/main/java/com/example/SpringBootHttpStudy/common/CustomException.java
```java
public class CustomException extends Exception {
    private boolean isFromException;
    private HttpClient5Response httpClient5Response;

    public CustomException(boolean isFromException, HttpClient5Response httpClient5Response) {
        this.isFromException = isFromException;
        this.httpClient5Response = httpClient5Response;
    }

    public boolean isFromException() {
        return isFromException;
    }

    public HttpClient5Response getHttpClient5Response() {
        return httpClient5Response;
    }
}
```

src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```diff
- CloseableHttpResponse httpResponse = httpClient.execute(httpUriRequestBase);
- System.out.println(httpResponse.getCode());
- String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
- System.out.println(jsonString);
- JSONParser jsonParser = new JSONParser(jsonString);
- LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
- httpClient.close();
- httpClient5Response.setCode(httpResponse.getCode());
- httpClient5Response.setResponseJsonString(jsonString);
- httpClient5Response.setResponseMap(linkedHashMap);
```
```java
try {
    CloseableHttpResponse httpResponse = httpClient.execute(httpUriRequestBase);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    httpClient.close();
    httpClient5Response.setCode(httpResponse.getCode());
    httpClient5Response.setResponseJsonString(jsonString);
    httpClient5Response.setResponseMap(linkedHashMap);
} catch (HttpHostConnectException httpHostConnectException) {
    throw new CustomException(false, httpClient5Response);
} catch (Exception exception) {
    throw new CustomException(true, httpClient5Response);
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/ExceptionController.java
```java
@ExceptionHandler({ CustomException.class })
public ResponseEntity<Map<String, Object>> handleCustomException(
        final CustomException customException,
        WebRequest webRequest
) {
    log.error("customException", customException);
    Map<String, Object> map = ErrorAttributes.getErrorAttributes(webRequest);
    if (customException.isFromException()) {
        map.put("fromException", customException.getHttpClient5Response());
    } else {
        map.put("error", customException.getHttpClient5Response());
        map.put("status", HttpStatus.INTERNAL_SERVER_ERROR.value());
    }
    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(map);
}
```

### 임의로 예외 발생 시키기
src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```diff
- httpClient5Response.setResponseJsonString(jsonString);
```
```java
httpClient5Response.setResponseJsonString(jsonString);
if (httpClient5Response != null) {
    throw new Exception();
}
```
* 8080 서버 <- 켜기

## Gson to Jackson
src/main/java/com/example/SpringBootHttpStudy/common/HttpClient5.java
```diff
- if (entity != null) {
-     Gson gson = new Gson();
-     StringEntity stringEntity = new StringEntity(gson.toJson(entity), StandardCharsets.UTF_8);
-     httpUriRequestBase.setEntity(stringEntity);
- }
```
```java
if (entity != null) {
    ObjectMapper objectMapper = new ObjectMapper();
    String jsonString = objectMapper.writeValueAsString(entity);
    StringEntity stringEntity = new StringEntity(jsonString, StandardCharsets.UTF_8);
    httpUriRequestBase.setEntity(stringEntity);
}
```

```diff
- Gson gson = new Gson();
- JsonObject jsonObject = gson.toJsonTree(query).getAsJsonObject();
- for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
-     System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue() );
-     uriBuilder.addParameter(entry.getKey(), entry.getValue().toString());
- }
```
```java
ObjectMapper objectMapper = new ObjectMapper();
String jsonString = objectMapper.writeValueAsString(object);
Map<String, Object> map = objectMapper.readValue(jsonString, new TypeReference<Map<String, Object>>(){});
for(Map.Entry<String, Object> entry : map.entrySet()) {
    System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue());
    uriBuilder.addParameter(entry.getKey(), entry.getValue().toString());
}
```

```diff
- for (Object object : objects) {
-     Gson gson = new Gson();
-     JsonObject jsonObject = gson.toJsonTree(object).getAsJsonObject();
-     for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
-         map.put(entry.getKey(), entry.getValue());
-     }
- }
```
```java
for (Object object : objects) {
    ObjectMapper objectMapper = new ObjectMapper();
    String jsonString = objectMapper.writeValueAsString(object);
    Map<String, Object> mapObject = objectMapper.readValue(jsonString, new TypeReference<Map<String, Object>>(){});
    for(Map.Entry<String, Object> entry : mapObject.entrySet()) {
        map.put(entry.getKey(), entry.getValue());
    }
}
```
