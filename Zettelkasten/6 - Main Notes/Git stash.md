
04-01-2026 12:12

Status:

Tags: [[Git]] [[Java+]]

---
# Git stash

**Назначение:**  
Временно сохранить незакоммиченные изменения и очистить рабочую директорию, чтобы можно было безопасно переключаться между ветками.

**Основные команды:**
- `git stash` — спрятать все отслеживаемые незакоммиченные изменения.
- `git stash push -m "msg"` — то же, но с комментарием.
- `git stash list` — посмотреть список stashey.
- `git stash show -p` — показать, что хранится в верхнем stash.
- `git stash apply` — применить верхний stash, не удаляя его из списка.
- `git stash apply stash@{N}` — применить конкретный stash.
- `git stash pop` — применить верхний stash и удалить его из списка.
- `git stash drop stash@{N}` — удалить конкретный stash.
- `git stash clear` — удалить все stashey.

**Типичный сценарий:**
1. Работаешь над задачей, есть незакоммиченные изменения.  
2. Приходит срочный hotfix → `git stash`.  
3. Переключаешься на нужную ветку → `git checkout hotfix-branch`.  
4. Чинишь, коммитишь, пушишь.  
5. Возвращаешься на свою ветку → `git checkout feature-branch`.  
6. Восстанавливаешь работу → `git stash pop`.



----
#### [[Git stash - Flashcards|Link to flashcards]]



---
### References:

