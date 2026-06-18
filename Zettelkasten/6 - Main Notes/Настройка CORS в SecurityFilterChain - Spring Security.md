

03-03-2026 14:06

Status:

Tags: [[CORS]] [[Spring Security]]

---
# Настройка CORS в SecurityFilterChain - Spring Security


Настройка CORS в Spring SecurityFilterChain (Spring Boot 3+ / Security 6+) требует создания CorsConfigurationSource и его привязки к цепочке фильтров.

### Шаги настройки

1. Создайте bean `CorsConfigurationSource` для указания разрешённых origins, методов и заголовков.
    
2. В `SecurityFilterChain` примените .cors() с этой конфигурацией (до authorizeHttpRequests).
    
3. Убедитесь, что OPTIONS-запросы разрешены для preflight.
    

### Пример кода

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowedOriginPatterns(List.of("*")); // Или конкретные домены, напр. "https://example.com"
        config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE", "OPTIONS"));
        config.setAllowedHeaders(List.of("*"));
        config.setAllowCredentials(true);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", config);
        return source;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))
            .csrf(csrf -> csrf.disable()) // Если stateless API
            .authorizeHttpRequests(auth -> auth
                .requestMatchers(HttpMethod.OPTIONS, "/**").permitAll()
                .anyRequest().authenticated()
            );
        return http.build();
    }
}

```


----
#### [[Настройка CORS в SecurityFilterChain - Spring Security - Flashcards|Link to flashcards]]



---
### References:

