
04-10-2026 15:30

Status:

Tags: [[Frontend]]

---
# XPath & parsing - Frontend




**Selector** — способ указать, какой элемент DOM нужно извлечь.

Основные варианты:

```css
.product-title
#product-123
[data-testid="price"]
```

или XPath:

```xpath
//div[@class='product-card']//span[@class='price']
```


---
## 2. CSS selectors

Наиболее распространённый вариант для обычного HTML scraping.

```css
.product-card .title
```

```css
[data-testid="price"]
```

```css
.product-card > h2
```


> [!note] 
> `data-testid` — это атрибут, который часто добавляют специально для **автоматизированных тестов**:
> ```html
> <div data-testid="product-card">
> ```
>
> Часто такие тестовые атрибуты могут называться немного иначе:
> ```html
> data-testid
>data-test
>data-qa
>data-cy
>data-component
>data-testid
> ```

### Плюсы

- простой синтаксис;
    
- обычно хорошо поддерживается парсерами;
    
- удобен для поиска по `class`, `id`, `data-*`;
    
- достаточно мощный для большинства задач.

### Минусы

Сложнее выразить сложные отношения между элементами.


---
# 3. XPath

XPath позволяет искать элементы **по структуре и отношениям между узлами**.

Например:

```html
<div class="product">
    <h2>iPhone 17</h2>
    <span class="price">999 €</span>
</div>
```

Можно написать:

```xpath
//div[@class='product']/span[@class='price']
```

или:

```xpath
//h2[text()='iPhone 17']/following-sibling::span
```

Последний вариант ищет `span`, расположенный после конкретного `h2`.

---

## 4. Полезный минимум XPath

#### По тегу

```xpath
//div
```

Все `div`.

#### По атрибуту

```xpath
//div[@class='product']
```

#### По нескольким условиям

```xpath
//div[@class='product' and @data-id='123']
```

#### По тексту

```xpath
//button[text()='Buy']
```

Частичное совпадение:

```xpath
//button[contains(text(), 'Buy')]
```

#### Поиск потомка

```xpath
//div[@class='product']//span
```

`//` означает потомка на любой глубине.

#### Поиск непосредственного ребёнка

```xpath
//div[@class='product']/span
```

#### Соседний элемент

```xpath
//h2/following-sibling::span
```

#### Родитель

```xpath
//span[@class='price']/parent::div
```

---

# 5. Главный принцип выбора selector

Старайся выбирать **стабильный семантический признак**, а не положение элемента.

### Хорошо

```css
[data-testid="product-price"]
```

```css
.product-card .price
```

```xpath
//div[@data-testid='product']//span[@class='price']
```

### Плохо

```css
div:nth-child(4) > div:nth-child(2) > span
```

```xpath
/html/body/div[2]/div[4]/div[1]/span[2]
```

Последние варианты зависят от конкретной структуры страницы и легко ломаются.

---

# 6. Осторожно с динамическими атрибутами

Плохо:

```html
<div id="a8f73bd92">
```

если ID генерируется случайно.

Также подозрительны:

```html
class="css-1x93abc"
id=":r17:"
data-reactid="739281"
```

Лучше искать:

```html
data-testid="price"
class="product-price"
```

если они стабильны.

---

# 7. Не всегда class — хороший selector

Например:

```html
<div class="css-1a2b3c product-card active">
```

Лучше:

```css
.product-card
```

чем:

```css
.css-1a2b3c
```

А если класс содержит несколько значений, можно использовать:

```css
div.product-card
```

или XPath:

```xpath
//div[contains(@class, 'product-card')]
```

---

# 8. Не злоупотреблять `nth-child`

Например:

```css
.product-list > div:nth-child(7)
```

означает:

> взять седьмой `div`.

Если сайт добавит один элемент перед ним — selector сломается.

Лучше:

```css
.product-list .product-card
```

и затем уже обработать **все** карточки.

---

# 9. Для парсера лучше иметь fallback

Вместо одного:

```text
price → .price
```

можно хранить:

```text
1. [data-testid="price"]
2. .product-card .price
3. span.price
```

И проверять их по порядку.

Если все перестали работать → вероятно, сайт изменился → можно запустить AI для поиска нового selector.

---

## 10. Хорошая стратегия для adaptive parser

```text
             HTML
               ↓
        known selectors
               ↓
          extraction
               ↓
          validation
          /         \
       success      fail
         ↓            ↓
       data          AI
                      ↓
               new selectors
                      ↓
                save/update
```

То есть **LLM лучше использовать для discovery/recovery, а не для каждого парсинга**.


Приоритет примерно такой:

**`data-testid` / semantic attributes → стабильные class/id → структура DOM → XPath relationships → `nth-child` / абсолютный XPath.**

----
#### [[XPath & parsing - Frontend - Flashcards|Link to flashcards]]



---
### References:

