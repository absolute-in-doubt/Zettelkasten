
13-01-2026 15:34

Status:

Tags: [[Docker]] [[Java+]]

---
# Namespaces - Docker

![[Pasted image 20260113153724.png]]


Namespaces оборачивают ресурс в абстракцию. Таким образом, для ресурса это выглядит так, будто он работает в отдельной системе.


Примеры:

1. В namespace id процессов начинается с 1, т.е. может быть процесс с id=1, несмотря на то, что во всей системе процессов сотни. Т.е. это процесс с `id=<namespace_name>:1`

2. Root user from the inside of the containermay map to the user from an outside system (и этот внешний user не обязан быть root).

----
#### [[Namespaces - Docker - Flashcards|Link to flashcards]]



---
### References:

