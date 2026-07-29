Here is the **Requirement Summary** and the **Implementation Guide** for the SQLAlchemy data models.

You can pass this directly to a junior engineer. It provides the exact file structure, relationship mappings, and clean code implementation ready for development.

---

## 📋 Requirement Summary

### Core Objective

Implement a production-ready SQLAlchemy (v2.0+) relational schema mapping seven core entities: **User**, **Product**, **Category**, **Order**, **OrderItem**, **Address**, and **Payment**.

### Entity Relationships & Business Rules

* **User**: Can have multiple Addresses and multiple Orders ($1 : N$).
* **Category**: Can have multiple Products ($1 : N$).
* **Product**: Belongs to a Category. Appears in multiple OrderItems ($1 : N$).
* **Order**: Belongs to a User. Has a shipping Address. Contains multiple OrderItems ($1 : N$). Has one Payment ($1 : 1$).
* **OrderItem**: Junction table linking Order and Product ($N : M$ via line items), storing quantity and locked unit price at purchase time.
* **Address**: Belongs to a User ($1 : N$).
* **Payment**: Belongs to a single Order ($1 : 1$).

---

## 📁 Recommended Project Structure

```text
app/
├── database.py         # Engine and Base setup
├── models/
│   ├── __init__.py     # Aggregates models & exports Base
│   ├── user.py
│   ├── address.py
│   ├── category.py
│   ├── product.py
│   ├── order.py
│   ├── order_item.py
│   └── payment.py
└── main.py             # App entry point (runs create_all)

```

---

## 💻 Code Implementation

### 1. `app/database.py`

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase

# Replace with your actual DB connection string
DATABASE_URL = "sqlite:///./app.db"

engine = create_engine(DATABASE_URL, echo=True)

class Base(DeclarativeBase):
    """Base class for all models."""
    pass

```

---

### 2. `app/models/user.py`

```python
from typing import List, TYPE_CHECKING
from sqlalchemy import String
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .address import Address
    from .order import Order

class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True, nullable=False)
    full_name: Mapped[str] = mapped_column(String(100), nullable=False)

    # Relationships
    addresses: Mapped[List["Address"]] = relationship(back_populates="user", cascade="all, delete-orphan")
    orders: Mapped[List["Order"]] = relationship(back_populates="user")

```

---

### 3. `app/models/address.py`

```python
from typing import TYPE_CHECKING
from sqlalchemy import String, ForeignKey
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .user import User

class Address(Base):
    __tablename__ = "addresses"

    id: Mapped[int] = mapped_column(primary_key=True)
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"), nullable=False)
    street: Mapped[str] = mapped_column(String(255), nullable=False)
    city: Mapped[str] = mapped_column(String(100), nullable=False)
    state: Mapped[str] = mapped_column(String(100), nullable=False)
    postal_code: Mapped[str] = mapped_column(String(20), nullable=False)
    country: Mapped[str] = mapped_column(String(100), nullable=False)

    # Relationships
    user: Mapped["User"] = relationship(back_populates="addresses")

```

---

### 4. `app/models/category.py`

```python
from typing import List, TYPE_CHECKING
from sqlalchemy import String
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .product import Product

class Category(Base):
    __tablename__ = "categories"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100), unique=True, nullable=False)

    # Relationships
    products: Mapped[List["Product"]] = relationship(back_populates="category")

```

---

### 5. `app/models/product.py`

```python
from typing import List, TYPE_CHECKING
from sqlalchemy import String, Numeric, ForeignKey
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .category import Category
    from .order_item import OrderItem

class Product(Base):
    __tablename__ = "products"

    id: Mapped[int] = mapped_column(primary_key=True)
    category_id: Mapped[int] = mapped_column(ForeignKey("categories.id"), nullable=False)
    name: Mapped[str] = mapped_column(String(200), nullable=False)
    price: Mapped[float] = mapped_column(Numeric(10, 2), nullable=False)

    # Relationships
    category: Mapped["Category"] = relationship(back_populates="products")
    order_items: Mapped[List["OrderItem"]] = relationship(back_populates="product")

```

---

### 6. `app/models/order.py`

```python
from typing import List, Optional, TYPE_CHECKING
from datetime import datetime
from sqlalchemy import ForeignKey, DateTime, func
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .user import User
    from .address import Address
    from .order_item import OrderItem
    from .payment import Payment

class Order(Base):
    __tablename__ = "orders"

    id: Mapped[int] = mapped_column(primary_key=True)
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"), nullable=False)
    shipping_address_id: Mapped[int] = mapped_column(ForeignKey("addresses.id"), nullable=False)
    created_at: Mapped[datetime] = mapped_column(DateTime, server_default=func.now())

    # Relationships
    user: Mapped["User"] = relationship(back_populates="orders")
    shipping_address: Mapped["Address"] = relationship()
    items: Mapped[List["OrderItem"]] = relationship(back_populates="order", cascade="all, delete-orphan")
    payment: Mapped[Optional["Payment"]] = relationship(back_populates="order", uselist=False)

```

---

### 7. `app/models/order_item.py`

```python
from typing import TYPE_CHECKING
from sqlalchemy import ForeignKey, Numeric, Integer
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .order import Order
    from .product import Product

class OrderItem(Base):
    __tablename__ = "order_items"

    id: Mapped[int] = mapped_column(primary_key=True)
    order_id: Mapped[int] = mapped_column(ForeignKey("orders.id"), nullable=False)
    product_id: Mapped[int] = mapped_column(ForeignKey("products.id"), nullable=False)
    quantity: Mapped[int] = mapped_column(Integer, default=1, nullable=False)
    unit_price: Mapped[float] = mapped_column(Numeric(10, 2), nullable=False)

    # Relationships
    order: Mapped["Order"] = relationship(back_populates="items")
    product: Mapped["Product"] = relationship(back_populates="order_items")

```

---

### 8. `app/models/payment.py`

```python
from typing import TYPE_CHECKING
from sqlalchemy import String, Numeric, ForeignKey
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

if TYPE_CHECKING:
    from .order import Order

class Payment(Base):
    __tablename__ = "payments"

    id: Mapped[int] = mapped_column(primary_key=True)
    order_id: Mapped[int] = mapped_column(ForeignKey("orders.id"), unique=True, nullable=False)
    amount: Mapped[float] = mapped_column(Numeric(10, 2), nullable=False)
    status: Mapped[str] = mapped_column(String(50), default="pending", nullable=False)

    # Relationships
    order: Mapped["Order"] = relationship(back_populates="payment")

```

---

### 9. `app/models/__init__.py` *(Central Registry)*

```python
from app.database import Base
from app.models.user import User
from app.models.address import Address
from app.models.category import Category
from app.models.product import Product
from app.models.order import Order
from app.models.order_item import OrderItem
from app.models.payment import Payment

# Exporting these ensures Base.metadata registers every model when models is imported
__all__ = [
    "Base",
    "User",
    "Address",
    "Category",
    "Product",
    "Order",
    "OrderItem",
    "Payment",
]

```

---

### 10. `app/main.py`

```python
from app.database import engine
from app.models import Base  # Importing models package executes __init__.py

def init_db():
    print("Creating tables in database...")
    Base.metadata.create_all(bind=engine)
    print("All tables created successfully!")

if __name__ == "__main__":
    init_db()

```

---

## 📌 Technical Notes for the Developer

1. **SQLAlchemy 2.0 Syntax**: This implementation uses `Mapped[...]` and `mapped_column(...)` which is the current standard for modern Python typing support.
2. **Circular Import Prevention**: `TYPE_CHECKING` guards from `typing` are used alongside string references in relationships (e.g., `Mapped["Order"]`) to prevent runtime circular dependency errors between model files.
3. **Price Precision**: `Numeric(10, 2)` is explicitly used for monetary fields (`price`, `unit_price`, `amount`) to prevent floating-point rounding errors in database queries.
