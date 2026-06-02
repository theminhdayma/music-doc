---
type: implementation-plan
status: ready
agent: copilot
module: authentication
---

# Objective

Implement a minimal JWT-based authentication module for Buyer and Seller users.

# Approved Spec

[[2026-05-27_authentication-module]]

# Tasks

## Task 1: Create Entity and Repository
- File: `src/main/java/com/tien/ecommerce_music_be/entity/User.java`
- File: `src/main/java/com/tien/ecommerce_music_be/repository/UserRepository.java`

## Task 2: Create DTOs and Mapper
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/RegisterRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/LoginRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/response/UserResponse.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/response/AuthResponse.java`
- File: `src/main/java/com/tien/ecommerce_music_be/mapper/UserMapper.java`

## Task 3: Create Security Support
- File: `src/main/java/com/tien/ecommerce_music_be/security/JwtService.java`
- File: `src/main/java/com/tien/ecommerce_music_be/security/JwtAuthenticationFilter.java`
- File: `src/main/java/com/tien/ecommerce_music_be/security/AuthenticatedUser.java`
- File: `src/main/java/com/tien/ecommerce_music_be/security/UserDetailsServiceImpl.java`
- File: `src/main/java/com/tien/ecommerce_music_be/config/SecurityConfig.java`

## Task 4: Create Service and Controller
- File: `src/main/java/com/tien/ecommerce_music_be/service/AuthService.java`
- File: `src/main/java/com/tien/ecommerce_music_be/controller/AuthController.java`

## Task 5: Update Exception Handling
- File: `src/main/java/com/tien/ecommerce_music_be/advice/GlobalExceptionHandler.java`
- File: `src/main/java/com/tien/ecommerce_music_be/exception/ConflictException.java`

## Task 6: Verify
- Run: `./gradlew test`
- Run: `./gradlew compileJava`

# Review Checklist
- [ ] DTOs do not expose password
- [ ] Password is BCrypt-hashed
- [ ] JWT claims contain userId and role
- [ ] Register blocks ADMIN role
- [ ] Unit tests pass for AuthService
# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[Table-User]]
- [[ADR-Authentication-JWT]]
