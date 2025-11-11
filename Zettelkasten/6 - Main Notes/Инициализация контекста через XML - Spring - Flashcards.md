
Theory for the cards: [[Инициализация контекста через XML - Spring]]

FILE TAGS: spring

Q: Зачем в Spring у бинов нужен init-method, если есть конструктор?
A:   
- При вызове конструктора только создаётся объект (ну и инжектятся зависимости конструктора). -> Мы можем выполнять ту логику инициализации класса, которая НЕ требует вставленные зависимости (injected beans)
- При вызове init-method все остальные бины уже injected into the class instance. -> Мы можем выполнять ту логику инициализации класса, которая требует вставленные зависимости (injected beans)
<!--ID: 1761981245753-->


Q: Describe the process of resolving bean's dependencies (other beans), the annotation @Autowired? Which class implements its logic? When does it happen?
A: Логика @Autowired реализовывается классом AutowiredAnnotationBeanPostProcesor, который реализует BeanPostProcessor. При инициализации бина сначала вызывается его конструктор, после чего вызывается метод `postProcessBeforeInitialization()` который и реализует метод вставки зависимости в аннотированный setter или поле. 
	
Далее уже вызывается init-method (или метод, аннотированный как @PostConstruct), где происходит инциализация с использованием injected beans.
	
Далее вызывается второй метод AutowiredAnnotationBeanPostProcessor, а именно `postProcessAfterInitialization()`
<!--ID: 1761981245760-->
