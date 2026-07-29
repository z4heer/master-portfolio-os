##
Awesome! Now that the environment and packages are resolved, we can set up **Integration Testing specifically for your Indian Payment Gateway (Razorpay) integration and Address flows**.

Because payment integration touches external APIs, testing in India requires handling two scenarios:

1. **Mocking External Razorpay Gateway API calls** (Fast execution, runs in CI/CD without hitting live endpoints or spending real money).
2. **Testing Webhook Signature Verification** (Ensuring HMAC security algorithms pass before updating database order statuses).

---

## 📁 Updated Test Directory Setup

Add tests to your `tests/` directory:

```text
tests/
├── conftest.py                # DB & client fixtures (from earlier)
├── test_address_api.py        # Address integration tests
└── test_payment_india_api.py  # Razorpay integration & webhook signature tests

```

---

## 🛠️ Step 1: Mocking Razorpay in `conftest.py`

Add a pytest fixture to `tests/conftest.py` so your test suite automatically mocks calls to Razorpay's API:

```python
# Add this fixture to tests/conftest.py
import pytest
from unittest.mock import MagicMock

@pytest.fixture
def mock_razorpay_client(mocker):
    """Mocks the Razorpay SDK client so tests don't make real network calls."""
    mock_client = MagicMock()
    
    # Mocking order.create response in Paise format
    mock_client.order.create.return_value = {
        "id": "order_rzp_test_12345",
        "entity": "order",
        "amount": 50000,  # ₹500 in paise
        "amount_paid": 0,
        "amount_due": 50000,
        "currency": "INR",
        "receipt": "rcpt_test",
        "status": "created"
    }
    return mock_client

```

---

## 🧪 Step 2: Payment & Webhook Tests (`test_payment_india_api.py`)

Create `tests/test_payment_india_api.py` to test the entire lifecycle, including **Paise conversion**, **order creation**, and **HMAC Webhook verification**:

```python
# tests/test_payment_india_api.py
import hmac
import hashlib
import uuid
import pytest
from fastapi import status

# Secret used to sign fake Razorpay webhooks in tests
TEST_WEBHOOK_SECRET = "your_webhook_secret_key"


def test_create_razorpay_payment_order(client, mocker):
    """Tests creating a payment record and ensuring amount is correctly handled."""
    # Mock Razorpay SDK creation
    mock_order_create = mocker.patch("razorpay.Client.order.create")
    mock_order_create.return_value = {"id": "order_rzp_mock_999"}

    order_id = str(uuid.uuid4())
    payload = {
        "order_id": order_id,
        "amount": 499.50,  # ₹499.50 INR
        "payment_method": "UPI",
        "currency": "INR"
    }

    response = client.post("/payments/", json=payload)
    assert response.status_code == status.HTTP_201_CREATED
    data = response.json()

    assert data["status"] == "PENDING"
    assert data["gateway_order_id"] == "order_rzp_mock_999"

    # Verify Razorpay was called with amount converted to PAISE (499.50 * 100 = 49950)
    mock_order_create.assert_called_once_with({
        "amount": 49950,
        "currency": "INR",
        "receipt": f"rcpt_{order_id[:8]}",
        "notes": {"order_id": order_id}
    })


def test_razorpay_webhook_signature_verification_success(client, db_session, mocker):
    """Tests receiving a successful payment.captured webhook with valid HMAC signature."""
    # 1. Setup existing payment in DB
    from app.models.payment import Payment  # Adjust path to your model
    from app.modules.orders.models.order import PaymentStatus

    order_id = uuid.uuid4()
    gateway_order_id = "order_rzp_webhook_test"
    
    payment = Payment(
        order_id=order_id,
        amount=1000.00,
        gateway_order_id=gateway_order_id,
        status=PaymentStatus.PENDING
    )
    db_session.add(payment)
    db_session.commit()

    # 2. Build Webhook Payload
    webhook_body = {
        "event": "payment.captured",
        "payload": {
            "payment": {
                "entity": {
                    "id": "pay_test_887766",
                    "order_id": gateway_order_id,
                    "amount": 100000,
                    "status": "captured"
                }
            }
        }
    }
    
    import json
    raw_body = json.dumps(webhook_body).encode("utf-8")

    # 3. Generate valid HMAC-SHA256 signature
    signature = hmac.new(
        key=TEST_WEBHOOK_SECRET.encode("utf-8"),
        msg=raw_body,
        digestmod=hashlib.sha256
    ).hexdigest()

    # 4. Fire POST to Webhook Endpoint
    headers = {"X-Razorpay-Signature": signature, "Content-Type": "application/json"}
    response = client.post("/payments/webhook/razorpay", content=raw_body, headers=headers)
    
    assert response.status_code == status.HTTP_200_OK

    # 5. Verify database status changed to PAID
    db_session.refresh(payment)
    assert payment.status == PaymentStatus.PAID
    assert payment.payment_reference == "pay_test_887766"


def test_razorpay_webhook_invalid_signature_rejected(client):
    """Tests that webhooks with fake/tampered signatures return HTTP 400."""
    raw_body = b'{"event": "payment.captured"}'
    fake_headers = {"X-Razorpay-Signature": "invalid_signature_hash"}

    response = client.post("/payments/webhook/razorpay", content=raw_body, headers=fake_headers)
    assert response.status_code == status.HTTP_400_BAD_REQUEST

```

---

## ⚡ Step 3: Run the Test Suite

Install `pytest-mock` to make fixture mocking easy:

```bash
pip install pytest pytest-mock httpx

```

Then execute all tests:

```bash
pytest -v

```

### What to check:

* All 200/201 creation endpoints succeed.
* DB `payment.status` switches to `PAID` only when a valid signature is provided.
* `amount_in_paise` multiplication logic converts Rupees accurately.
##
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
