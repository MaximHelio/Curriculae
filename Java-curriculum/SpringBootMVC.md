# Spring Boot - MVC 패턴

## 설치
* https://start.spring.io
* https://sidepower.tistory.com/352
```sh
Project: Maven Project
Language: Java
Spring Boot: 2.5.4 (현재)
Project Metadata
    Artifact: SpringBootMvcStudy
Packaging: Jar
Java: 8
Dependencies: Spring Web

GENERATE <- SpringBootMvcStudy.zip 다운받기
압축 풀고 해당 경로를 Intellij에서 Open
Terminal <- git init
```

## Spring Boot Tomcat 서버 실행
src/main/java/com/example/SpringBootMvcStudy/SpringBootMvcStudyApplication.java
```sh
public class SpringBootMvcStudyApplication <- RUN 버튼 누르기
```

## Controller 만들기
* Controller는 웹 URL 경로를 생성해 준다.

src/main/java/com/example/SpringBootMvcStudy/controllers/RootController.java
```java
@Controller
public class RootController {
    @RequestMapping("/")
    @ResponseBody
    String root() {
        return "Hello world!";
    }
}
```

```diff
- return "Hello world!";
+ return "<script>document.location.href = '/usersRead';</script>";
```

src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```java
@Controller
public class UsersController {
    @RequestMapping(value = "/usersRead", method = RequestMethod.GET)
    @ResponseBody
    String usersRead() {
        return "usersRead";
    }
}
```

src/main/java/com/example/SpringBootMvcStudy/controllers/SearchController.java
```java
@Controller
public class SearchController {
    @RequestMapping(value = "/search", method = RequestMethod.GET)
    @ResponseBody
    String search() {
        return "search";
    }
}
```

## .jsp 파일 읽기
src/main/webapp/test.jsp
```jsp
<%="test"%>
```
* http://localhost:8080/test.jsp
* pom.xml 설정 없이도 webapp 경로의 파일은 접근 가능하다. 하지만 jsp를 컴파일 하지 못한다.

### .jsp 파일 컴파일 하기
pom.xml
```xml
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>
```
* pom.xml <- Maven <- Reload project
* Spring Boot(Tomcat) 재시작
* http://localhost:8080/test.jsp
* ❕ 안될 경우: IntelliJ 재시작

## View 만들기
src/main/webapp/WEB-INF/views/users.jsp
```jsp
<%="users.jsp"%>
```

src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```diff
- @ResponseBody
- String usersRead() {
-     return "usersRead";
- }
```
```java
ModelAndView usersRead() {
    return new ModelAndView("users");
}
```
* ❕ `ModelAndView`을 사용하면 `@ResponseBody` 어노테이션은 사용하지 않아도 된다.

src/main/resources/application.properties
```properties
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```
<!--
# 컨테이너에 기본 서블릭을 등록한다. Spring boot 2.4 버전 부터는 기본이 false이다. (무슨 차이인지 ...)
server.servlet.register-default-servlet=true
-->

### View Users 페이지 Markup 적용
src/main/webapp/WEB-INF/views/users.jsp
```jsp
<%@ page language="java" contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Users</title>
    <link href="/css/app.css" rel="stylesheet">
</head>
<body>
    <div>
        <header>
            <h1>Spring Boot MVC study</h1>
        </header>
        <hr />
        <div class="container">
            <nav class="nav">
                <ul>
                    <li><h2><a href="/usersRead" class="active">Users</a></h2></li>
                    <li><h2><a href="/search">Search</a></h2></li>
                </ul>
            </nav>
            <hr />
            <section class="contents">
                <div>
                    <h3>Users</h3>
                    <hr class="d-block" />
                    <div>
                        <h4>Read</h4>
                        <table>
                            <thead>
                                <tr>
                                    <th>Name</th>
                                    <th>Age</th>
                                    <th>Modify</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr>
                                    <td>홍길동</td>
                                    <td>20</td>
                                    <td>
                                        <button>Update</button>
                                        <button>Delete</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <hr class="d-block" />
                    <div>
                        <h4>Create</h4>
                        <form method="POST" action="/usersCreate">
                            <input type="text" name="name" placeholder="Name" />
                            <input type="text" name="age" placeholder="Age" />
                            <button>Create</button>
                        </form>
                    </div>
                </div>
            </section>
            <hr />
        </div>
        <footer>Copyright</footer>
    </div>
</body>
</html>
```

