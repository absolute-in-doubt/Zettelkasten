
06-01-2026 11:06

Status:

Tags: [[Maven]] [[Java+]]

---
# pom.xml - Maven


1. Можно вынести свойство в отдельную переменную, например для стандартизации версий зависимостей 


```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">  
  <modelVersion>4.0.0</modelVersion>  
  <groupId>org.bsuir</groupId>  
  <artifactId>spring-data-crud</artifactId>  
  <packaging>war</packaging>  
  <version>1.0-SNAPSHOT</version>  
  <name>spring-data-crud Maven Webapp</name>  
  <url>http://maven.apache.org</url>  
  
  <properties>    
	  <spring-version>6.2.11</spring-version>  
  </properties>  
  
  <dependencies>    
	  <dependency>      
		  <groupId>junit</groupId>  
	      <artifactId>junit</artifactId>  
	      <version>3.8.1</version>  
	      <scope>test</scope>  
	    </dependency>  
	    <dependency>      
		    <groupId>org.springframework</groupId>  
		    <artifactId>spring-context</artifactId>  
		    <version>${spring-version}</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.springframework</groupId>  
		    <artifactId>spring-beans</artifactId>  
		    <version>${spring-version}</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.springframework</groupId>  
		    <artifactId>spring-core</artifactId>  
		    <version>${spring-version}</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.springframework</groupId>  
		    <artifactId>spring-webmvc</artifactId>  
		    <version>${spring-version}</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.springframework</groupId>  
		    <artifactId>spring-orm</artifactId>  
		    <version>${spring-version}</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.apache.tomcat.embed</groupId>  
			<artifactId>tomcat-embed-core</artifactId>  
		    <version>11.0.5</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.apache.tomcat.embed</groupId>  
		    <artifactId>tomcat-embed-jasper</artifactId>  
		    <version>11.0.5</version>  
	    </dependency>    
		<dependency>      
			<groupId>org.thymeleaf</groupId>  
		    <artifactId>thymeleaf-spring6</artifactId>  
		    <version>3.1.3.RELEASE</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.hibernate.validator</groupId>  
		    <artifactId>hibernate-validator</artifactId>  
		    <version>9.0.1.Final</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.hibernate.orm</groupId>  
		    <artifactId>hibernate-core</artifactId>  
		    <version>7.0.0.Final</version>  
	    </dependency>    
	    <dependency>      
		    <groupId>org.postgresql</groupId>  
		    <artifactId>postgresql</artifactId>  
		    <version>42.7.8</version>  
	    </dependency>  
    </dependencies>  
    <build>    
	    <finalName>spring-data-crud</finalName>  
	    <plugins>      
		    <plugin>        
			    <groupId>org.apache.maven.plugins</groupId>  
		        <artifactId>maven-compiler-plugin</artifactId>  
		        <configuration>          
			        <source>16</source>  
			        <target>16</target>  
		        </configuration>      
	        </plugin>    
		</plugins>  
	</build>
</project>
```

----
#### [[pom.xml - Maven - Flashcards|Link to flashcards]]



---
### References:

