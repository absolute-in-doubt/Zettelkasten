
10-01-2026 11:22

Status:

Tags:

---
# CSS‑градиенты: кратко

Градиент задаётся как значение свойства `background` или `background-image` через специальные функции: `linear-gradient()`, `radial-gradient()`, `conic-gradient()`.

---

### Линейный градиент: `linear-gradient`

Базовый синтаксис:

```css
background: linear-gradient(направление, цвет1, цвет2, ...);
```

### Примеры

```css
/* Сверху вниз (по умолчанию) */
background: linear-gradient(#ff0000, #0000ff);

/* Снизу вверх */
background: linear-gradient(to top, #ff0000, #0000ff);

/* Слева направо */
background: linear-gradient(to right, #ff0000, #0000ff);

/* Справа налево */
background: linear-gradient(to left, #ff0000, #0000ff);

/* из верхнего левого в нижний правый */
background: linear-gradient(to bottom right, #ff0000, #0000ff);


/* из нижнего левого в верхний правый */
background: linear-gradient(to top right, #ff0000, #0000ff);
```

#### Можно указывать позицию каждого цвета:

```css
background: linear-gradient(
  to right,
  #ff0000 0%,
  #ffff00 50%,
  #0000ff 100%
);
```


> [!tip] 
> Есть ещё конический и радиальный (кругом) градиент, но они редко применяются. Разве что в комбинации.


### Комбинирование градиентов

```css
background:
  linear-gradient(to right, rgba(0,0,0,0.5), transparent),
  linear-gradient(to bottom, #ff0000, #0000ff);
```


---
## Радиальный градиент: `radial-gradient`

Базовый синтаксис:
```css
background: radial-gradient(форма размер в_позиции, цвет1, цвет2, ...);
```

### Примеры:

```css
/* Круг из центра */
background: radial-gradient(circle, #ff0000, #0000ff);

/* Эллипс, смещённый к верху */
background: radial-gradient(ellipse at top, #ff0000, #0000ff);
```


---
### Конический градиент: `conic-gradient`

Базовый синтаксис:

```css
background: conic-gradient(from 0deg, red, yellow, lime, cyan, blue, magenta, red);
```

----
#### [[градиенты - CSS - Flashcards|Link to flashcards]]



---
### References:

