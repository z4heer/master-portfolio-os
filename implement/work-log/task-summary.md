Following the **Proposed Execution Framework**, we are pausing the **System Integration Testing (SIT)** to address the critical functional gap identified: the missing **"Add to Cart"** capability within the Product List.

As the **Principal Architect**, I have prepared the formal **Implementation Package** for this task to ensure it integrates seamlessly with your **Enterprise Design System**.

### **Role 1: Architecture Review & Design Decisions**

*   **Objective:** Integrate the "Add to Cart" trigger into the `ProductListComponent` cards, enabling the core customer journey from browsing to shopping.
*   **Service Integration:** The component will consume the `addToCart(product)` method from the already established and verified **Signal-based `CartService`**.
*   **UI Pattern:** The action button will be placed within the `actions` slot of the existing `AppCard` component to maintain visual consistency.
*   **State Management:** The "Add" action must trigger a reactive update to the cart signals, which in turn updates the global navigation counter in the `Toolbar`.
*   **User Feedback:** Use the `NotificationService` to trigger a **Material Snackbar** upon a successful add, replacing the legacy `console.log` approach.
*   **Inventory Logic:** The button must be reactively disabled if the product’s `stock_quantity` is 0, utilizing the same logic as the `StatusChip`.

### **Task Details**

*   **Folder Structure:** No changes required; work is within `src/app/features/product/`.
*   **Files to Modify:**
    *   `product-list.component.ts`: Add `CartService` and `NotificationService` injection and the `onAddToCart` handler.
    *   `product-list.component.html`: Update the `AppCard` template to include the action button.
*   **Files to Create:** None.

---

### **Role 2: AI Coding Assistant Prompt (Implementation Engineer)**

Copy and paste the following prompt into your **AI Coding Assistant** chat:

> **"Act as an Implementation Engineer.** We are closing a functional gap in the **Product List** feature of our Enterprise E-Commerce Platform using **Angular 19**.
>
> **Task:** Add the 'Add to Cart' functionality to the `ProductListComponent` while adhering to our **Enterprise Design System**.
>
> **1. Component Logic (product-list.component.ts):**
> * Inject the `CartService` and the `NotificationService`.
> * Implement a method `onAddToCart(product: Product)`. 
> * This method should call `this.cartService.addToCart(product)` and then display a success message using `this.notificationService.success('Added to cart')`.
>
> **2. Template Update (product-list.component.html):**
> * Within the existing `<app-card>` loop, add a Material button to the card's action area.
> * The button should have a `shopping_cart` icon and the label 'Add to Cart'.
> * **Business Rule:** Use a property binding to disable the button if `product.stock_quantity <= 0`.
>
> **Technical Requirements:**
> * Strictly follow **OnPush Change Detection** and **Angular Signals**.
> * Use established Material 3 styling and utility classes.
> * Ensure all new code passes the project's existing linting rules.
>
> **Deliverables:** Provide the updated `product-list.component.ts` and `product-list.component.html`. Include a unit test for the `ProductListComponent` that spies on the `CartService` to verify the add action is called."

---

### **Role 3: Next Action Items for You (QA)**

1.  **Run the Prompt:** Execute the prompt with your AI coding assistant.
2.  **Verify Locally:**
    *   Confirm that clicking "Add to Cart" updates the cart count in the header toolbar.
    *   Verify the **Snackbar** notification appears on success.
    *   Check that products with **0 stock** have a disabled "Add to Cart" button.
3.  **Return for Review:** Provide the code or the `git diff` here for my **Principal Architect Review** before we resume the **Sprint 4.4 SIT**.

Once this functional gap is closed, we will proceed to **Phase 3 (RBAC)** of the System Integration Testing.
