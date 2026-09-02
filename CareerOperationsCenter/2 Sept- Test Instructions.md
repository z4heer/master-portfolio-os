1. Verification of the Unified Auth & Onboarding UI (Dark Theme)
The login, invite-accept, and public onboarding screens should now share the exact same modern, dark-themed background and structural layout.

Login Screen: Navigate to /login. Verify the deep blue gradient background and glowing orbs are present. Check that the "Email" and "Password" form fields are styled correctly (dark background, white text, indigo focus ring). Leave fields empty and try to proceed to verify the red validation errors (.field-error) appear clearly.
Tenant Onboarding (Public): Navigate to the public onboarding URL (usually /onboard?code=YOUR_INVITE_CODE). Verify the layout matches the login screen. Fill out the organization details. Verify the password strength meter works and form validation looks correct. Upon successful completion, confirm that you are redirected to the billing dashboard.
2. Verification of the Internal Tenant Manager (Light Theme & Logic)
This verifies the internal admin tool uses the clean form layout, auto-generates the slug, and properly creates both users.

Navigate to Tenant Manager: Login as a Super Admin and navigate to the Tenant Manager (Admin) view.
Verify UI Consistency: Check that the form uses the standardized inputs (light theme: white background, dark text, gray borders) and that there are no duplicate input fields (you should only see Organization Name, Workspace Slug, Admin Email, Admin Password, and Admin Full Name).
Test Auto-Slug Generation:
Type Acme Corporation into the "Organization Name" field.
Expected behavior: The "Workspace Slug" field should automatically populate with acme-corporation.
Test Manual Slug Override:
Manually edit the "Workspace Slug" field to be acme-test.
Change the "Organization Name" to Acme Corporation Updated.
Expected behavior: The slug should not change from acme-test (it remembers that you manually overrode it).
Test Form Completion: Fill out the rest of the form and click "Create Workspace". Verify the button shows a "Creating..." state. Upon success, verify the success alert banner appears, and the form fields are cleared.
3. Verification of Global Buttons & Spinners
This ensures our UI standardizations didn't inadvertently break styling in other areas of the application.

CRM Pipeline / General Modals: Navigate to the CRM module. Open a modal (e.g., "New Deal").
Check Buttons: Verify the "Create Deal" primary button and any secondary/cancel buttons look correct (proper padding, readable text, clear hover states) and are not invisible against the white modal background.
Check Spinners: Trigger an action that requires a loading state (like drafting an email for a deal) and verify the loading spinner is visible (indigo color on light backgrounds).
4. API Routing Verification (Backend Check)
When sending the Public Tenant Onboarding form, inspect the network tab in your browser's developer tools. Verify the request is made to POST /api/v1/auth/onboard.
When sending the Admin Tenant Manager form, inspect the network tab. Verify the request is made to POST /api/v1/tenants/onboard and that the payload contains the duplicated fields (name and org_name, email and admin_email, etc.).
