
Theory for the cards: [[Namespaces - Docker]]

FILE TAGS: docker java_interview

Q: За счет чего достигается изоляция в docker контейнерах (один контейнер на видит другой и не мешает ему)?
A: За счет Linux `namespaces`. 
	
Namespaces оборачивают ресурс в абстракцию. Таким образом, для ресурса это выглядит так, будто он работает в отдельной системе.
	
	
Примеры:
	
1. В namespace id процессов начинается с 1, т.е. может быть процесс с id=1, несмотря на то, что во всей системе процессов сотни. Т.е. это процесс с `id=<namespace_name>:1`
	
2. Root user from the inside of the container may map to the user from an outside system (и этот внешний user не обязан быть root). 
<!--ID: 1770189508674-->
