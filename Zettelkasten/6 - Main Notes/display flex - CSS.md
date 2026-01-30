
10-01-2026 15:02

Status:

Tags:

---
# display flex - CSS

Включает флекс‑контейнер, внутри которого элементы (flex‑items) выстраиваются вдоль основной оси.

```css
.container {
  display: flex;
}
```

## flex-direction

Определяет направление основной оси.

- `row` — по горизонтали слева направо (по умолчанию).
    
- `row-reverse` — по горизонтали справа налево.
    
- `column` — по вертикали сверху вниз.
    
- `column-reverse` — по вертикали снизу вверх.

```css
.container {
  display: flex;
  flex-direction: row;
}
```



---
## flex-wrap

Определяет, переносятся ли элементы на новую строку/столбец.

- `nowrap` — без переноса (по умолчанию).
    
- `wrap` — элементы переносятся.
    
- `wrap-reverse` — переносятся, но в обратном направлении.
    
```css
.container {
  display: flex;
  flex-wrap: wrap;
}
```


---
## justify-content

Выравнивание элементов вдоль **основной оси** (горизонталь при `row`, вертикаль при `column`).

- `flex-start` — прижать к началу.
    
- `flex-end` — прижать к концу.
    
- `center` — по центру.
    
- `space-between` — первый у начала, последний у конца, между остальными равные промежутки.
    
- `space-around` — равные «поля» вокруг каждого элемента.
    
- `space-evenly` — равные расстояния между всеми элементами и краями контейнера.
    
```css
.container {
  display: flex;
  justify-content: space-between;
}
```


---
## align-items

Выравнивание элементов~={cyan} вдоль **перекрёстной оси** (перпендикулярной основной).=~

- `stretch` — растянуть по высоте/ширине контейнера (по умолчанию).
    
- `flex-start` — прижать к началу перекрёстной оси.
    
- `flex-end` — прижать к концу.
    
- `center` — по центру.
    
- `baseline` — по базовой линии текста.
    
```css
.container {
  display: flex;
  align-items: center;
}
```


---

## align-content

Работает, когда есть **несколько рядов/колонок** (т.е. при `flex-wrap`). Выравнивает _ряды_ вдоль перекрёстной оси.

- `stretch` — растянуть ряды (по умолчанию).
    
- `flex-start` — все ряды к началу.
    
- `flex-end` — все ряды к концу.
    
- `center` — все ряды по центру.
    
- `space-between`, `space-around`, `space-evenly` — распределение рядов с промежутками.
    
```css
.container {
  display: flex;
  flex-wrap: wrap;
  align-content: space-between;
}
```


---
## Дополнительно: gap, flex, align-self

- `gap` — расстояние между элементами.
    
```css
.container {
  display: flex;
  gap: 16px;
}
```


- `flex` — краткая запись для `flex-grow`, `flex-shrink`, `flex-basis` у элемента.
	
```css
.item {
  flex: 1;
}
```


- `align-self` — переопределяет `align-items` для конкретного элемента.
	
```css
.item--top {
  align-self: flex-start;
}
```


---
### References:

