**Test Execution Guide & QA Checklist**

---

### 1. Unified Auth & Onboarding UI (Dark Theme)

Verify that the login, invite-accept, and public onboarding screens share the exact same modern, dark-themed background and structural layout.

* **Login Screen (`/login`)**
* Verify the deep blue gradient background and ambient glowing orbs are rendered correctly.
* Confirm form fields (**Email** and **Password**) feature dark background styling, white text, and an indigo focus ring on selection.
* Trigger validation by submitting empty fields; verify red `.field-error` messages appear clearly.


* **Tenant Onboarding (Public)**
* Navigate to the public onboarding URL (`/onboard?code=YOUR_INVITE_CODE`).
* Confirm the overall structural layout and background match the `/login` screen.
* Fill out organization details and verify the functional behavior of the password strength meter.
* Submit valid details and verify successful redirection to the **Billing Dashboard**.



---

### 2. Internal Tenant Manager (Light Theme & Business Logic)

Verify that the internal admin tool utilizes standard light-theme form components, handles automatic slug generation correctly, and creates entities as expected.

* **Navigation & UI Consistency**
* Log in as a **Super Admin** and open the **Tenant Manager (Admin)** view.
* Confirm inputs match the standardized light theme (white background, dark text, gray borders).
* Ensure no duplicate input fields exist. The form should strictly contain:
* Organization Name
* Workspace Slug
* Admin Email
* Admin Password
* Admin Full Name




* **Auto-Slug Generation**
* Type `Acme Corporation` into the **Organization Name** field.
* **Expected:** The **Workspace Slug** field automatically populates with `acme-corporation`.


* **Manual Slug Override**
* Manually change **Workspace Slug** to `acme-test`.
* Update **Organization Name** to `Acme Corporation Updated`.
* **Expected:** The slug remains `acme-test` without being overwritten by the auto-generator.


* **Form Submission**
* Complete remaining fields and click **Create Workspace**.
* Verify the submit button switches to a dynamic `Creating...` state.
* Upon completion, verify a success alert banner displays and all form fields clear automatically.



---

### 3. Global Buttons & Loading Spinners

Ensure UI standardization updates have not regressed core interactive components across other application views.

* **CRM Pipeline / General Modals**
* Navigate to the **CRM** module and trigger a modal (e.g., *New Deal*).
* Inspect primary (**Create Deal**) and secondary/cancel buttons for correct padding, clear hover states, and proper contrast against white modal backgrounds.


* **Loading Indicators**
* Trigger an asynchronous action (e.g., drafting an email within a deal).
* Verify the loading spinner is clearly visible (indigo contrast on light backgrounds).



---

### 4. API Routing & Payload Verification

Inspect network calls in Browser Developer Tools during submission to ensure endpoints match backend routing specs.

| Form Context | Target Endpoint | Key Network Payload Requirements |
| --- | --- | --- |
| **Public Tenant Onboarding** | `POST /api/v1/auth/onboard` | Verify successful HTTP submission and expected auth payload response. |
| **Admin Tenant Manager** | `POST /api/v1/tenants/onboard` | Confirm payload contains synchronized field aliases (e.g., `name` alongside `org_name`, `email` alongside `admin_email`). |
