
25-01-2026 09:29

Status:

Tags: [[Spring]]

---
# Спринговские BeanPostProcessors - Spring

`org.springframework.context.annotation.CommonAnnotationBeanPostProcessor`
	Обрабатывает аннотации ~={purple}@PreDestroy=~, ~={purple}@PostConstruct=~ и ~={purple}@Resource=~ 

`org.springframework.context.annotation.AutowiredAnnotationBeanPostProcessor`
	Обрабатывает ~={purple}@Autowired=~



---
#### xml namespaces

Т.к. вручную добавлять каждый обработчик анотаций заёбно - сделали namespaces, которые включают в себя несколько аннотаций:

- `context:component-scan` - добавляет обработчики class-level аннотаций (~={purple}@Controller=~, ~={purple}@Component=~ и т.д.)
	~={red}!!!=~ ~={yellow}При использовании `component-scan` все user-defined классы пакета, реализующие BeanPostProcessor, будут загружены автоматически.=~ ~={red}!!!=~ 

- `context:annotation-config` - добавляет обработчики method-level аннотаций (~={purple}@PostConstruct=~, ~={purple}@PreDestroy=~, ~={purple}@Autowired=~ и т.д.)

---
### References:

- [[ApplicationContext.xml file - Spring]]