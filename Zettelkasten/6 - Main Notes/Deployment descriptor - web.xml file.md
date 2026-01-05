
05-01-2026 10:04

Status:

Tags: [[Jakarta.servlet]] [[Java+]]

---
# Deployment descriptor - web.xml file

В Java/Jakarta web‑приложении файл `WEB-INF/web.xml` является **deployment descriptor**: в нём описываются сервлеты, фильтры, слушатели и их URL‑маппинг.


```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE web-app
    PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd">

<web-app>
  
  <servlet>
    <servlet-name>controlServlet</servlet-name>
    <servlet-class>com.jenkov.webui.ControlServlet</servlet-class>
    <init-param><param-name>container.script.static</param-name>
                <param-value>/WEB-INF/container.script</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

  <servlet-mapping>
    <servlet-name>controlServlet</servlet-name>
    <url-pattern>*.html</url-pattern>
  </servlet-mapping>
</web-app>
```


----
#### [[Deployment descriptor - web.xml file - Flashcards|Link to flashcards]]



---
### References:

