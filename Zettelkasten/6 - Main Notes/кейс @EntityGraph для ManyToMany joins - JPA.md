
12-06-2026 20:18

Status:

Tags:

---
# кейс @EntityGraph для ManyToMany joins - JPA


Есть объект `Order`, который хранит коллекцию промежуточных объектов `OrderItems`.

`OrderItems` хранит ссылки на `Order` и `Item`.

Граф:

```
Order
 └── items (List<OrderItem>)
      └── item (Item)
```

нужно сказать Hibernate:

1. Загрузи `Order.items`.
2. Для каждого `OrderItem` загрузи `item`.

> [!warning] ~={red}**~={purple}@EntityGraph=~ как аннотация не может использоваться со Specification**=~
> Нельзя просто переопределить метод и повесить `@EntityGraph`, потому что реализация приходит из `SimpleJpaRepository`.
>
> В данном случае удобнее использовать [[кейс custom Spring Data repository for EntityGraph with ManyToMany - Spring Data|кастомный Spring Data репозиторий]] 



---
## attributeNodes

Первый уровень дерева.

```java
@NamedEntityGraph(
    name = "order-with-items",
    attributeNodes = {
        @NamedAttributeNode("items")
    }
)
```

Здесь `@NamedAttributeNode("items")` означает:

> При загрузке `Order` также загрузить поле `items`.

Соответствует первому уровню дерева:

```text
Order
 └── items
```


---
## subgraph

Используется, когда нужно идти глубже первого уровня.

```java
@NamedAttributeNode(
    value = "items",
    subgraph = "items-subgraph"
)
```

Здесь:

- `value` — поле текущей сущности (`Order.items`);
    
- `subgraph` — имя подграфа, который описывает, что грузить внутри `OrderItem`.
    

---

## @NamedSubgraph

Описывает следующий уровень дерева.

```java
@NamedSubgraph(
    name = "items-subgraph",
    attributeNodes = {
        @NamedAttributeNode("item")
    }
)
```

Означает:

> Для каждого `OrderItem` дополнительно загрузить поле `item`.

Получается дерево:

```text
Order
 └── items
      └── item
```

---

## Полный пример

```java
@NamedEntityGraph(
    name = "order-with-items",
    attributeNodes = {
        @NamedAttributeNode(
            value = "items",
            subgraph = "items-subgraph"
        )
    },
    subgraphs = {
        @NamedSubgraph(
            name = "items-subgraph",
            attributeNodes = {
                @NamedAttributeNode("item")
            }
        )
    }
)
@Entity
public class Order {
}
```

---

## Как читать такой граф

Читаем сверху вниз:

```java
@NamedAttributeNode(
    value = "items",
    subgraph = "items-subgraph"
)
```

↓

```java
@NamedSubgraph(
    name = "items-subgraph"
)
```

↓

```java
@NamedAttributeNode("item")
```

То есть:

```text
Order
 └── items
      └── item
```

---

## Если нужна ещё большая глубина

Например:

```text
Order
 └── items
      └── item
           └── category
```

Тогда создаётся ещё один подграф:

```java
@NamedEntityGraph(
    name = "order-full",
    attributeNodes = {
        @NamedAttributeNode(
            value = "items",
            subgraph = "items-subgraph"
        )
    },
    subgraphs = {
        @NamedSubgraph(
            name = "items-subgraph",
            attributeNodes = {
                @NamedAttributeNode(
                    value = "item",
                    subgraph = "item-subgraph"
                )
            }
        ),
        @NamedSubgraph(
            name = "item-subgraph",
            attributeNodes = {
                @NamedAttributeNode("category")
            }
        )
    }
)
```

Дерево:

```text
Order
 └── items
      └── item
           └── category
```

---

### Шпаргалка

|Аннотация|Назначение|
|---|---|
|`@NamedEntityGraph`|Корень графа загрузки|
|`attributeNodes`|Какие поля текущей сущности загрузить|
|`@NamedAttributeNode`|Один узел дерева|
|`subgraph`|Ссылка на следующий уровень дерева|
|`@NamedSubgraph`|Описание следующего уровня дерева|
|`subgraphs`|Список всех подграфов внутри графа|

Мысленно всегда представляй `EntityGraph` как дерево навигации по объектам, где `attributeNodes` — это дочерние узлы текущей сущности, а `subgraph` — переход к следующему уровню вложенности.

----
#### [[кейс @EntityGraph для ManyToMany joins - JPA - Flashcards|Link to flashcards]]



---
### References:

