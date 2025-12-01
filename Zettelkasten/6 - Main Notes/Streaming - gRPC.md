
18-11-2025 11:51

Status:

Tags: [[gRPC]] [[Java+]]

---
# Streaming - gRPC


## Server-side streaming:

### Server side:

Часть кода, переопределяющая метод из SomeServiceBaseImpl:

```java
@Override
public void serverSideStreamingGetListStockQuotes(Stock request, StreamObserver<StockQuote> responseObserver) {
    for (int i = 1; i <= 5; i++) {
        StockQuote stockQuote = StockQuote.newBuilder()
          .setPrice(fetchStockPriceBid(request))
          .setOfferNumber(i)
          .setDescription("Price for stock:" + request.getTickerSymbol())
          .build();
        responseObserver.onNext(stockQuote);
    }
    responseObserver.onCompleted();
}
```

~={orange}Просто используем `.onNext(...)` столько раз, сколько надо для отправки информации (=~не один раз~={orange}), после чего вызываем `.onCompleted()`.=~


### Client side:

Вызов rpc метода на клиенте через [[client - gRPC|Stub]]

> [!note]
> На клиенте получаем stub через внешний сгенерированный класс.
> Например:
> ```proto
> service Greeter {
>  rpc SayHello (HelloRequest) returns (HelloReply);
>}
>```
>Для такого proto сгенерируется:
>```java
>class Greeter{
>	//описания всех messages  и типов данных, объявленных в .proto
>}
>
>class GreeterGrpc{
>	class GreeterGrpcBaseImpl { /*класс, чей функционал мы расширяем*/}
>
>	// методы создания stub-ов, которые фактически имитирую GreeterGrpcBaseImpl
>}
>```

---
## Client-side streaming:

### Client Side:

```java
    // Client-side for a client streaming example
    MyServiceStub asyncStub = MyServiceGrpc.newStub(channel);
    StreamObserver<MyRequest> requestObserver = asyncStub.clientStream(new StreamObserver<MyResponse>() {
        @Override
        public void onNext(MyResponse response) {
            System.out.println("Server response: " + response.getMessage());
        }
        @Override
        public void onError(Throwable t) {
            System.err.println("Server error: " + t.getMessage());
        }
        @Override
        public void onCompleted() {
            System.out.println("Client stream completed.");
        }
    });

    // Send messages
    requestObserver.onNext(MyRequest.newBuilder().setData("data1").build());
    requestObserver.onNext(MyRequest.newBuilder().setData("data2").build());
    requestObserver.onCompleted(); // Signal end of client stream
```

Создаём анонимный `StreamObserver`, в котором переопределяем методы:
- `onNext(...)` - вызовется при зеркальном вызове `onNext(...)` на сервере и получит отправленные данные.


### Server-side

```java
    // Server-side for a client streaming example
    @Override
    public StreamObserver<MyRequest> clientStream(StreamObserver<MyResponse> responseObserver) {
        return new StreamObserver<MyRequest>() {
            List<MyRequest> receivedRequests = new ArrayList<>();
            @Override
            public void onNext(MyRequest request) {
                receivedRequests.add(request);
            }
            @Override
            public void onError(Throwable t) {
                System.err.println("Client stream error: " + t.getMessage());
            }
            @Override
            public void onCompleted() {
                // Process receivedRequests and send a single response
                MyResponse response = MyResponse.newBuilder().setMessage("Processed " + receivedRequests.size() + " requests").build();
                responseObserver.onNext(response);
                responseObserver.onCompleted();
            }
        };
    }

```


ПРи этом proto выглядит так:

```java
    service MyService {
      rpc ServerStream(Request) returns (stream /* !! здесь Stream !! */Response); 
      rpc ClientStream(stream Request) returns (Response);
      rpc BidiStream(stream Request) returns (stream /*! здесь Stream!*/ Response);
    }

```


----
#### [[Streaming - gRPC - Flashcards|Link to flashcards]]



---
### References:

- [[client - gRPC]]