src/main/webapp/css/app.css
```css
* {
  margin: 0;
  font-family: -apple-system,BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
}
a:link, a:visited {
  text-decoration: none;
  color: black;
}
a.active {
  color: white;
}
h1, footer, .nav ul {
  padding: 0.5rem;
}
h4, li {
  margin: 0.5rem 0;
}
hr {
  display: none;
  margin: 1rem 0;
  border: 0;
  border-top: 1px solid #ccc;
}
input[type=text] {
  width: 120px;
}

.d-block {
  display: block;
}
.container {
  display: flex;
  border-top: 1px solid #ccc;
  border-bottom: 1px solid #ccc;
}
.nav {
  min-height: 300px;
  background-color: #4285F4;
}
.nav ul {
  list-style: none;
}
.contents {
  flex: 1;
  padding: 1rem;
}

.table-search {
  border: 1px solid rgb(118, 118, 118);
  border-collapse: collapse;
  text-align: center;
}
.table-search th, .table-search td {
  padding: 0.2rem;
}
.table-search td {
  border-top: 1px solid rgb(118, 118, 118);
  min-width: 100px;
}
```

<!--
## View - Thymeleaf
pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

src/main/resources/application.properties
```properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.cache=false
spring.thymeleaf.enabled=true
```

src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```java
@RequestMapping("/usersRead")
ModelAndView usersRead(Model model) {
    ModelAndView modelAndView = new ModelAndView("users");
    modelAndView.addObject("result", "read");
    return modelAndView;
}
```

src/main/resources/templates/users.html
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Users</title>
</head>
<body>
    <p th:text="${result}"></p>
