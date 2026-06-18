
06-10-2025 10:42

Status: #baby 

Tags: [[Git]] [[Java+]]

---
# Branching

#### Branch creation & switching

- `git branch <new_branch_name>` - добавление новой ветки
	
- `git checkout <branch_name>` - переход к новой ветке -> при создании коммита он будет прикреплен к новой ветке
(`git checkout -b <new_branch_name>` - создание + перемещение к новой ветке)


----
#### Относительне ссылки на коммиты

- Перемещение на один коммит назад `^` (`HEAD^`)

- Перемещение на несколько коммитов назад `~<num>` (`HEAD~4`)


`main^` означает "первый родитель ветки `main`".

`main^^` означает прародитель (родитель родителя) `main`

---
### Перемещение ветки (branch forcing)

Одна из наиболее распространённых целей, для которых используются относительные ссылки - это перемещение веток. Можно напрямую прикрепить ветку к коммиту при помощи опции `-f`. Например, команда:

`git branch -f main HEAD~3` - Переместит (принудительно) ветку `main` на три родителя назад от `HEAD`.


----
### Интеграция изменений

- `git merge <branch_to_be_merged_with_current_branch>`-  слияние двух веток и создание коммита

![[Pasted image 20250930133759.png]]

> [!note] 
> **`git merge <some_branch>` - добавит все коммиты ветки <some_branch> в текущую ветку**
> 
> Чтобы не добавлять все коммиты ветки `<some_branch>` (они ставятся после последнего коммита current branch и последним коммитом `<some_branch>`) можно использовать `--squash`:
> ```bash
> git merge --squash feature
> ```

### What happens step-by-step

Let’s say we start with this:
```mathematica
A---B---C  (main)
         \           
          D---E---F  (feature)
```

If you are on `main` and run:

```bash
git merge feature
```
Git does this:

- It **keeps all the existing commits** (`A, B, C, D, E, F`)
    
- Then creates a **new merge commit**, say `M`, that has **two parents** (`C` from `main` and `F` from `feature`).
    

Result:

```mathematica
A---B---C-----------M  (main)
         \         /           
          D---E---F  (feature)
```

✅ **All commits stay in their original order.**  
Nothing is rewritten or moved “on top.”  
Git just joins the two histories together with the merge commit `M`.

---

`git rebase <branch_to_be_rebased>` - слияние веток, все коммиты ветки `<branch_to_be_rebased>` присоединяются в начало текущей ветки, при возникновении merge конфликтов - они решаются вручную. 

~={red}_«Не rebase’ить ветки, которые уже разделяются с другими людьми»_=~

(Все конфликты решаются как [[Merge Conflicts - Git|merge conflicts]])

> [!warning]
> ветка `<branch_to_be_rebased>` - ~={green}НЕ удаляется=~
> 
> И parent branch  и current branch продолжат существовать, просто current branch теперь начинается с последнего коммита parent ветки.



![[Pasted image 20250930133900.png]]


### 🔹 What happens step-by-step

Let’s say your branches look like this:
```mathematica
A---B---C  (main)
         \           
          D---E---F  (feature)
```

If you run:

```bash
git checkout feature 
git rebase main
```

Git will:

1. Temporarily _remove_ commits `D`, `E`, and `F` (your branch commits).
    
2. Move your branch pointer to `main` (commit `C`).
    
3. Reapply each of your commits (`D`, `E`, `F`) **on top of C**, creating _new commits_ (`D'`, `E'`, `F'`).
    

Result:

```mathematica
`A---B---C---D'---E'---F'  (feature)`
```


### ✅ Diffrence between mege and rebase:

- **Merge**: combines histories (preserves original structure).
    
- **Rebase**: _rewrites history_ so it looks like you started from the latest version of the base branch.
    
- Both can cause conflicts, which you resolve the same way.


#### Interactive rebase:

При выполнении `git rebase -i HEAD~4` 
- `-i` сделает rebase интерактивным - откроется vim для редактирования того, какие коммиты хотим оставить

- Можно сменить положение коммита по порядку, переставив строчку с ним в редакторе (у нас в окошке строку с коммитом можно перенести просто мышкой).
- Можно "выкинуть" коммит из ребейза. Для этого есть `pick` - переключение его означает, что нужно выкинуть коммит.
- Наконец, можно соединить коммиты. В этом уровне игры у нас не реализована эта возможность, но, вкратце, при помощи этой функции можно объединять изменения двух коммитов.

До:

![[Pasted image 20260422135214.png]]  

- Дропнули C4 (в vim редакторе)

После:
![[Pasted image 20260422135255.png]]

Пример use case:
	***Внесение изменений в старый коммит:**
![[Pasted image 20260422135841.png]]



---

`git cherry-pick <commit_index>` - позволяет брать коммит из child branch и добавлять его в начало текущей ветки. 

(Все конфликты решаются как [[Merge Conflicts - Git|merge conflicts]])

![[Pasted image 20250930133949.png]]

---

![[Pasted image 20251006110153.png]]


### [[Merge Conflicts - Git|Merge Conflicts]]

----
#### [[Branching - Git - Flashcards|Link to flashcards]]



---
### References:

- [[Изменение, удаление коммитов & откат изменений - Git]]
- 