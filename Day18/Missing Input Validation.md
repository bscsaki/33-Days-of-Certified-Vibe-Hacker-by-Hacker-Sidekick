### **\# Day 18: Missing Input Validation \- Hacker Sidekick Certified Vibe Hacker CTF Walkthrough**

**\#\# Challenge:** The application doesn't validate user input for format, length, or content, allowing malformed data and potential injection attacks. What is the exact method name in AuthService.java that registers new users without validating password strength or email format?

**\#\# Methodology:**  
We have solved previous challenges located in AuthService.java and so by now and by name you know this file handles user identity, authentication, authorisation, user registration. And we have almost analysed all the functions the file contains. One of the few left and the only method that was registering new users without password and email validation is the following one.

As you can see this method takes the user’s username, password and email as input. No validation takes place on any of the 3 parameters and the new user is registered in the database and saved. 

**\#\# The why:**  
Today’s weakness is classified by **CWE 20: Improper Input Validation** and as we saw the code receives the user’s input and performs no kind of validation.  
According to OWASP there are 2 types of validation **Syntactic**  and **Semantic**. The former checks that input matches the expected format, structure, or type, confirming the input is well formed before it gets used anywhere. The latter checks that a value is actually correct or meaningful in the context of the application, which usually means confirming its real world or business correctness rather than just its shape.   
Taking today’s function as an example, syntactic validation would mean checking that the email input looks like an email address matching a specific pattern. Or checking that the username meets the character limit and contains allowed characters. Semantic validation would mean checking to see if the username is unique or ensuring that the password chosen does not belong in a list of the most common passwords.

**\#\# Prevention:**  
Over the past 18 days the word input validation has been consistently one of the prevention methods for various of the other types of attacks that we saw, so how do you properly validate input? This question has many answers since input can be many things; email, file, string, link… From today’s challenge there was a username, email and password each of which requires its own kind of check.

**Username** \- This input needs to have a character allow list and length limit. According to the OWASP Proactive Controls here is a concrete regex pattern to validate against must have the following criteria,

- only lowercase letters, numbers, the underscore character in total 3-16 characters. 

**Email** \- This input is a little bit more complicated compared to usernames. The most practical approach is to check for,

- the local part, everything before the @ symbol, is \< 64 octets, the @ symbol is present, and the domain \< 255 octets. 

**Password** \- This input needs to be validated, then handled securely the moment it passes validation, before it ever touches the database. There is not a specific pattern of password and usually a mix of uppercase, lowercase, numbers, and symbols are required. The password needs a minimum length rather than a mix of character types, OWASP recommends at least 8 characters, and needs to be checked against a list of known breached passwords before it's accepted. The step after is to secure how the password is processed. Today's function uses the hash function from Day 11, which uses MD5, a hashing algorithm that is no longer considered cryptographically secure. Fixing this means replacing MD5 with a strong, slow hashing algorithm built for password storage, and once that's in place, a few additional controls strengthen the registration flow further. 

I also asked Hacker Sidekick for help. I instructed Sidekick to harden this method and secure the user registration process and here are its suggestions to this weakness.

Hardened user registration method:

public User registerUser(String username, String password, String email) {  
    **// \--- Defensive null checks \---**  
    if (username \== null || password \== null || email \== null) {  
        throw new IllegalArgumentException("All fields are required");  
    }

    **// \--- Sanitize & normalize \---**  
    String u \= username.trim();  
    String e \= email.trim().toLowerCase();

    **// \--- Length enforcement (match DB column constraints) \---**  
    if (u.length() \< 3 || u.length() \> 50\) {  
        throw new IllegalArgumentException("Username must be 3-50 characters");  
    }  
    if (e.isEmpty() || e.length() \> 255\) {  
        throw new IllegalArgumentException("Email must be 1-255 characters");  
    }  
    if (password.length() \< 12 || password.length() \> 128\) {  
        throw new IllegalArgumentException("Password must be 12-128 characters");  
    }

    **// \--- Format / content validation \---**  
    **// Alphanumeric, hyphens, underscores only; no control chars or spaces**  
    if (\!u.matches("^\[a-zA-Z0-9\]\[a-zA-Z0-9\_-\]{1,48}\[a-zA-Z0-9\]$")) {  
        throw new IllegalArgumentException("Username contains invalid characters");  
    }

    **// Basic but strict email pattern (blocks pathological / newline injection)**  
    if (\!e.matches("^\[A-Za-z0-9+\_.-\]+@\[A-Za-z0-9.-\]+\\\\.\[A-Za-z\]{2,}$")) {  
        throw new IllegalArgumentException("Invalid email format");  
    }

    **// Password complexity: upper, lower, digit, special character**  
    if (\!password.matches("^(?=.\*\[a-z\])(?=.\*\[A-Z\])(?=.\*\\\\d)(?=.\*\[^A-Za-z0-9\]).+$")) {  
        throw new IllegalArgumentException(  
                "Password must contain uppercase, lowercase, digit, and special character");  
    }

    **// \--- Uniqueness checks \---**  
    if (userRepository.findByUsername(u).isPresent()) {  
        throw new IllegalArgumentException("Username already taken");  
    }  
    if (userRepository.findByEmail(e).isPresent()) {  
        throw new IllegalArgumentException("Email already registered");  
    }

    **// \--- Secure persistence \---**  
    User user \= new User(u, hashPassword(password), e);  
    return userRepository.save(user);  
}

Additional Controls to Consider according to Hacker Sidekick,

- **Rate limiting**.	Add a @RateLimiter or servlet filter to block rapid registration attempts from the same IP/session.  
- **Case normalization**. Usernames should be stored case-insensitively (force .toLowerCase() on u if the app allows it) so Admin and admin are not distinct accounts.  
- **Return-value hygiene**.Do not return the full User object with the hashed password to the caller. Return a DTO or just an ID.  
- **Audit logging**. Log registration events (timestamp, username, IP) with a non-repudiable logger for incident response.

**\#\# Summary:**  
In this challenge of [Certified Vibe Hacker Workshop](https://certifiedvibehacker.com/) by [Hacker Sidekick](https://hackersidekick.com/) we saw a user registration method with no input validation for username, email or password. 

**\#\# Bibliography:**  
[CWE \- CWE-20: Improper Input Validation (4.20)](https://cwe.mitre.org/data/definitions/20.html)   
[Input Validation \- OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)   
[Designing an Effective Registration and Login System](https://wowdigital.com/blog/designing-effective-registration-and-login-system/)   
[Password Storage \- OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)   
[C3: Validate all Input & Handle Exceptions \- OWASP Top 10 Proactive Controls](https://top10proactive.owasp.org/the-top-10/c3-validate-input-and-handle-exceptions/) 