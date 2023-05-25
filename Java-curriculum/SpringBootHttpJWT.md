# JSON Web Token(JWT)
* https://www.npmjs.com/package/jsonwebtoken
* https://jwt.io

## 설치
pom.xml
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

## JWT 테스트
src/main/java/패키지/common/JwtAuth.java
```java
import io.jsonwebtoken.Header;
import java.time.Duration;
import java.util.Date;

public class JwtAuth {
    private static String privateKey = "privateKey";

    public static String tokenCreate(Map<String, Object> user) {
        Date now = new Date();
        return Jwts.builder()
            .setHeaderParam(Header.TYPE, Header.JWT_TYPE)
            .setExpiration(new Date(now.getTime() + Duration.ofMinutes(60 * 24).toMillis()))
            .setSubject("login")
            .setClaims(user)
            .signWith(SignatureAlgorithm.HS256, privateKey)
            .compact();
    }

    public static Map<String, Object> tokenCheck(String token) {
        return Jwts.parser().setSigningKey(privateKey).parseClaimsJws(token).getBody();
    }
}
```

src/test/java/패키지/{프로젝트명}Tests.java
```java
@Test
void tokenCreate() {
    Map<String, Object> user = new HashMap<>();
    user.put("name", "홍길동");
    user.put("age", 39);
    String token = JwtAuth.tokenCreate(user);
    System.out.println(token);
}

@Test
void tokenCheck() {
    String token = "tokenCreate 함수에서 생성한 token";
    Map<String, Object> user = JwtAuth.tokenCheck(token);
    System.out.println(user);
}
```

## Controller 만들기
src/main/java/패키지/api/v1/users/UsersController.java
```java
@RequestMapping(path = "/login", method = RequestMethod.POST)
public String usersLogin(@RequestBody User user) {
    Map<String, Object> mapUser = new HashMap<>();
    mapUser.put("name", user.getName());
    mapUser.put("age", user.getAge());
    return JwtAuth.tokenCreate(mapUser);
}

@RequestMapping(path = "/check", method = RequestMethod.GET)
public Map<String, Object> usersCheck(@RequestParam String token) {
    Map<String, Object> user = JwtAuth.tokenCheck(token);
    return user;
}
```
### Java 11 이상에서 java.lang.ClassNotFoundException: javax.xml.bind.DatatypeConverter 발생 하는 경우
```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <!-- <version>2.3.1</version> -->
</dependency>
```

## token 값을 header에서 받기
### Swagger에 적용
src/main/java/패키지/프로젝트.java
```java
import java.util.Collections;
import springfox.documentation.service.AuthorizationScope;

@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket apiJwt() {
        ApiKey apikey = new ApiKey("x-jwt-token", "x-jwt-token", "header");
        AuthorizationScope[] authorizationScopes = {};
        SecurityReference securityReference = new SecurityReference("x-jwt-token", authorizationScopes);
        SecurityContext securityContext = SecurityContext.builder().securityReferences(Collections.singletonList(securityReference)).build();

        return new Docket(DocumentationType.SWAGGER_2)
                // 그룹을 생성해서 Swagger 페이지 우측 상단에서 이동 가능 
                .groupName("jwt-token")
                // Header에 x-jwt-token 받기
                .securitySchemes(Collections.singletonList(apikey))
                // API가 Header에서 받은 x-jwt-token 읽기
                .securityContexts(Collections.singletonList(securityContext))
                .select()
                .apis(RequestHandlerSelectors.basePackage("패키지 경로"))
                .paths(Predicates.or(PathSelectors.ant("/api/v1/users/login"), PathSelectors.ant("/api/v1/users/check")))
                .build();
    }
}
```
* ❕ 이전 `@Bean`이 있으면 추가 한다.

<!--
Predicates.and(
        Predicates.or(ant("/api/v1/users/login"), ant("/api/v1/users/check")),
        Predicates.not(ant("/error"))
)

