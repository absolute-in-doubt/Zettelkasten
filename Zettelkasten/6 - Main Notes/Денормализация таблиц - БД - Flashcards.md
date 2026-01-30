
Theory for the cards: 

FILE TAGS: databases

Q: Когда хранение данных в денормализованном виде оправдано?
A:   
✅ Read-heavy applications (analytics, dashboards)  
	
✅ When query performance is critical  
	
✅ Data warehouses and OLAP systems  
	
✅ Caching frequently accessed data  
	
❌ Avoid in transactional systems (OLTP) where data consistency is critical
<!--ID: 1769324948374-->


Q: В чем плюсы и минусы нормализации?
A:   
**Плюсы**
- отсутствие избыточных данных
- экономия места на диске
- consistency (согласованность) - т.к. данные хранятся в одной таблице в единственном экземпляре
- быстрая запись
- проще содержать и обновлять данные
	
**Минусы**
- медленное чтение, нужны JOINs
<!--ID: 1769324948380-->

