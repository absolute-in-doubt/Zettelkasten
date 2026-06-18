
09-11-2025 12:50

Status:

Tags: [[Spring]] [[Http in Java]] [[Java+]]

---
# RestTemplate - Spring Web



```java
RestTemplate restTemplate = new RestTemplate();

//GET запрос
String response = restTemplate.getForObject(
	"https://reqres.in/api/users/2", 
	String.class);


Map<String, String> jsonToSend = new HashMap<>();
jsonToSend.put("name", "Test name");
jsonToSend.put("job", "Test job"); 


HttpHeaders headers = new HttpHeaders();
//установка стандартного заголовка
headers.setContentType(MediaType.APPLICATION_JSON);
//установка нестандартного заголовка
headers.add("Authorization", "Bearer " + "3jh234g2vj34k22jh3g43");

HttpEntity<Map<String,String>> request = new HttpEntity<>(JsonToSend, headers);

String response = restTemplate.getForObject(
	"https://reqres.in/api/users", 
	request,
	String.class);

```

##### Если нужны заголовки в GET запросе - используем метод `exchange()`:

```java
HttpHeaders headers = new HttpHeaders();  
  
headers.add("Authorization", "Bearer " + "3jh234g2vj34k22jh3g43");  
  
HttpEntity<Void> request = new HttpEntity<>(headers);  
  
  
ResponseEntity<UserInfoResponseDto> response = restTemplate.exchange(                                                                                                                  src/main/java/com/innowise/orde +1 -1  
        getUserUriUnformatted + userId,                                                                                                                                                src/main/java/com/innowise/ord +22 -8  
        HttpMethod.GET,  
        request,  
        UserInfoResponseDto.class);
```


## Методы

- `T` ~={green}.getForObject(`String url, CLass<T> expectedReturnType`)=~ - для GET запросов

- `T` ~={green}.postForObject(`String url, HttpEntity<P> request,  Class<T> expectedReturnType`)=~ - для отправки POST запросов.



----
#### [[RestTemplate - Spring Web - Flashcards|Link to flashcards]]



---
### References:

