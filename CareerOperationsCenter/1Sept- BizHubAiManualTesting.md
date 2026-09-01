Here is a comprehensive manual testing playbook to verify the new Tailwind UI standardization (Issue #104) across all the main modules.

Since you are testing the visual changes, keep an eye out for consistency in buttons, form fields, and error messages.

### Preparation

1. Ensure your backend is running in one terminal (`fastapi dev app/main.py` or your standard startup command).
2. Ensure your frontend is running in another terminal (`cd frontend && npm start`).
3. Open your browser to `http://localhost:4200` (preferably in an Incognito/Private window so you start fresh).

---

### Step 1: Authentication & Onboarding (Dark Theme)

*Jules updated these views to use the new deep blue `.auth-container` and `.auth-card` layouts.*

1. **Sign Up / Onboarding:** Navigate to the registration or onboarding page to create a new organization.
* **Visual Check:** Does the background have the new dark theme? Is the central card styled cleanly with rounded corners?


2. **Validation Errors:** Before typing anything, click the **"Submit"** or **"Continue"** button.
* **Visual Check:** Look for the new red `.field-error` text under the inputs and the generic `.error-banner` at the top. They should look uniform, not like default browser text.


3. **Login:** Log out and navigate to `/login`.
* **Visual Check:** Confirm the login screen matches the exact same layout and styling as the onboarding screen. Log back in.



### Step 2: CRM Pipeline (Light Theme & Spinners)

*This tests the standard light mode, primary buttons, and async loading states.*

1. **Navigate to the CRM Pipeline:**
* **Visual Check:** Look at the main action buttons (like "Add Deal"). Hover over them—they should have a smooth Indigo hover effect (`.btn-primary`).


2. **Open the Deal Draft Modal:** Click to create or draft a new CRM deal.
* **Visual Check:** Ensure the input fields (`.form-group`) have a clean focus ring (usually a blue/indigo outline) when you click inside them.


3. **Trigger the AI/Save Action:** Submit the form.
* **Visual Check:** Watch for the loading spinner. Jules specifically standardized the spinner CSS, so it should look clean and centered inside the button or modal while saving.



### Step 3: Workspace Settings & Billing (Forms & Modals)

*This verifies that the previous billing work seamlessly inherited the new Tailwind styles.*

1. **Navigate to Workspace/Organization Settings:**
* **Visual Check:** Check the layout of the settings forms. The spacing between labels and input fields should feel consistent and breathable.


2. **Trigger the PRO Upgrade Modal:** Click the "Upgrade to Pro" button.
* **Visual Check:** The modal should have the same unified form styling as the CRM and Onboarding modules.



### Step 4: LMS / Learner Module (General Consistency)

*To ensure the standardization reached all corners of the app.*

1. **Navigate to the Courses/LMS view:**
* **Visual Check:** Look at the course cards (`.card-container`). They should have standardized borders, padding, and subtle shadows.


2. **Secondary Buttons:** Find any secondary actions (like "Cancel", "Back", or "View Details").
* **Visual Check:** Ensure they use the new `.btn-secondary` styling (usually a gray or outline style) and don't clash with the primary Indigo buttons.



---

Walk through these steps, and if everything feels cohesive, buttons respond consistently, and forms don't look broken, you can give Jules the green light to merge Issue #104! Let me know how it goes.
