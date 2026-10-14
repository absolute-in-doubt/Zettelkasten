
12-10-2026 12:44

Status:

Tags: [[pandas - Python]] [[Python]]

---
# Indexing & reindexing - Pandas




### Reindexing

После фильтрации dataframe мы получили новый df, но индексы остались (самая левая колонка):
![[Pasted image 20261012124524.png]]


Чтобы сбросить индексы и они снова начинались с нуля используем reindex:

```python
new_df = new_df.reset_index() # Просто добавит новый индекс без удаления колонки старого индекса

new_df = new_df.reset_index(drop=True) # Создаст новую колонку индекса и удалит старую



```

----
#### [[Indexing & reindexing - Pandas - Flashcards|Link to flashcards]]



---
### References:

