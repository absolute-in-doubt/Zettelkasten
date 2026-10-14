
Theory for the cards: 

FILE TAGS: grpc java


Q: Можно ли при изменении `.proto` контракта просто переназначить номер поля новому полю? Вот так:
Было:
	
```java
message Example {
	
	string name = 1;
	string nold_field = 2
}
```
	
Стало:
	
```java
message Example {
	
	string name = 1;
	string new_field = 2;
}
```
A: Нет, если изменить поле переназначив ему индекс сарого поля, новое поле  будет десериализовано в старое поле на сервисе, где не поменяли контракт. Чтобы такого не происходило - используем reserved:
	
```java
message Example {
	
	reserved 2;
	reserved "old_field"
	
	string name = 1;
}
```
<!--ID: 1790417058493-->


Q: Почему в proto файле номера полей в message начинаются с 1, а в enum - с нуля:
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
A: В enum индексы полей начинаются с нуля, а в полях Request response - с единицы.
	
В enum начало с нуля доступно, поскольку статус, обозначенный нулем логически значит ЗНАЧЕНИЕ ПО УМОЛЧАНИЮ. Допустим - обозначение статуса UNKNOWN при поллинге, чтобы отдавать хоть какой-то ответ пока мы не имеем конкретного статуса. Таким образом тот, кто поллит - понимает, что просто статус не известен еще, а иначе поллили бы не получая ответа и не знали, может с сервером чето или что.
<!--ID: 1790417438091-->


