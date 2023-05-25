# Rest Template

## Service
UsersService.java
```java
@Service
public class UsersService {
    static String URL = "http://localhost:8080/api/v1/users";

    public UsersResponse read() {
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<UsersResponse> responseEntity = restTemplate.exchange(URL, HttpMethod.GET, null, UsersResponse.class);
        return responseEntity.getBody();
    }

    public UsersResponse create(User user) {
        RestTemplate restTemplate = new RestTemplate();
        HttpEntity<User> httpEntity = new HttpEntity<>(user, null);
        ResponseEntity<UsersResponse> responseEntity = restTemplate.exchange(URL, HttpMethod.POST, httpEntity, UsersResponse.class);
        return responseEntity.getBody();
    }

    public UsersResponse delete(int index) {
        RestTemplate restTemplate = new RestTemplate();
        String url = URL + "/" + index;
        ResponseEntity<UsersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.DELETE, null, UsersResponse.class);
        return responseEntity.getBody();
    }

    public UsersResponse update(int index, User user) {
        RestTemplate restTemplate = new RestTemplate();
        String url = URL + "/" + index;
        HttpEntity<User> httpEntity = new HttpEntity<>(user, null);
        ResponseEntity<UsersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, httpEntity, UsersResponse.class);
        return responseEntity.getBody();
    }
}
```
* ❕ `HttpMethod.PATCH` 통신은 기본으로 제공 하지 않는다.

<!--
### Warning: Dereference of 'usersResponse' may produce 'NullPointerException'
```diff
- return usersResponse.users;
+ return usersResponse != null ? usersResponse.users : null;
```
* 위와 같이 수정하면 경고가 나오지 않지만, 소스가 지저분 해지므로 경고를 무시한다.
-->

## UriComponentsBuilder
```java
public static String uriBuilder(String url, Object query) {
    UriComponentsBuilder uriComponentsBuilder = UriComponentsBuilder.fromHttpUrl(url);
    try {
        ObjectMapper objectMapper = new ObjectMapper();
        String jsonString = objectMapper.writeValueAsString(query);
        Map<String, Object> map = objectMapper.readValue(jsonString, new TypeReference<Map<String, Object>>() {
        });
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue());
            uriComponentsBuilder.queryParam(entry.getKey(), entry.getValue().toString());
        }
    } catch (JsonProcessingException e) {
        e.printStackTrace();
    }
    return uriComponentsBuilder.build().toUriString();
}
```
```diff
- ResponseEntity<UsersResponse> responseEntity = restTemplate.exchange(URL, HttpMethod.GET, null, UsersResponse.class);
+ String url = uriBuilder(URL, new User("홍길동", 39));
+ ResponseEntity<UsersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.GET, null, UsersResponse.class);
```