```diff
- .securityContexts(Arrays.asList(securityContext))
+ .securityContexts(Collections.singletonList(securityContext))
-->

src/main/java/패키지/api/v1/users/UsersController.java
```diff
- public Map<String, Object> usersCheck(@RequestParam String token) {
```
```java
public Map<String, Object> usersCheck(
        @ApiIgnore @RequestHeader("x-jwt-token") String token
        // @RequestHeader(value="x-jwt-token", required=false) String token
) {
```
* `@ApiIgnore`가 함수 위에 있을 경우는 `swagger`에서 해당 API를 숨기고,
* `@ApiIgnore`가 파라미터에 있는 경우는 `swagger`에서 해당 파라미터를 숨긴다.
* ❕ `Swagger`에서 `Select a spec`이 `x-jwt-token`으로 선택되었는지 확인. 

## 모든 서버 요청에 JwtRequestFilter 먼저 실행 시키기
* 필터(Filter), 인터셉터(Interceptor), 미들웨어(Middleware) 상황에 따라서 달리 불려진다.

src/main/java/패키지/common/JwtRequestFilter.java
```java
@Component
public class JwtRequestFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain
    ) throws IOException, ServletException {
        filterChain.doFilter(request,response);
    }

    @Override
    protected boolean shouldNotFilter(HttpServletRequest request) {
        return true;
    }
}
```
* `디버깅 모드`에서 어떻게 동작 하는지 확인
* `@Component` 주석 처리해 보기
* `return true;` `false`로 바꿔 보기

### /api/v1/users/check 경로에만 Filter 적용
```diff
- return true;
```
```java
AntPathMatcher pathMatcher = new AntPathMatcher();
Collection<String> includeUrlPatterns = new ArrayList<>();
includeUrlPatterns.add("/api/v1/users/check");
boolean match = includeUrlPatterns
        .stream()
        .anyMatch(p -> pathMatcher.match(p, request.getServletPath()));
System.out.println(match + ", " + request.getServletPath());
return !match;
```

### Filter에서 Header의 x-jwt-token 토큰 받아서 회원정보 받기
```diff
- filterChain.doFilter(request,response);
```
```java
String token = request.getHeader("x-jwt-token");
Map<String, Object> user = JwtAuth.tokenCheck(token);
request.setAttribute("user", user);
filterChain.doFilter(request,response);
```

src/main/java/패키지/api/v1/users/UsersController.java
```diff
- @RequestMapping(path = "/check", method = RequestMethod.GET)
- public Map<String, Object> usersCheck(@RequestParam String token) {
-     Map<String, Object> user = JwtAuth.tokenCheck(token);
-     return user;
- }
```
```java
@RequestMapping(path = "/check", method = RequestMethod.GET)
public Map<String, Object> usersCheck(
        @ApiIgnore @RequestAttribute Map<String, Object> user
) {
    return user;
}
```

## Security와 JwtRequestFilter 연결 (꼭 필요하지 않으면 사용 하지 않는다.)
pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

src/main/java/패키지/WebSecurityConfig.java
```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                // csrf disable을 설정 안하면 POST, PATCH, DELETE 메소드에서 403 Forbidden 에러가 발생한다.
                .csrf().disable()
                .antMatcher("/api/v1/**")
                .addFilterBefore(new JwtRequestFilter(), UsernamePasswordAuthenticationFilter.class);
    }
}
```
<!--
@Override
public void configure(WebSecurity web) throws Exception {
    // swagger 관련 리소스 시큐리티 필터 제거
    web.ignoring().antMatchers(
            "/v2/api-docs",
            "/swagger-resources/**",
            "/swagger-ui.html",
            "/webjars/**", "/swagger/**"
    );
}

* https://sup2is.github.io/2020/03/05/spring-security-login-with-jwt.html
* https://kimchanjung.github.io/programming/2020/07/02/spring-security-02
-->
* [WebSecurityConfigurerAdapter - deprecation 관련](https://spring.io/blog/2022/02/21/spring-security-without-the-websecurityconfigureradapter)

src/main/java/패키지/common/JwtRequestFilter.java
```diff
- @Component
+ // @Component
```

* `디버깅 모드`에서 어떻게 동작 하는지 확인
* `.antMatcher` 주석 처리해 보기
* `.csrf().disable()` 주석 처리해 보기
* ❕ `.addFilterBefore` 없으면 아무일도 일어나지 않는다.
* ❕ `spring-boot-starter-security`는 `MVC` 패턴 기반이므로 `Rest API` 패턴에서 사용해야 할지는 신중히 판단 해야 한다.
