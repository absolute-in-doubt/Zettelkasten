
07-01-2026 16:48

Status:

Tags: [[Java Core]]

---
# Set Implementations - Time Complexity

| Операция         | HashSet       | LinkedHashSet  | TreeSet     |
|------------------|---------------|----------------|-------------|
| add(e)           | O(1) среднее  | O(1) среднее   | O(log n)    |
| remove(e)        | O(1) среднее  | O(1) среднее   | O(log n)    |
| contains(e)      | O(1) среднее  | O(1) среднее   | O(log n)    |
| Добавление в "конец"  | неупорядочено | упорядочен по вставке, O(1) | отсортирован, O(log n) |
| Удаление с "конца"    | неупорядочено | упорядочен по вставке, O(1) | удаление max/min O(log n) |


---
### References:

- [[Set Interface]]
