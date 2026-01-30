
08-01-2026 17:17

Status:

Tags: [[CSS]] [[Frontend]]

---
# Общие теги положения элементов - CSS


### Позиционирование элемента

```css
.some-class: {
	position: { static, relative, absolute, fixed, sticky};
}
```


`static` - (default) элемент позиционируется как обычно. ~={orange}И на него не влияют параметры  `top`, `right`, `bottom`, and `left`.=~

`relative` - позиция элемента рассчитывается относительно нормального положения. ~={orange}Но теперь `top`, `right`, `bottom`, and `left` влияют на рассчёт конечного положения элемента.=~

`absolute` - элемент не виден для других элементов. (~={orange}Относительно него ничего нельзя спозиционировать.=~ "The element is removed entirely from the normal document flow and other elements flow as if it did not exist").  Элемент позиционируется относительно его длижайшего родителя с параметром `position` любым, кроме `static` (если таких нет, то относительно browser window/viewport)

`fixed` - то же самое, что и `absolute`, но элемент устанавливается относительно viewport -> ~={orange}При движении страницы вверх-вниз элемент остаётся на том же месте экрана=~. ~={blue}useful for creating sticky headers or footers=~

`sticky` - гибрид `fixed` и `relative` - элемент ведёт себя как `relative`, пока не достигает определённой координаты (defined by `top`, `right`, `bottom`, or `left`) на экране (при движении страницы). После этого ведёт себя как `sticky`.


---
### Z-index

Работает для элементов, у которых `position` не `static`.


```css
#el1 : {
	position: relative;
	z-index: 1; /* маленький z-index -> будет снизу*/
}

#el2: {
	position: relative;
	z-index: 2; /* z-index больше -> будет сверху*/
}
```

----
#### [[Общие теги положения элементов - CSS - Flashcards|Link to flashcards]]



---
### References:

