
30-01-2026 18:19

Status:

Tags: [[Jakarta.servlet]] 

---
# Servlet Listeners - jakarta.servlet


- _Request_:
    - `ServletRequestListener` используется для того, чтобы поймать момент создания и уничтожения запроса;
    - `ServletRequestAttributeListener` используется для прослушивания событий, происходящих с атрибутами запроса.
- _Context_:
    - `ServletContextListener` ~={cyan}позволяет поймать момент, когда контекст инициализируется либо уничтожается;=~
    - `ServletContextAttributeListener` используется для прослушивании событий, происходящих с атрибутами в контексте.
- _Session_:
    - `HttpSessionListener` позволяет поймать момент создания и уничтожения сессии;
    - `HttpSessionAttributeListener` используется при прослушивании событий происходящих с атрибутами в сессии;
    - `HttpSessionActivationListener` используется в случае, если происходит миграция сессии между различными JVM в распределённых приложениях;
    - `HttpSessionBindingListener` так же используется для прослушивания событий, происходящих с атрибутами в сессии. Разница между `HttpSessionAttributeListener` и `HttpSessionBindingListener` слушателями: первый декларируется в `web.xml`; экземпляр класса создается контейнером автоматически в единственном числе и применяется ко всем сессиям; второй: экземпляр класса должен быть создан и закреплён за определённой сессией «вручную», количество экземпляров также регулируется самостоятельно.


----
#### [[Servlet Listeners - jakarta.servlet - Flashcards|Link to flashcards]]



---
### References:

