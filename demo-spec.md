# Product Specification: User Registration & Login

## 1. User Registration

Users can create a new account by providing the following information:
- **Email** (required): Must be a valid email format. Must be unique in the system.
- **Password** (required): Minimum 8 characters, must contain at least 1 uppercase letter, 1 number, and 1 special character.
- **Full Name** (required): 2–100 characters.
- **Phone Number** (optional): Vietnamese phone format (10 digits, starts with 0).

On successful registration:
- System sends a verification email to the provided address.
- Account is created with status `pending_verification`.
- User cannot log in until email is verified.

## 2. Email Verification

- Verification link is valid for 24 hours.
- Clicking the link activates the account (status changes to `active`).
- Expired or already-used links show an appropriate error message.
- User can request a new verification email (max 3 resends per day).

## 3. User Login

Users log in with email and password.

- On success: return JWT access token (expires in 1 hour) and refresh token (expires in 7 days).
- On failure: return error message. After 5 consecutive failed attempts, lock the account for 15 minutes.
- Locked accounts display a message with remaining lockout time.

## 4. Password Reset

Users can reset their password via email:
1. User enters email on the "Forgot Password" page.
2. System sends a reset link valid for 1 hour.
3. User clicks the link and enters a new password (same rules as registration).
4. Old password is invalidated immediately after reset.
5. All active sessions are terminated after reset.
