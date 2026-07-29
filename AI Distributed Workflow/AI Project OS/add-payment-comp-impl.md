Here is the complete implementation for the **Payment** and **Address** components following a standard **4-layer FastAPI architecture** (*Schemas → Repositories → Services → Routers*).

---

## 1. `schemas/` Layer (Pydantic v2)

### `app/schemas/address.py`

```python
import uuid
from typing import Optional
from pydantic import BaseModel, ConfigDict

class AddressBase(BaseModel):
    street: str
    city: str
    state: str
    postal_code: str
    country: str

class AddressCreate(AddressBase):
    user_id: uuid.UUID

class AddressUpdate(BaseModel):
    street: Optional[str] = None
    city: Optional[str] = None
    state: Optional[str] = None
    postal_code: Optional[str] = None
    country: Optional[str] = None

class AddressResponse(AddressBase):
    id: uuid.UUID
    user_id: uuid.UUID

    model_config = ConfigDict(from_attributes=True)

```

### `app/schemas/payment.py`

```python
import uuid
from datetime import datetime
from decimal import Decimal
from typing import Optional
from pydantic import BaseModel, ConfigDict
from app.modules.orders.models.order import PaymentMethod, PaymentStatus, Currency

class PaymentBase(BaseModel):
    order_id: uuid.UUID
    amount: Decimal
    payment_method: PaymentMethod
    currency: Currency = Currency.INR

class PaymentCreate(PaymentBase):
    pass

class PaymentUpdate(BaseModel):
    status: Optional[PaymentStatus] = None
    payment_reference: Optional[str] = None

class PaymentResponse(PaymentBase):
    id: uuid.UUID
    status: PaymentStatus
    payment_reference: Optional[str] = None
    payment_date: Optional[datetime] = None

    model_config = ConfigDict(from_attributes=True)

```

---

## 2. `repositories/` Layer (Data Access)

### `app/repositories/address_repository.py`

```python
import uuid
from typing import Sequence, Optional
from sqlalchemy.orm import Session
from app.models.address import Address  # Adjust import based on exact path
from app.schemas.address import AddressCreate, AddressUpdate

class AddressRepository:
    def __init__(self, db: Session):
        self.db = db

    def get_by_id(self, address_id: uuid.UUID) -> Optional[Address]:
        return self.db.query(Address).filter(Address.id == address_id).first()

    def get_by_user_id(self, user_id: uuid.UUID) -> Sequence[Address]:
        return self.db.query(Address).filter(Address.user_id == user_id).all()

    def create(self, schema: AddressCreate) -> Address:
        address = Address(**schema.model_dump())
        self.db.add(address)
        self.db.commit()
        self.db.refresh(address)
        return address

    def update(self, address: Address, schema: AddressUpdate) -> Address:
        update_data = schema.model_dump(exclude_unset=True)
        for key, value in update_data.items():
            setattr(address, key, value)
        self.db.commit()
        self.db.refresh(address)
        return address

    def delete(self, address: Address) -> None:
        self.db.delete(address)
        self.db.commit()

```

### `app/repositories/payment_repository.py`

```python
import uuid
from typing import Optional
from sqlalchemy.orm import Session
from app.models.payment import Payment  # Adjust import based on exact path
from app.schemas.payment import PaymentCreate, PaymentUpdate

class PaymentRepository:
    def __init__(self, db: Session):
        self.db = db

    def get_by_id(self, payment_id: uuid.UUID) -> Optional[Payment]:
        return self.db.query(Payment).filter(Payment.id == payment_id).first()

    def get_by_order_id(self, order_id: uuid.UUID) -> Optional[Payment]:
        return self.db.query(Payment).filter(Payment.order_id == order_id).first()

    def create(self, schema: PaymentCreate) -> Payment:
        payment = Payment(**schema.model_dump())
        self.db.add(payment)
        self.db.commit()
        self.db.refresh(payment)
        return payment

    def update(self, payment: Payment, schema: PaymentUpdate) -> Payment:
        update_data = schema.model_dump(exclude_unset=True)
        for key, value in update_data.items():
            setattr(payment, key, value)
        self.db.commit()
        self.db.refresh(payment)
        return payment

```

---

## 3. `services/` Layer (Business Logic)

### `app/services/address_service.py`

```python
import uuid
from typing import Sequence
from fastapi import HTTPException, status
from app.repositories.address_repository import AddressRepository
from app.schemas.address import AddressCreate, AddressUpdate, AddressResponse

class AddressService:
    def __init__(self, repo: AddressRepository):
        self.repo = repo

    def create_address(self, schema: AddressCreate) -> AddressResponse:
        return AddressResponse.model_validate(self.repo.create(schema))

    def get_address(self, address_id: uuid.UUID) -> AddressResponse:
        address = self.repo.get_by_id(address_id)
        if not address:
            raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Address not found")
        return AddressResponse.model_validate(address)

    def get_user_addresses(self, user_id: uuid.UUID) -> Sequence[AddressResponse]:
        addresses = self.repo.get_by_user_id(user_id)
        return [AddressResponse.model_validate(a) for a in addresses]

    def update_address(self, address_id: uuid.UUID, schema: AddressUpdate) -> AddressResponse:
        address = self.repo.get_by_id(address_id)
        if not address:
            raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Address not found")
        updated = self.repo.update(address, schema)
        return AddressResponse.model_validate(updated)

    def delete_address(self, address_id: uuid.UUID) -> None:
        address = self.repo.get_by_id(address_id)
        if not address:
            raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Address not found")
        self.repo.delete(address)

```

### `app/services/payment_service.py`

```python
import uuid
from datetime import datetime, timezone
from fastapi import HTTPException, status
from app.repositories.payment_repository import PaymentRepository
from app.schemas.payment import PaymentCreate, PaymentUpdate, PaymentResponse
from app.modules.orders.models.order import PaymentStatus

class PaymentService:
    def __init__(self, repo: PaymentRepository):
        self.repo = repo

    def process_payment(self, schema: PaymentCreate) -> PaymentResponse:
        existing = self.repo.get_by_order_id(schema.order_id)
        if existing:
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST, 
                detail="Payment record already exists for this order"
            )
        payment = self.repo.create(schema)
        return PaymentResponse.model_validate(payment)

    def get_payment_by_order(self, order_id: uuid.UUID) -> PaymentResponse:
        payment = self.repo.get_by_order_id(order_id)
        if not payment:
            raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Payment record not found")
        return PaymentResponse.model_validate(payment)

    def update_payment_status(self, payment_id: uuid.UUID, schema: PaymentUpdate) -> PaymentResponse:
        payment = self.repo.get_by_id(payment_id)
        if not payment:
            raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Payment record not found")
        
        if schema.status == PaymentStatus.PAID and not payment.payment_date:
            payment.payment_date = datetime.now(timezone.utc)

        updated = self.repo.update(payment, schema)
        return PaymentResponse.model_validate(updated)

```

---

## 4. `routers/` Layer (API Endpoints)

### `app/routers/address_router.py`

```python
import uuid
from typing import List
from fastapi import APIRouter, Depends, status
from sqlalchemy.orm import Session

from app.database.session import get_db  # Adjust to match DB session dependency
from app.repositories.address_repository import AddressRepository
from app.services.address_service import AddressService
from app.schemas.address import AddressCreate, AddressUpdate, AddressResponse

router = APIRouter(prefix="/addresses", tags=["Addresses"])

def get_address_service(db: Session = Depends(get_db)) -> AddressService:
    return AddressService(AddressRepository(db))

@router.post("/", response_model=AddressResponse, status_code=status.HTTP_201_CREATED)
def create_address(
    schema: AddressCreate, 
    service: AddressService = Depends(get_address_service)
):
    return service.create_address(schema)

@router.get("/user/{user_id}", response_model=List[AddressResponse])
def get_user_addresses(
    user_id: uuid.UUID, 
    service: AddressService = Depends(get_address_service)
):
    return service.get_user_addresses(user_id)

@router.get("/{address_id}", response_model=AddressResponse)
def get_address(
    address_id: uuid.UUID, 
    service: AddressService = Depends(get_address_service)
):
    return service.get_address(address_id)

@router.patch("/{address_id}", response_model=AddressResponse)
def update_address(
    address_id: uuid.UUID, 
    schema: AddressUpdate, 
    service: AddressService = Depends(get_address_service)
):
    return service.update_address(address_id, schema)

@router.delete("/{address_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_address(
    address_id: uuid.UUID, 
    service: AddressService = Depends(get_address_service)
):
    service.delete_address(address_id)

```

### `app/routers/payment_router.py`

```python
import uuid
from fastapi import APIRouter, Depends, status
from sqlalchemy.orm import Session

from app.database.session import get_db  # Adjust to match DB session dependency
from app.repositories.payment_repository import PaymentRepository
from app.services.payment_service import PaymentService
from app.schemas.payment import PaymentCreate, PaymentUpdate, PaymentResponse

router = APIRouter(prefix="/payments", tags=["Payments"])

def get_payment_service(db: Session = Depends(get_db)) -> PaymentService:
    return PaymentService(PaymentRepository(db))

@router.post("/", response_model=PaymentResponse, status_code=status.HTTP_201_CREATED)
def process_payment(
    schema: PaymentCreate, 
    service: PaymentService = Depends(get_payment_service)
):
    return service.process_payment(schema)

@router.get("/order/{order_id}", response_model=PaymentResponse)
def get_payment_by_order(
    order_id: uuid.UUID, 
    service: PaymentService = Depends(get_payment_service)
):
    return service.get_payment_by_order(order_id)

@router.patch("/{payment_id}", response_model=PaymentResponse)
def update_payment_status(
    payment_id: uuid.UUID, 
    schema: PaymentUpdate, 
    service: PaymentService = Depends(get_payment_service)
):
    return service.update_payment_status(payment_id, schema)

```

---

### Architectural Highlights for Junior Developers

1. **Separation of Concerns**: Routers handle HTTP logic $\rightarrow$ Services handle business decisions $\rightarrow$ Repositories handle database execution.
2. **Pydantic v2 `model_config = ConfigDict(from_attributes=True)**`: Replaces the old `orm_mode = True` syntax to seamlessly convert SQLAlchemy objects into Pydantic responses.
3. **Dependency Injection**: Simple dependency functions (`get_address_service`, `get_payment_service`) assemble the database session and classes automatically for route functions.
