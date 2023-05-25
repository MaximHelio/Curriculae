# Tomcat 9 - Spring Boot 배포
## Spring boot war 파일 만들기
https://start.spring.io
```sh
Packaging > War
```

### Maven
pom.xml
```xml
<packaging>war</packaging>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<scope>provided</scope>
</dependency>
```
* ❕ 기존 프로젝트에서 `spring-boot-starter-tomcat provided` 없을 경우 Tomcat에서 Spring Boot가 실행 되지 않을 수 있다.
* ❕ 이 경우 프로젝트를 처음부터 만드는 방법이 빠를 수 있다.

#### .war 파일 생성
```sh
Maven 탭 > 프로젝트명 > Lifecycle > package > `target 디렉토리에 .war 파일 생성`
```

### Gradle
* https://hye0-log.tistory.com/29

build.gradle
```gradle
plugins {
	id 'war'
}

bootWar {
	archiveName("ROOT.war")
}
```

```sh
# war 파일 생성
./gradlew bootWar

# war 파일 실행
java -jar ./build/libs/ROOT.war

# ROOT 디렉토리명 변경
mv tomcat/webapps/ROOT tomcat/webapps/ROOT_ORI

# tomcat/webapps/ROOT.war 이동

# 현재 터미널에서 톰캣 실행 (로그를 바로 볼 수 있다. ctrl + c 바로 종료)
tomcat/bin/catalina.sh run

# tomcat 데몬으로 실행
tomcat/bin/startup.sh

# tomcat 데몬으로 종료
tomcat/bin/shutdown.sh

# tomcat 데몬 로그 보기
tail -f tomcat/logs/catalina.out
```

# Tomcat 10 - Spring Boot 배포
* https://adg0609.tistory.com/57
```sh
# ROOT 디렉토리명 변경
mv tomcat/webapps/ROOT tomcat/webapps/ROOT_ORI

# tomcat/webapps-javaee 폴더 생성
# tomcat/webapps-javaee/ROOT.war 이동
```
