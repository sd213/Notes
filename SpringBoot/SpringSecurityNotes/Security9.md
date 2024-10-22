# how to encode/hash password in springboot  with salting  5 ,  10, 16 times
In Spring Boot, using `BCryptPasswordEncoder` inherently provides salting, as it automatically generates a unique salt for each password hash. The `BCrypt` algorithm uses a cost factor that you can adjust, which effectively increases the hashing complexity (or iterations). While you don’t manually set the salt, you can influence the salting process through the cost factor.

### Understanding Cost Factor
The cost factor determines how many times the password hashing algorithm is applied. The higher the cost factor, the more times the algorithm hashes the password, which increases the time required to compute the hash. This makes it harder for attackers to crack passwords using brute force.

### Encoding Password with Different Cost Factors
Here’s how to implement password encoding in Spring Boot with `BCryptPasswordEncoder`, showing examples with different cost factors (which determine how many times the hashing is done).

### Step 1: Add Dependencies
Make sure you have Spring Security included in your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### Step 2: Create a Password Encoder Bean
You can create multiple instances of `BCryptPasswordEncoder` with different cost factors for demonstration purposes.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder5() {
        return new BCryptPasswordEncoder(5); // Cost factor of 5
    }

    @Bean
    public PasswordEncoder passwordEncoder10() {
        return new BCryptPasswordEncoder(10); // Cost factor of 10
    }

    @Bean
    public PasswordEncoder passwordEncoder16() {
        return new BCryptPasswordEncoder(16); // Cost factor of 16
    }
}
```

### Step 3: Encoding Passwords with Different Salts
Now, let’s create a service where you can encode passwords with the different cost factors:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private PasswordEncoder passwordEncoder5;

    @Autowired
    private PasswordEncoder passwordEncoder10;

    @Autowired
    private PasswordEncoder passwordEncoder16;

    public void registerUser(String username, String rawPassword) {
        // Encode the password with different cost factors
        String encodedPassword5 = passwordEncoder5.encode(rawPassword);
        String encodedPassword10 = passwordEncoder10.encode(rawPassword);
        String encodedPassword16 = passwordEncoder16.encode(rawPassword);

        // Print the encoded passwords (in real scenario, save them in a database)
        System.out.println("Encoded password with cost 5: " + encodedPassword5);
        System.out.println("Encoded password with cost 10: " + encodedPassword10);
        System.out.println("Encoded password with cost 16: " + encodedPassword16);
    }
}
```

### Step 4: Using the UserService in a Controller
You can use the `UserService` in a controller to register users:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping("/register")
    public void register(@RequestParam String username, @RequestParam String password) {
        userService.registerUser(username, password);
    }
}
```

### Example Request
To register a user, send a POST request to `/api/users/register` with the `username` and `password` parameters.

### Important Notes:
1. **Cost Factor Implications**: Higher cost factors result in longer hashing times, which can slow down the authentication process. Use a cost factor that balances security and performance based on your application needs.
2. **Salting**: Each encoded password will have its own unique salt due to the nature of `BCrypt`, making it resistant to rainbow table attacks.
3. **Password Verification**: Use the `matches` method to verify passwords during authentication.

### Verifying Passwords
To verify a password, you can use:

```java
boolean isMatch = passwordEncoder5.matches(rawPassword, encodedPassword5);
```

### Summary
Using `BCryptPasswordEncoder` with different cost factors allows you to encode passwords securely in Spring Boot. Each password will be hashed with its unique salt, ensuring that even if two users have the same password, their hashes will be different. If you need further assistance or have more questions, feel free to ask!