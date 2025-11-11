
09-10-2025 20:16

Status: #baby 

Tags: [[Spring]]

---
# Bean scope

Можно явно указать в [[ApplicationContext.xml file - Spring|ApplicationContext.xml]].

```xml
	<bean id="musicPlayer" scope="prototype" class="secret.boy.spring.demo.MusicPlayer">  
    <property name="music">  
        <list>            
	        <ref bean="classicalMusic"/>  
            <ref bean="rockMusic"/>  
            <ref bean="grungeMusic"/>  
        </list>    
	</property>
</bean>
```

## Singleton scope

~={orange}Используется по умолчанию=~

Создаётся единственный объект класса. 
При каждом вызове getBean() возвращается ссылка на один и тот же объект

![[Pasted image 20251009201910.png]]

(Про "столкнёмся с проблемой" - очевидная хуйня)


## Prototype scope

![[Pasted image 20251009202330.png]]


## Request scope

~={pink}(no info yet)=~
## Session scope

~={pink}(no info yet)=~

## Global-session scope

~={pink}(no info yet)=~


> [!warning]  **Для prototype не работают destroy методы**
> Если bean является singleton, то он создаётся изначально, как только поднимается контекст. А все прототайпы создаются в тот момент, когда они нужны. Кто-то запросил прототайп — тогда Spring его создал, настроил, отдал и забыл про него.
>
>Это важно знать, потому что если вы, например, прописываете destroy-метод для бина, то для синглтона этот метод работать будет, а для прототайпа — нет. Когда контекст закрывается, Spring проходит по всем бинам, которые там хранятся (а это только синглтоны), находит их destroy-методы, если они прописаны, и запускает. А прототайпы Spring нигде не хранит, и поэтому destroy-метод для них работать не будет.


----
#### [[Bean scope - Flashcards|Link to flashcards]]



---
### References:

