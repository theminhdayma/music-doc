# User Flow - Authentication

## Goal

Allow public users to register as BUYER or SELLER, verify their email with OTP, reset passwords, and log in to receive a JWT access token.

## Actors

- Public user
- BUYER
- SELLER
- ADMIN

## Main Flow: Register

1. User opens the registration form.
2. User enters email, password, full name, and selects BUYER or SELLER.
3. System validates required fields and role.
4. System checks that the email is unique.
5. System hashes the password using BCrypt.
6. System saves the new user with `ACTIVE` status, `emailVerified = false`, and profile fields left `NULL`.
7. System sends a 6-digit verification code to the user's email.
8. User verifies the email with the OTP.
9. System marks the account as verified and returns a JWT access token.

## Main Flow: Login

1. User opens the login form.
2. User enters email and password.
3. System checks that the account exists and is verified.
4. System authenticates the credentials.
5. System returns a JWT access token and safe user data.

## Main Flow: Forgot Password

1. User opens the forgot-password form.
2. User enters email.
3. System generates a password reset OTP and sends it to email.
4. User submits email, OTP, and a new password.
5. System verifies the OTP and updates the password.

## Main Flow: Update Profile

1. Authenticated user opens the profile page.
2. System loads the current user data from JWT identity.
3. User updates allowed profile fields such as full name, avatar URL, phone number, address, bio, and date of birth.
4. System saves the changes and returns the updated profile.

## Main Flow: Logout

1. Authenticated user clicks logout.
2. Client sends the current JWT in the `Authorization` header.
3. System blacklists the token until it expires.
4. Subsequent requests with the same token are rejected.

## Alternate Flows

- If email already exists, registration is rejected.
- If the user selects ADMIN, registration is rejected.
- If the account is not verified, login is rejected until OTP verification is completed.
- If credentials are invalid, login is rejected.
- If the account is deleted or inactive, login is rejected.
- If the verification or reset OTP is invalid or expired, the request is rejected.
- If a logged-out token is reused, protected endpoints reject it.

## Output Rules

- Password data must never be returned in API responses.
- The JWT token must contain the user id and role.

# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[ADR-Authentication-JWT]]
