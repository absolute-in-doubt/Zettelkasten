
08-01-2026 17:34

Status:

Tags:

---
# Box Model - CSS

![[Pasted image 20260108173804.png]]


### Размеры

- `width`, `height` — размеры области содержимого (content box).
- `min-width`, `max-width`, `min-height`, `max-height` — ограничения размеров.

### Настройки интерпретации указанных размеров объекта

![[Pasted image 20260110115143.png]]


- `box-sizing` — управляет тем, как считаются итоговая ширина и высота элемента: учитываются ли в них `padding` и `border` или добавляются сверху.
	  - `content-box` (по умолчанию): размеры относятся только к содержимому.
	  - `border-box`: размеры включают всё вместе: content, padding и border.

```css
#el {
	box-sizing: {content-box, border-box};
}
```


---
### Поля и отступы

- `margin` — задаёт внешний отступ (можно использовать `auto` для центрирования).

- `padding` — задаёт внутренний отступ.

> [!tip] Note
> `margin` между вертикальными блоками могут "схлопываться"

---
### Границы

- `border-width` — толщина границы.
- `border-style` — стиль границы (solid, dashed, dotted и т. д.).
- `border-color` — цвет границы.
- `border-radius` — скругление углов границы.
- `outline` — внешняя линия, не влияющая на размеры элемента (в отличие от border).


---
### Вспомогательные свойства

- `overflow` — управляет поведением содержимого, выходящего за пределы:
	  - `visible`, `hidden`, `scroll`, `auto`.
	  
- `display` — определяет тип коробки (block, inline, flex, grid и др.).

- `position`, `top`, `right`, `bottom`, `left` — управление положением коробки в документе.

- `z-index` — задаёт порядок наложения перекрывающихся коробок.


---
## Визуальные эффекты

- `box-shadow` — тень от коробки.

- `background-clip` — определяет, до какого слоя (border, padding, content) рисуется фон.

- `background-origin` — задаёт, откуда начинается отрисовка фона (content-box, padding-box, border-box).

```css
/* box-shadow */

/* Простая внешняя тень */
.card {
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
}

/* Внутренняя тень */
.input {
  box-shadow: inset 0 0 4px rgba(0, 0, 0, 0.3);
}

/* Несколько теней */
.button {
  box-shadow:
    0 2px 4px rgba(0, 0, 0, 0.2),
    0 6px 12px rgba(0, 0, 0, 0.15);
}

/* Жёсткая тень без размытия */
.tag {
  box-shadow: 0 0 0 2px #1e90ff;
}

/* background-clip */

/* Фон обрезается по краю содержимого */
.clip-content {
  background: linear-gradient(135deg, #f093fb, #f5576c);
  background-clip: content-box;
  padding: 16px;
}

/* Фон обрезается по внутреннему краю рамки (значение по умолчанию) */
.clip-padding {
  background: linear-gradient(135deg, #43e97b, #38f9d7);
  background-clip: padding-box;
  border: 4px solid transparent;
}

/* Фон доходит до внешнего края рамки */
.clip-border {
  background: linear-gradient(135deg, #4facfe, #00f2fe);
  background-clip: border-box;
  border: 6px solid rgba(0, 0, 0, 0.2);
}

/* background-origin */

/* Координаты background от content-box */
.origin-content {
  background-image: url(pattern.png);
  background-repeat: no-repeat;
  background-origin: content-box;
  padding: 24px;
}

/* Координаты background от padding-box (значение по умолчанию) */
.origin-padding {
  background-image: url(pattern.png);
  background-repeat: no-repeat;
  background-origin: padding-box;
  padding: 24px;
}

/* Координаты background от border-box */
.origin-border {
  background-image: url(pattern.png);
  background-repeat: no-repeat;
  background-origin: border-box;
  border: 8px solid #000;
  padding: 24px;
}

```


----
#### [[Box Model - CSS - Flashcards|Link to flashcards]]



---
### References:

