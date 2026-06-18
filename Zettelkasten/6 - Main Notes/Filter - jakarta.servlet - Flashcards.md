
Theory for the cards: [[Filter - jakarta.servlet]]

FILE TAGS: java_interview

Q: Для чего нужны фильтры в Jakarta servlet?
A:   
- Prevent downstream (продвижение далее по **FilterChain**) `Filter` instances or the `Servlet` from being invoked. In this case, the `Filter` typically writes the `HttpServletResponse`.
	
-  Modify the `HttpServletRequest` or `HttpServletResponse` used by the downstream `Filter` instances and the `Servlet`.
<!--ID: 1769790671751-->
