
18-11-2025 08:52

Status:

Tags: [[gRPC]] [[Java+]]

---
# Request-Response - gRPC


### Создание сервера

(на него будут приходить запросы, он будет отвечать)

```java
Server server = ServerBuilder.forPort(8080).addService(new GreetingServiceImpl()).build();

server.start();

server.awaitTermination();
```


---

### Логика обработки запроса-ответа

Maven-plugin автоматически генерирует классы запроса и ответа и сохраняет их в target.

Также, он генерирует класс-обработчик вопроса и ответа (в данном случае `GreetingServiceGrpc` ). В этом классе определено всё, кроме самого метода обработки запроса. 
Для этого расширяем внутренний класс этого `GreetingServiceGrpc` и переопределяем метод обработки запроса (тут `greeting`):

```java
import io.grpc.stub.StreamObserver;  
import org.bsuir.received_data_directory.GreetingServiceGrpc;  
import org.bsuir.received_data_directory.GreetingServiceOuterClass;  
  
public class GreetingServiceImpl extends GreetingServiceGrpc.GreetingServiceImplBase {  
    @Override  
    public void greeting(GreetingServiceOuterClass.HelloRequest request, StreamObserver<GreetingServiceOuterClass.HelloResponse> responseObserver) {  
        System.out.println(request);  
  
        GreetingServiceOuterClass.HelloResponse response = GreetingServiceOuterClass.HelloResponse  
                .newBuilder().setGreeting("Hello, " + request.getName()).build();  
			  
        responseObserver.onNext(response); //загружает ответ в поток  
		  
        responseObserver.onCompleted(); //означает, что мы больше не будем пересылать данные  
    }  
}
```


```java
syntax = "proto3"; //версия proto файла  
package org.bsuir.received_data_directory; //куда сохранится полученный файл  
  
message HelloRequest { //запрос, получаемый сервером  
  
  string name = 1; //name и 1 - это ключ. Передаётся фактически 1, но при получении 1 заменяется на name в конечном файле  
  
  repeated string hobbies = 2; //список строк  
  
}  
  
message HelloResponse {  
  string greeting = 1;  
}  
  
service GreetingService { //Сервис, ответственный за обработку этого gRPC запроса  
  rpc greeting(HelloRequest) returns (HelloResponse); //сигнатура метода, ответственного за обработку запроса  
}
```

----
#### [[Request-Response - gRPC - Flashcards|Link to flashcards]]



---
### References:

- [[Streaming - gRPC]]