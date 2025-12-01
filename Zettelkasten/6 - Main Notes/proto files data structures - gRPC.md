
18-11-2025 12:54

Status:

Tags: [[gRPC]] [[Java+]]

---
# proto files data structures - gRPC


### Enum:

```java
syntax = "proto3";

package demo;

// Определяем enum
enum Status {
  STATUS_UNKNOWN = 0;   // Всегда начинаем с 0
  STATUS_ACTIVE = 1;
  STATUS_INACTIVE = 2;
  STATUS_PENDING = 3;
}

// Сообщение, содержащее enum
message User {
  string id = 1;
  Status status = 2;
}

// Сервис
service UserService {
  rpc GetUser (UserRequest) returns (User);
}

message UserRequest {
  string id = 1;
}

```




---
### References:

