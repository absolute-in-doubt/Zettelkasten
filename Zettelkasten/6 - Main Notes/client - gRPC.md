
18-11-2025 09:50

Status:

Tags: [[gRPC]] [[Java+]]

---
# client - gRPC

```java
ManagedChannel channel = ManagedChannelBuilder  
            .forTarget("localhost:8080")  
            .usePlaintext()  
            .build();  
  
    //Stub - имитация объекта, где типо есть нужные нам методы локально, но в реале - Stub вызывает их удалённо  
    GreetingServiceGrpc.GreetingServiceBlockingStub stub =  GreetingServiceGrpc.newBlockingStub(channel);  
  
    GreetingServiceOuterClass.HelloRequest request = GreetingServiceOuterClass.HelloRequest  
            .newBuilder().setName("Jane").build();  
  
    GreetingServiceOuterClass.HelloResponse response = stub.greeting(request);  
  
    System.out.println("Received: " + response.getGreeting());  
  
    channel.shutdownNow();  
```


----
#### [[client - gRPC - Flashcards|Link to flashcards]]



---
### References:

- [[Streaming - gRPC]]