</body>
</html>
```
-->

## Model 만들기
* Model은 데이터 구조 또는 스키마라고 한다.

### 회원(Users) Model
src/main/java/com/example/SpringBootMvcStudy/models/User.java
```java
public class User {
    private String name;
    private Integer age;

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

## Model과 Controller 연결
src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java

public class UsersController {

```java
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
    
    @RequestMapping(value = "/usersRead", method = RequestMethod.GET)
    ModelAndView usersRead() {
        User user = users.get(0);
        System.out.println(user.getName());
        System.out.println(user.getAge());
        return new ModelAndView("users");
    }
```

## Model과 Controller와 View 연결
```diff
    ModelAndView usersRead() {
-       User user = users.get(0);
-       System.out.println(user.getName());
-       System.out.println(user.getAge());
-       return new ModelAndView("users");
	ModelAndView modelAndView = new ModelAndView("users");
        modelAndView.addObject("result", "read");
        modelAndView.addObject("users", users);
        return modelAndView;
```

### View Users 페이지에서 Model 활용
src/main/webapp/WEB-INF/views/users.jsp
```diff
- <h4>Read</h4>
+ <h4>${result}</h4>
```

## 회원(Users) CRUD
### 회원(Users) Read
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```
```diff
- <tr>
-     <td>홍길동</td>
-     <td>20</td>
-     <td>
-         <button>Update</button>
-         <button>Delete</button>
-     </td>
- </tr>
```
```jsp
<c:forEach items="${users}" var="user" varStatus="status">
    <form method="POST">
    <tr>
        <td>${user.name}</td>
        <td>${user.age}</td>
        <td>
            <button>Update</button>
            <button>Delete</button>
        </td>
    </tr>
    </form>
</c:forEach>
```

### 회원(Users) Create
src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```java
@RequestMapping(value = "/usersCreate", method = RequestMethod.POST)
@ResponseBody
String usersCreate(User user) {
    users.add(user);
    return "<script>document.location.href = '/usersRead';</script>";
}
```

### 회원(Users) Delete
src/main/webapp/WEB-INF/views/users.jsp
```diff
- <button>Delete</button>
+ <button onclick="this.form.action = '/usersDelete/${status.index}';">Delete</button>
```

src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```java
@RequestMapping(value = "/usersDelete/{index}", method = RequestMethod.POST)
@ResponseBody
    String usersDelete(@PathVariable("index") int index) {
    users.remove(index);
    return "<script>document.location.href = '/usersRead';</script>";
}
```

### 회원(Users) Update
src/main/webapp/WEB-INF/views/users.jsp
```diff
- <td>${user.name}</td>
- <td>${user.age}</td>
+ <td><input type="text" name="name" placeholder="Name" value="${user.name}" /></td>
+ <td><input type="text" name="age" placeholder="Age" value="${user.age}" /></td>
```
```diff
- <button>Update</button>
+ <button onclick="this.form.action = '/usersUpdate/${status.index}';">Update</button>
```

src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```java
@RequestMapping(value = "/usersUpdate/{index}", method = RequestMethod.POST)
@ResponseBody
String usersUpdate(
        @PathVariable("index") int index,
        User user
) {
    users.set(index, user);
    return "<script>document.location.href = '/usersRead';</script>";
}
```

## 검색(Search) 
### Search 페이지 Markup 적용
src/main/webapp/WEB-INF/views/search.jsp
```jsp
<%@ page language="java" contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Search</title>
    <link href="/css/app.css" rel="stylesheet">
</head>
<body>
    <div>
        <header>
            <h1>Spring Boot MVC study</h1>
        </header>
        <hr />
        <div class="container">
            <nav class="nav">
                <ul>
                    <li><h2><a href="/usersRead">Users</a></h2></li>
                    <li><h2><a href="/search" class="active">Search</a></h2></li>
                </ul>
            </nav>
            <hr />
            <section class="contents">
                <div>
                    <h3>${result}</h3>
                    <hr class="d-block" />
                    <div>
                        <form>
                            <input type="text" placeholder="Search" name="q" />
                            <button>Search</button>
                        </form>
                    </div>
                    <hr class="d-block" />
                    <div>
                        <table class="table-search">
                            <thead>
                                <tr>
                                    <th>Name</th>
                                    <th>Age</th>
                                </tr>
                            </thead>
                            <tbody>
                            <c:forEach items="${users}" var="user" varStatus="status">
                                <tr>
                                    <td>${user.name}</td>
                                    <td>${user.age}</td>
                                </tr>
                            </c:forEach>
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>
            <hr />
        </div>
        <footer>Copyright</footer>
    </div>
</body>
</html>
```

src/main/java/com/example/SpringBootMvcStudy/controllers/SearchController.java
```java
@Controller
public class SearchController {
    @RequestMapping(value = "/search", method = RequestMethod.GET)
    ModelAndView search() {
        ModelAndView modelAndView = new ModelAndView("search");
        modelAndView.addObject("result", "search");
        modelAndView.addObject("users", UsersController.users);
        return modelAndView;
    }
}
```

### Search 검색 기능 적용
src/main/webapp/WEB-INF/views/search.jsp
```diff
- <input type="text" placeholder="Search" name="q" />
+ <input type="text" placeholder="Search" name="q" value="${param.q}" />
```

src/main/java/com/example/SpringBootMvcStudy/controllers/SearchController.java
```diff
- ModelAndView search() {
+ ModelAndView search(@RequestParam(required = false) String q) {
```
```java
List<User> users = new ArrayList<>();
for (int index = 0; index < UsersController.users.size(); index++) {
    User user = UsersController.users.get(index);
    if (q == null || user.getName().contains(q)) {
        users.add(user);
    }
}
```
```diff
- modelAndView.addObject("users", UsersController.users);
+ modelAndView.addObject("users", users);
```

### Nav import
```diff
- <nav class="nav">
-     <ul>
-         <li><h2><a href="/usersRead" class="active">Users</a></h2></li>
-         <li><h2><a href="/search">Search</a></h2></li>
-     </ul>
- </nav>
```
```jsp
<c:import url="../layout/nav.jsp" charEncoding="UTF-8">
    <c:param name="active" value="users" />
</c:import>
```

src/main/webapp/WEB-INF/layout/nav.jsp
```jsp
<%@ page language="java" contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<nav class="nav">
    <ul>
        <li>
            <h2>
                <a href="/usersRead"
                    <c:if test="${param.active eq 'users'}">class="active"</c:if>
                >Users</a>
            </h2>
        </li>
        <li><h2><a href="/search">Search</a></h2></li>
    </ul>
</nav>
```

### Session
src/main/java/com/example/SpringBootMvcStudy/controllers/UsersController.java
```java
ModelAndView usersRead(HttpServletRequest request) {
    System.out.println(request.getSession().getAttribute("userPk"));
    request.getSession().setAttribute("userPk", 1);
}
```
src/main/webapp/WEB-INF/views/users.jsp
```jsp
${userPk}
```
