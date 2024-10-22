# withDefaultPasswordEncoder() is deprecated

You're correct! The `withDefaultPasswordEncoder()` method has been deprecated in Spring Security 6.x. Instead, you should manually encode passwords using a proper password encoder like `BCryptPasswordEncoder`. Here's how you can modify your `UserDetailsService` bean accordingly:

### Updated Example Using `BCryptPasswordEncoder`:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.core.userdetails.User;

@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(); // Use BCrypt for password encoding
    }

    @Bean
    public UserDetailsService userDetailsService() {
        // Create users with encoded passwords
        UserDetails user1 = User.builder()
                .username("kiran")
                .password(passwordEncoder().encode("K@123")) // Encode password
                .roles("USER")
                .build();

        UserDetails user2 = User.builder()
                .username("som")
                .password(passwordEncoder().encode("s@123")) // Encode password
                .roles("ADMIN")
                .build();

        return new InMemoryUserDetailsManager(user1, user2);
    }

    // Additional configuration (e.g., HTTP security) can go here
}
```

### Key Changes:
1. **Using `BCryptPasswordEncoder`**:
    - The `PasswordEncoder` bean is created to use BCrypt, which is a more secure way to hash passwords.
    - You call `passwordEncoder().encode("password")` to encode the password before creating the `UserDetails`.

2. **Using `User.builder()`**:
    - Instead of the deprecated `withDefaultPasswordEncoder()`, you now use `User.builder()` to create user instances and specify roles, usernames, and encoded passwords.

### Benefits of Using `BCryptPasswordEncoder`:
- **Security**: BCrypt provides a strong hashing algorithm, making it much harder for attackers to crack hashed passwords compared to simpler hashing methods.
- **Adaptive**: The cost factor for BCrypt can be adjusted over time to increase the time it takes to compute the hash as computational power increases, thus improving security.

This configuration is much more aligned with best practices for securing user passwords in modern applications. If you have further questions or need additional functionality, feel free to ask!