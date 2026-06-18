
13-01-2026 15:44

Status:

Tags: [[Docker]] [[Java+]]

---
# union filesystems - Docker

![[Pasted image 20260113155501.png]]

Позволяет накладывать разные file systems (branches) друг на друга, создавая общую файловую систему.

Содержимое такой united file system с одним и тем же путем (same path in both branches) will be merged together:

```
System A 
├─ Folder1  
│  ├─ indexA1.html  
│  └─ indexA1.js
└─ Folder2


System B 
├─ Folder1  
│  ├─ indexA2.html  
│  └─ indexA2.js
└─ Folder2
   └─ someFileA2.txt
   
   
United system
├─ Folder1 
│  ├─ indexA1.html  
│  ├─ indexA1.js   
│  ├─ indexA2.html  
│  └─ indexA2.js
└─ Folder2
   └─ someFileA2.txt
```


### Таким образом:

- позволяет эффективнее использовать место на диске~={cyan} (Many images share a common lower layer)=~

For example, if multiple containers from the same image are created on a single host, the container runtime only has to allocate a thin overlay specific to each container, while the underlying image layers can be shared.

----
#### [[union filesystems - Docker - Flashcards|Link to flashcards]]



---
### References:

