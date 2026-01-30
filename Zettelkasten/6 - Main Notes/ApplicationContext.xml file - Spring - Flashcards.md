
Theory for the cards: [[ApplicationContext.xml file - Spring]]

FILE TAGS: spring

Q: Что делает параметр lazy-init-"true" при конфигурации бина? Для каких бинов он имеет смысл?
A: Параметр lazy-init="true" устанавливается для singleton бинов для того, чтобы их экземпляр не создавался при инициализации контекста. Экземпляр такого бина будет создан при первом использовании этого бина в коде.
	
Если бина, отмеченного lazy-init="true" не существует, BeanDefinitionsReader всё равно загрузит его в Map в `DefaultListableBeanFactory` и ошибка `ClassNotFoundException` будет выкинута не при старте приложения, а в момент первого ипользования данного бина.
<!--ID: 1769238604907-->
