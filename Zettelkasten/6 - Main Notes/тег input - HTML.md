
10-01-2026 11:04

Status:

Tags: [[Frontend]]

---
# тег input - HTML


### Выпадающий список как input:

Для этого вместо input используется тег `<select>`:
```html
<label for="city">Город:</label>
<select id="city" name="city">
  <option value="msk">Москва</option>
  <option value="spb">Санкт‑Петербург</option>
  <option value="mnsk">Минск</option>
</select>
```


---
### Input с подсказками:

```html
<label for="browser">Браузер:</label>
<input list="browsers" id="browser" name="browser">

<datalist id="browsers">
  <option value="Chrome">
  <option value="Firefox">
  <option value="Safari">
  <option value="Edge">
</datalist>
```

----
#### [[тег input - HTML - Flashcards|Link to flashcards]]



---
### References:

