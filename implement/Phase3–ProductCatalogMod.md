# Phase 3 – Product Catalog Module (Angular 19)

Since **Phase 2 Authentication is completed and verified** (Register, Login, Logout, JWT Storage, Guards, Interceptor), we will now build the **Product Catalog Module** using enterprise Angular architecture.

---

# Assumptions

Backend APIs already working:

```http
GET    /products
GET    /products/{id}
POST   /products
PUT    /products/{id}
DELETE /products/{id}
```

Configured Base URL:

```typescript
http://localhost:8000/api/v1
```

Authentication Interceptor already attaches:

```http
Authorization: Bearer <token>
```

---

# Angular CLI Commands

Generate Product Module Components

```bash
ng g c features/products/product-list --standalone
ng g c features/products/product-detail --standalone
ng g c features/products/product-search --standalone

ng g s core/services/product
```

---

# Folder Structure

```text
src/
│
├── app/
│
├── core/
│   ├── services/
│   │   └── product.service.ts
│   │
│   └── models/
│       └── product.model.ts
│
├── features/
│   └── products/
│
│       ├── product-list/
│       │   ├── product-list.component.ts
│       │   ├── product-list.component.html
│       │   └── product-list.component.scss
│       │
│       ├── product-detail/
│       │   ├── product-detail.component.ts
│       │   ├── product-detail.component.html
│       │   └── product-detail.component.scss
│       │
│       └── product-search/
│           ├── product-search.component.ts
│           ├── product-search.component.html
│           └── product-search.component.scss
│
└── app.routes.ts
```

---

# 1. Product Model

## File

```text
src/app/core/models/product.model.ts
```

```typescript
export interface Product {
  id: string;
  name: string;
  description: string;
  category: string;
  price: number;
  stock_quantity: number;
  image_url?: string;
  created_at?: string;
}
```

---

# 2. Product Service

## File

```text
src/app/core/services/product.service.ts
```

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, Observable, tap } from 'rxjs';

import { environment } from '../../../environments/environment';
import { Product } from '../models/product.model';

@Injectable({
  providedIn: 'root'
})
export class ProductService {

  private productsSubject =
    new BehaviorSubject<Product[]>([]);

  products$ =
    this.productsSubject.asObservable();

  constructor(
    private http: HttpClient
  ) {}

  getProducts(): Observable<Product[]> {

    return this.http.get<Product[]>(
      `${environment.apiUrl}/products`
    ).pipe(
      tap(products =>
        this.productsSubject.next(products)
      )
    );
  }

  getProductById(
    id: string
  ): Observable<Product> {

    return this.http.get<Product>(
      `${environment.apiUrl}/products/${id}`
    );
  }

  createProduct(
    product: Product
  ): Observable<Product> {

    return this.http.post<Product>(
      `${environment.apiUrl}/products`,
      product
    );
  }

  updateProduct(
    id: string,
    product: Product
  ): Observable<Product> {

    return this.http.put<Product>(
      `${environment.apiUrl}/products/${id}`,
      product
    );
  }

  deleteProduct(
    id: string
  ): Observable<void> {

    return this.http.delete<void>(
      `${environment.apiUrl}/products/${id}`
    );
  }

  filterProducts(
    searchTerm: string
  ): void {

    const products =
      this.productsSubject.value;

    const filtered =
      products.filter(product =>
        product.name
          .toLowerCase()
          .includes(searchTerm.toLowerCase())
      );

    this.productsSubject.next(filtered);
  }

  filterByCategory(
    category: string
  ): void {

    const products =
      this.productsSubject.value;

    if (!category) {
      this.getProducts().subscribe();
      return;
    }

    const filtered =
      products.filter(
        p => p.category === category
      );

    this.productsSubject.next(filtered);
  }
}
```

---

# Observable Usage

### Why Observable?

Angular HTTP returns Observables.

Benefits:

```text
Lazy Execution
Cancellation
Reactive Updates
Error Handling
Stream Processing
```

Example:

```typescript
this.productService
    .getProducts()
    .subscribe({
      next: products => {
         this.products = products;
      },
      error: err => {
         console.error(err);
      }
    });
```

---

# BehaviorSubject Usage

### Why BehaviorSubject?

Keeps latest state in memory.

```typescript
private productsSubject =
new BehaviorSubject<Product[]>([]);
```

Benefits:

```text
Shared State
Reactive UI Updates
Current Value Available
Simple Alternative to NgRx
```

Component subscribes:

```typescript
this.productService.products$
.subscribe(products => {
   this.products = products;
});
```

---

# 3. Product Search Component

## File

```text
src/app/features/products/product-search/product-search.component.ts
```

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';

import { ProductService } from '../../../core/services/product.service';

@Component({
  selector: 'app-product-search',
  standalone: true,
  imports: [
    CommonModule,
    FormsModule,
    MatFormFieldModule,
    MatInputModule
  ],
  templateUrl: './product-search.component.html'
})
export class ProductSearchComponent {

  searchText = '';

  constructor(
    private productService: ProductService
  ) {}

  search(): void {
    this.productService
      .filterProducts(this.searchText);
  }
}
```

---

## HTML

```text
src/app/features/products/product-search/product-search.component.html
```

```html
<mat-form-field appearance="outline">

  <mat-label>
    Search Products
  </mat-label>

  <input
    matInput
    [(ngModel)]="searchText"
    (keyup)="search()">

</mat-form-field>
```

---

# 4. Product List Component

## File

```text
src/app/features/products/product-list/product-list.component.ts
```

```typescript
import {
  Component,
  OnInit
} from '@angular/core';

import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';

import { MatCardModule } from '@angular/material/card';
import { MatGridListModule } from '@angular/material/grid-list';
import { MatButtonModule } from '@angular/material/button';

import { Observable } from 'rxjs';

import { Product } from '../../../core/models/product.model';
import { ProductService } from '../../../core/services/product.service';
import { ProductSearchComponent } from '../product-search/product-search.component';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [
    CommonModule,
    RouterModule,
    MatCardModule,
    MatGridListModule,
    MatButtonModule,
    ProductSearchComponent
  ],
  templateUrl: './product-list.component.html'
})
export class ProductListComponent implements OnInit {

  products$!: Observable<Product[]>;

  constructor(
    private productService: ProductService
  ) {}

  ngOnInit(): void {

    this.productService
      .getProducts()
      .subscribe();

    this.products$ =
      this.productService.products$;
  }
}
```

---

## HTML

```text
src/app/features/products/product-list/product-list.component.html
```

```html
<app-product-search />

<div class="grid-container">

  @for (
    product of products$ | async;
    track product.id
  ) {

  <mat-card>

    <mat-card-title>
      {{ product.name }}
    </mat-card-title>

    <mat-card-content>

      <p>{{ product.category }}</p>

      <p>
        ₹ {{ product.price }}
      </p>

    </mat-card-content>

    <button
      mat-raised-button
      color="primary"
      [routerLink]="['/products', product.id]">

      View Details

    </button>

  </mat-card>

  }

</div>
```

---

## SCSS

```scss
.grid-container {
  display: grid;
  gap: 16px;

  grid-template-columns:
    repeat(auto-fit, minmax(250px, 1fr));
}
```

---

# 5. Product Detail Component

## File

```text
src/app/features/products/product-detail/product-detail.component.ts
```

```typescript
import {
  Component,
  OnInit
} from '@angular/core';

import { ActivatedRoute } from '@angular/router';
import { CommonModule } from '@angular/common';

import { MatCardModule } from '@angular/material/card';

import { Product } from '../../../core/models/product.model';
import { ProductService } from '../../../core/services/product.service';

@Component({
  selector: 'app-product-detail',
  standalone: true,
  imports: [
    CommonModule,
    MatCardModule
  ],
  templateUrl:
    './product-detail.component.html'
})
export class ProductDetailComponent
  implements OnInit {

  product?: Product;

  constructor(
    private route: ActivatedRoute,
    private productService: ProductService
  ) {}

  ngOnInit(): void {

    const id =
      this.route.snapshot.paramMap.get('id');

    if (id) {

      this.productService
        .getProductById(id)
        .subscribe(product => {
          this.product = product;
        });
    }
  }
}
```

---

## HTML

```text
src/app/features/products/product-detail/product-detail.component.html
```

```html
@if(product){

<mat-card>

  <mat-card-title>
    {{ product.name }}
  </mat-card-title>

  <mat-card-content>

    <p>{{ product.description }}</p>

    <p>
      Category:
      {{ product.category }}
    </p>

    <p>
      Price:
      ₹ {{ product.price }}
    </p>

    <p>
      Stock:
      {{ product.stock_quantity }}
    </p>

  </mat-card-content>

</mat-card>

}
```

---

# 6. Category Filter Enhancement

### Recommended Component

```text
product-category-filter.component
```

Angular Material:

```typescript
MatSelectModule
```

Example:

```html
<mat-select
(selectionChange)="onCategoryChange($event.value)">
```

Categories:

```typescript
Electronics
Books
Clothing
Sports
Home
```

Can be loaded dynamically later from API.

---

# 7. Routing Updates

## File

```text
src/app/app.routes.ts
```

Add:

```typescript
{
  path: 'products',
  component: ProductListComponent,
  canActivate: [AuthGuard]
},

{
  path: 'products/:id',
  component: ProductDetailComponent,
  canActivate: [AuthGuard]
}
```

---

# Required Angular Material Modules

Install if not already available:

```bash
ng add @angular/material
```

Imports used:

```typescript
MatCardModule
MatButtonModule
MatGridListModule
MatInputModule
MatFormFieldModule
MatSelectModule
```

---

# Environment Verification

## File

```text
src/environments/environment.ts
```

```typescript
export const environment = {
  production: false,
  apiUrl: 'http://localhost:8000/api/v1'
};
```

---

# Junior Developer Checklist

### Must Verify

* [ ] Login successful
* [ ] JWT stored in localStorage
* [ ] Interceptor sending token
* [ ] GET /products returns data
* [ ] Product list renders
* [ ] Search works
* [ ] Product details page loads
* [ ] Route guard blocks anonymous users
* [ ] Responsive layout works on mobile/tablet
* [ ] Error handling displays API errors

### Expected Outcome

After implementation:

```text
Login
   ↓
Products Page
   ↓
Search Products
   ↓
Filter Products
   ↓
View Product Details
   ↓
Ready for Orders Module (Phase 4)
```

**Stop here and validate Product Catalog end-to-end before generating Phase 4 (Orders).**
