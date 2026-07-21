Perfect. We'll stay in **implementation mode** from here.

From now on, I'll give you production-ready changes in small batches that you can complete in 15–30 minutes each.

---

# Batch 1 — Enterprise Foundation

**Goal:** Transform the existing collection into a reusable, automated SIT suite.

**Estimated effort:** 45–60 minutes.

---

# Task 1 — Create Collection Variables

At the **Collection** level, create these variables:

| Variable           | Initial Value                  |
| ------------------ | ------------------------------ |
| `baseUrl`          | `http://localhost:8000/api/v1` |
| `accessToken`      | _(blank)_                      |
| `refreshToken`     | _(blank)_                      |
| `userId`           | _(blank)_                      |
| `adminId`          | _(blank)_                      |
| `roleId`           | _(blank)_                      |
| `productId`        | _(blank)_                      |
| `categoryId`       | _(blank)_                      |
| `orderId`          | _(blank)_                      |
| `orderNumber`      | _(blank)_                      |
| `paymentReference` | _(blank)_                      |

These will replace hardcoded values throughout the collection.

---

# Task 2 — Update All URLs

Replace every occurrence of:

```text
http://localhost:8000/api/v1
```

with:

```text
{{baseUrl}}
```

For example:

```text
GET

{{baseUrl}}/products
```

instead of

```text
http://localhost:8000/api/v1/products
```

This makes the collection portable across local, Docker, and future environments.

---

# Task 3 — Update Authentication

Replace all Bearer Tokens with:

```text
Bearer {{accessToken}}
```

No request should contain a hardcoded JWT anymore.

---

# Task 4 — Login Request

Modify the Login request to use collection variables for credentials.

Request body:

```json
{
  "email": "{{loginEmail}}",
  "password": "{{loginPassword}}"
}
```

Add these variables to the environment (not the collection):

```
loginEmail
loginPassword
```

This avoids committing credentials to source control.

---

# Task 5 — Login Tests

In the **Tests** tab of the Login request, add logic to save the returned tokens and user information.

The exact JSON keys depend on your login response. Use the keys that your API returns.

```javascript
pm.test("Login Success", function () {
  pm.response.to.have.status(200);
});

const response = pm.response.json();

// Example — adjust key names if needed
pm.collectionVariables.set("accessToken", response.access_token);
pm.collectionVariables.set("refreshToken", response.refresh_token);
pm.collectionVariables.set("userId", response.user.id);

pm.test("Access Token Saved", function () {
  pm.expect(pm.collectionVariables.get("accessToken")).to.not.be.empty;
});
```

If your response shape differs (for example `token`, `data.access_token`, etc.), update the property names accordingly.

---

# Task 6 — Collection-Level Tests

At the **Collection** level, add these generic tests so every request benefits from consistent validation:

```javascript
pm.test("Response time < 1000 ms", function () {
  pm.expect(pm.response.responseTime).to.be.below(1000);
});

pm.test("Content-Type is JSON", function () {
  pm.expect(pm.response.headers.get("Content-Type")).to.include(
    "application/json",
  );
});
```

If you have health or download endpoints that intentionally return non-JSON, we can override those later.

---

# Task 7 — Variable Chaining

Whenever a request creates a new resource, save its identifier for later requests.

Examples:

### Create Product

```javascript
const response = pm.response.json();

pm.collectionVariables.set("productId", response.id);
```

### Create Order

```javascript
const response = pm.response.json();

pm.collectionVariables.set("orderId", response.id);
pm.collectionVariables.set("orderNumber", response.order_number);
```

This removes the need for hardcoded UUIDs.

---

# Task 8 — Replace Hardcoded IDs

Update URLs such as:

```
/products/<uuid>
```

to:

```
/products/{{productId}}
```

and similarly for:

- `{{orderId}}`
- `{{userId}}`
- `{{categoryId}}`

---

# Task 9 — Folder Organization

Reorganize the collection into folders like this:

```text
00 Health
01 Authentication
02 Products
03 Categories
04 Orders
05 Users
06 Roles
07 Admin
08 Dashboard
09 Payment
```

This ordering makes full collection runs predictable and easier to maintain.

---

# Validation Checklist

When you're done with Batch 1:

- [ ] Login succeeds.
- [ ] `accessToken` is populated automatically.
- [ ] No request contains a hardcoded JWT.
- [ ] All URLs use `{{baseUrl}}`.
- [ ] New product IDs are captured automatically.
- [ ] New order IDs are captured automatically.
- [ ] Collection runs without manually editing requests.

---

# After Batch 1

We'll immediately move to **Batch 2**, where we'll turn the Products module into a production-grade regression suite with positive/negative cases, validation scripts, pagination, search/filter coverage (if supported), and assertions for the new `image_url` field. After that we'll tackle the Orders module, which will become the most comprehensive part of the suite because of your recent schema enhancements.
