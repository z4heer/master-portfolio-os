### **Sprint 09 Technical Brief: Backend Infrastructure**

To maintain our **Clean Architecture** and **Repository-Service pattern**, the Backend Engineer must implement the following structures.

#### **1. Database Schema (SQLAlchemy 2.x / PostgreSQL 17)**
The implementation must support the "Security First" goal by ensuring strict data types and relationship constraints.

*   **`addresses` Table:**
    *   `id`: UUID (Primary Key)
    *   `user_id`: UUID (Foreign Key to `users.id`, index for performance)
    *   `address_type`: Enum ('SHIPPING', 'BILLING')
    *   `full_name`: String(255)
    *   `address_line1`: String(255)
    *   `address_line2`: String(255), nullable
    *   `city`: String(100)
    *   `state_province`: String(100)
    *   `postal_code`: String(20)
    *   `country_code`: String(2) (ISO 3166-1 alpha-2)
    *   `is_default`: Boolean (default=False)

*   **`payments` Table:**
    *   `id`: UUID (Primary Key)
    *   `order_id`: UUID (Unique Foreign Key to `orders.id`)
    *   `transaction_id`: String (Gateway reference)
    *   `amount`: Numeric(12, 2)
    *   `currency`: String(3) (Default 'USD')
    *   `status`: Enum ('PENDING', 'COMPLETED', 'FAILED', 'REFUNDED')
    *   `provider`: String (e.g., 'STRIPE_SANDBOX')

#### **2. Data Transfer Objects (Pydantic v2)**
As per our **Coding Standards**, the backend must return DTOs only.

*   **`AddressCreate` / `AddressResponse`:** Strict validation for postal codes and country codes.
*   **`PaymentRequest`:** Must include `order_id` and a mock `payment_method_id`.
*   **`OrderCheckoutRequest`:** A composite DTO including `shipping_address_id`, `billing_address_id`, and `payment_info`.

---

### **Execution Instructions for the Engineering Manager**

1.  **Backend Session:** Copy the **"Backend Execution Prompt"** from our previous turn and append this **Technical Brief** to it. This ensures the specialist AI doesn't "hallucinate" a schema that breaks our existing database modularity.
2.  **Frontend Session:** Once the Backend Engineer provides the DTO structures, feed them into the **Frontend Engineer** chat to ensure the **Angular Signals** and **Angular Material** forms are perfectly typed.
3.  **The Reporting Loop:** As soon as the `AddressService` and `PaymentService` are coded, bring the **Completion Report** back here. I will then validate it against the **Definition of Done** (tests, builds, architecture check) before we move to the UI implementation.

**Would you like me to provide the specific Pytest test cases for the Payment logic to ensure the QA Engineer has a baseline for validation?**
