That's the correct order for an enterprise sprint review.

# Sprint 2 Sign-Off Sequence

```text
1. Unit Tests
2. Manual Integration Test Evidence
3. Git Commit & Push
4. Tech Lead Review
5. Sprint Closure
```

---

# 1. Unit Tests (Developer Deliverable #1)

## Folder Structure

```text
tests/
│
├── unit/
│   ├── core/
│   │   └── test_security.py
│   │
│   └── auth/
│       ├── repositories/
│       │   └── test_auth_repository.py
│       │
│       └── services/
│           └── test_auth_service.py
```

---

## A. Security Tests

### test_security.py

Expected Test Cases

```python
test_hash_password()

test_verify_password_success()

test_verify_password_failure()

test_create_access_token()

test_create_refresh_token()
```

Expected Result

```bash
pytest tests/unit/core/test_security.py -v
```

Output

```text
5 passed
```

---

## B. Service Tests

### test_auth_service.py

Expected Test Cases

```python
test_register_user_success()

test_register_duplicate_user()

test_login_success()

test_login_invalid_password()

test_login_user_not_found()
```

Use:

```python
pytest-mock
AsyncMock
```

Expected Result

```text
5 passed
```

---

## C. Repository Tests

Optional for Sprint 2

Expected Cases

```python
test_get_user_by_email()

test_create_user()
```

Expected Result

```text
2 passed
```

---

## Unit Test Evidence Required

Developer should provide:

### Terminal Output

```bash
pytest tests/unit -v
```

Expected

```text
========================
12 passed
========================
```

### Screenshot

Capture:

```text
Terminal
Pytest execution
Green PASS status
```

---

# Unit Test Sign-Off

Checklist

```text
□ Security tests pass
□ Service tests pass
□ Repository tests pass
□ No failing tests
□ No skipped tests
```

---

# 2. Manual Integration Test Evidence (Developer Deliverable #2)

After unit tests pass.

---

## Test 1

### Migration

Command

```bash
alembic upgrade head
```

Evidence

Screenshot:

```text
Migration successful
```

---

## Test 2

### Role Seed

Command

```sql
SELECT * FROM roles;
```

Evidence

Screenshot:

```text
ADMIN
CUSTOMER
```

---

## Test 3

### Swagger Available

URL

```text
http://localhost:8000/docs
```

Evidence

Screenshot:

```text
Authentication Section
/register
/login
```

---

## Test 4

### Registration Success

Request

```json
{
  "email": "admin@test.com",
  "password": "Admin123",
  "role_id": "<uuid>"
}
```

Evidence

Screenshot:

```text
200 OK
User Created
```

---

## Test 5

### Verify Password Hash

Query

```sql
SELECT email,password_hash
FROM users;
```

Evidence

Screenshot

Expected

```text
$2b$12...
```

NOT

```text
Admin123
```

---

## Test 6

### Login Success

Request

```json
{
  "email":"admin@test.com",
  "password":"Admin123"
}
```

Evidence

Screenshot

Expected

```json
{
 "access_token":"...",
 "refresh_token":"..."
}
```

---

## Test 7

### Invalid Login

Request

```json
{
 "email":"admin@test.com",
 "password":"wrong"
}
```

Evidence

Screenshot

Expected

```text
401 Unauthorized
```

---

## Test 8

### Angular Login

Evidence

Screenshot

```text
Login Screen
```

and

```text
Network Tab
POST /login
200 OK
```

---

## Test 9

### Local Storage

Evidence

Browser DevTools

```text
access_token
refresh_token
```

visible.

---

# Integration Evidence Package

Developer should submit:

```text
01_Migration.png

02_Roles.png

03_Swagger.png

04_Register.png

05_UserTable.png

06_LoginSuccess.png

07_LoginFailure.png

08_AngularLogin.png

09_LocalStorage.png
```

---

# Integration Test Sign-Off

Checklist

```text
□ Migration successful
□ Roles inserted
□ Register working
□ Password hashed
□ Login working
□ Invalid login blocked
□ JWT generated
□ Angular login working
□ Tokens stored
```

---

# 3. Git Commit & Push (Developer Deliverable #3)

Only after tests pass.

---

## Verify Changes

```bash
git status
```

Expected

```text
clean working tree
```

---

## Commit Sequence

### Commit 1

```bash
git add .

git commit -m "feat(auth): add user and role entities"
```

---

### Commit 2

```bash
git commit -m "feat(auth): implement jwt and password hashing"
```

---

### Commit 3

```bash
git commit -m "feat(auth): add repository and service layer"
```

---

### Commit 4

```bash
git commit -m "feat(auth): add authentication api endpoints"
```

---

### Commit 5

```bash
git commit -m "test(auth): add unit tests"
```

---

### Commit 6

```bash
git commit -m "feat(auth-ui): add angular authentication service"
```

---

## Push

```bash
git push origin feature/auth-foundation
```

---

## Tag Sprint

```bash
git tag sprint-2-auth-foundation

git push origin sprint-2-auth-foundation
```

---

# Final Sprint 2 Review Package

Before closing Sprint 2, ask the developer to submit:

```text
1. Source Code

2. Unit Test Report
   (pytest output)

3. Integration Evidence
   (9 screenshots)

4. Git Commit History

5. PR Link

6. Sprint Demo Notes
```

If all six are available, you can confidently move to **Sprint 2.1 – JWT Middleware, Refresh Endpoint, Angular Interceptor, Route Guards, and RBAC Foundation**.
