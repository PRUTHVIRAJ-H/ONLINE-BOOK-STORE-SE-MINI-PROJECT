# 📚 Online Bookstore System

### Buying & Selling Platform

A full-stack web-based **Online Bookstore System** that allows users to search, purchase, and review new or pre-owned books. The system also provides seller functionality for listing books and administrator functionality for managing books, inventory, orders, reviews, and sales information.

---

## 📌 Project Overview

The Online Bookstore System is designed as a full-stack e-commerce application for buying and selling books.

The system supports three main types of users:

* 👤 **Customer / Buyer**
* 🏪 **Seller**
* 🛠️ **Administrator**

Customers can create accounts, search for books, add books to their cart or wishlist, place orders, make payments, track orders, and submit reviews.

Sellers can submit book listings by providing information such as title, author, ISBN, condition, price, stock and cover images.

Administrators can manage the catalog, approve seller listings, manage inventory and orders, moderate reviews, and view sales information.

The system follows a **React + Django REST Framework + PostgreSQL** architecture.

---

# 🎯 Objectives

The main objectives of this project are:

* To provide an easy-to-use online platform for buying books.
* To support both new and pre-owned books.
* To allow users to list books for selling.
* To provide search and filtering facilities.
* To provide a persistent shopping cart and wishlist.
* To provide secure online payment through external payment gateways.
* To provide order tracking and order history.
* To allow administrators to manage books and inventory.
* To provide review and rating functionality.
* To maintain secure authentication and role-based access.
* To maintain reliable and consistent order and inventory data.

---

# ✨ Main Features

## 👤 User Authentication

* User registration
* Email activation
* Login and logout
* JWT-based authentication
* Password recovery
* Profile management
* Address management
* Role-based access control

---

## 🔎 Book Discovery

Users can search books using:

* Title
* Author
* ISBN
* Genre

Books can also be filtered using:

* Price
* Condition
* Rating

Sorting is supported using:

* Price
* Popularity

The SRS specifies a target search response time of **2 seconds for 10,000 book records**.

---

## 📖 Book Details

Each book can display:

* Book cover
* Title
* Author
* Description
* Publisher
* ISBN
* Price
* Condition
* Available stock
* Seller information
* Reviews

---

## 🛒 Shopping Cart

Users can:

* Add books to cart
* Change quantity
* Remove books
* View subtotal
* View taxes
* View shipping charges
* View final order amount

Cart information is maintained for authenticated users.

---

## ❤️ Wishlist

Users can:

* Add books to wishlist
* View saved books
* Keep wishlist items across login/logout
* Move wishlist items to the shopping cart

---

# 🏪 Seller Features

Sellers can submit books for listing.

A seller listing contains:

* Title
* Author
* ISBN
* Genre
* Description
* Condition
* Price
* Stock quantity
* Cover image

### Supported Book Conditions

* New
* Like New
* Good
* Acceptable

New seller listings initially remain in **Pending Approval** state until reviewed by an administrator.

---

# 💳 Checkout and Payment

The checkout process includes:

1. Cart verification
2. Address selection
3. Order summary
4. Tax calculation
5. Shipping calculation
6. Payment
7. Payment confirmation
8. Order creation
9. Cart clearing
10. Confirmation email

The application uses an external payment gateway such as **Stripe or Razorpay**.

Raw card information such as PAN and CVV is not stored by the application. Payment processing uses gateway tokenization.

---

# 📦 Order Management

Users can view their orders and track their current status.

### Order States

```text
Placed
   ↓
Processing
   ↓
Shipped
   ↓
Delivered
```

An order can also move to:

```text
Cancelled
```

The SRS defines the order states as **Placed, Processing, Shipped, Delivered and Cancelled**.

---

# ⭐ Reviews and Ratings

Verified buyers can submit ratings and reviews.

A review is allowed only when:

```text
Order exists
      ↓
Order belongs to user
      ↓
Order is Delivered
      ↓
User can submit review
```

Administrators can moderate or remove reviews.

---

# 🛠️ Admin Features

Administrators can:

* Add books
* Edit books
* Deactivate books
* Delete books
* Approve seller listings
* Manage stock
* Set low-stock thresholds
* Update order status
* Moderate reviews
* View sales information

The SRS specifies that inventory should be flagged as low when stock falls below **5 units**.

---

# 🏗️ System Architecture

The application follows a three-layer full-stack architecture.

```mermaid
flowchart TB

    U[User / Browser]

    FE[React Frontend<br/>Single Page Application]

    API[Django REST API]

    AUTH[Authentication<br/>JWT + RBAC]

    DB[(PostgreSQL Database)]

    PAY[Payment Gateway<br/>Stripe / Razorpay]

    EMAIL[Transactional Email<br/>SMTP / SendGrid]

    U --> FE
    FE -->|HTTPS / JSON REST API| API

    API --> AUTH
    API --> DB

    API -->|Payment Request| PAY
    PAY -->|Webhook Callback| API

    API -->|Order Confirmation| EMAIL
```

The SRS defines React as the frontend, Django REST Framework as the backend, PostgreSQL as the database, and external payment/email services as integrations.

---

# 🔄 Overall System Flow

```mermaid
flowchart TD

    A[Open Website] --> B{Existing User?}

    B -->|No| C[Register]
    C --> D[Email Activation]
    D --> E[Login]

    B -->|Yes| E[Login]

    E --> F[Browse / Search Books]

    F --> G[View Book Details]

    G --> H{User Action}

    H -->|Wishlist| I[Add to Wishlist]
    H -->|Buy| J[Add to Cart]

    I --> F

    J --> K[View Cart]
    K --> L[Checkout]

    L --> M[Select Address]
    M --> N[Calculate Order Total]
    N --> O[Payment Gateway]

    O --> P{Payment Successful?}

    P -->|No| Q[Show Payment Error]
    Q --> K

    P -->|Yes| R[Create Order]
    R --> S[Update Inventory]
    S --> T[Clear Cart]
    T --> U[Send Confirmation Email]
    U --> V[Track Order]

    V --> W[Delivered]
    W --> X[Submit Review]
```

---

# 🔐 Authentication Flow

```mermaid
sequenceDiagram

    actor User
    participant React as React Frontend
    participant API as Django REST API
    participant DB as PostgreSQL

    User->>React: Enter email and password
    React->>API: Login request
    API->>DB: Validate credentials
    DB-->>API: User details

    alt Valid credentials
        API-->>React: JWT Access + Refresh Token
        React-->>User: Login successful
    else Invalid credentials
        API-->>React: HTTP 401
        React-->>User: Invalid credentials
    end
```

The SRS requires signed JWT access and refresh tokens and specifies HTTP 401 for invalid login credentials.

---

# 🛒 Checkout Flow

```mermaid
sequenceDiagram

    actor Buyer
    participant UI as React Frontend
    participant API as Django REST
    participant DB as PostgreSQL
    participant PG as Payment Gateway
    participant Email as Email Service

    Buyer->>UI: Proceed to Checkout
    UI->>API: Request checkout
    API->>DB: Validate cart and stock
    DB-->>API: Cart details

    API-->>UI: Order summary
    Buyer->>UI: Confirm payment

    UI->>PG: Payment request
    PG-->>API: Signed payment callback

    alt Payment Successful
        API->>DB: Create order
        API->>DB: Decrease inventory
        API->>DB: Clear cart
        API->>Email: Send receipt
        API-->>UI: Payment successful
        UI-->>Buyer: Order confirmed
    else Payment Failed
        PG-->>UI: Payment failure
        UI-->>Buyer: Show payment error
    end
```

---

# 👥 User Roles

```mermaid
flowchart LR

    CUSTOMER[Customer / Buyer]
    SELLER[Seller]
    ADMIN[Administrator]

    CUSTOMER --> C1[Browse Books]
    CUSTOMER --> C2[Search / Filter]
    CUSTOMER --> C3[Cart]
    CUSTOMER --> C4[Wishlist]
    CUSTOMER --> C5[Checkout]
    CUSTOMER --> C6[Track Orders]
    CUSTOMER --> C7[Reviews]

    SELLER --> S1[Submit Book Listing]
    SELLER --> S2[View Seller Listings]
    SELLER --> S3[Manage Own Listings]

    ADMIN --> A1[Manage Catalog]
    ADMIN --> A2[Approve Listings]
    ADMIN --> A3[Manage Inventory]
    ADMIN --> A4[Manage Orders]
    ADMIN --> A5[Moderate Reviews]
    ADMIN --> A6[View Sales Dashboard]
```

Server-side role-based access control is required so that users cannot access APIs belonging to roles they are not authorized to use.

---

# 🗄️ Database Concept

The system uses **PostgreSQL** as its relational database.

A simplified database relationship can be represented as:

```mermaid
erDiagram

    USER ||--o{ ADDRESS : has
    USER ||--o{ ORDER : places
    USER ||--o{ WISHLIST : creates
    USER ||--o{ REVIEW : writes
    USER ||--o{ BOOK : lists

    BOOK ||--o{ CART_ITEM : contains
    BOOK ||--o{ WISHLIST : saved
    BOOK ||--o{ REVIEW : receives
    BOOK ||--o{ ORDER_ITEM : included_in

    CART ||--o{ CART_ITEM : contains

    ORDER ||--|{ ORDER_ITEM : contains
    ORDER ||--|| PAYMENT : has
    ORDER ||--|| ADDRESS : uses

    USER {
        int id
        string name
        string email
        string password_hash
        string role
    }

    BOOK {
        int id
        string title
        string author
        string isbn
        string genre
        decimal price
        string condition
        int stock
    }

    ORDER {
        int id
        int user_id
        decimal total
        string status
        datetime created_at
    }

    PAYMENT {
        int id
        int order_id
        string transaction_id
        string status
    }

    REVIEW {
        int id
        int user_id
        int book_id
        int rating
        string review_text
    }
```

**Note:** This diagram is a conceptual representation of the database entities. The exact final database schema should follow the implementation.

---

# 🔌 API Architecture

The frontend communicates with the Django backend through REST APIs.

```mermaid
flowchart LR

    FRONTEND[React SPA]

    AUTH_API[/Authentication APIs/]
    CATALOG_API[/Catalog APIs/]
    CART_API[/Cart & Wishlist APIs/]
    SELLER_API[/Seller APIs/]
    ORDER_API[/Order & Payment APIs/]
    ADMIN_API[/Admin APIs/]

    BACKEND[Django REST Framework]

    FRONTEND --> AUTH_API
    FRONTEND --> CATALOG_API
    FRONTEND --> CART_API
    FRONTEND --> SELLER_API
    FRONTEND --> ORDER_API
    FRONTEND --> ADMIN_API

    AUTH_API --> BACKEND
    CATALOG_API --> BACKEND
    CART_API --> BACKEND
    SELLER_API --> BACKEND
    ORDER_API --> BACKEND
    ADMIN_API --> BACKEND

    BACKEND --> DB[(PostgreSQL)]
```

All application communication is designed around secure HTTPS and JSON REST APIs.

---

# 🔒 Security

Security is an important part of the system because it handles user accounts, personal information and payment-related transactions.

## Security Measures

### Password Security

* Passwords are securely hashed.
* Passwords are never stored as plain text.
* Passwords should not appear in application logs.

### HTTPS

The system uses HTTPS for communication between:

```text
React
  ↓
Django REST
  ↓
External Services
```

TLS 1.2 or higher is required.

### JWT Authentication

* Signed JWT tokens
* Short-lived access token
* Refresh token
* Token expiry
* Token revocation after logout

The specified access-token expiry is 15 minutes.

### Role-Based Access Control

```text
Customer
   ├── Customer APIs
   └── Cannot access Admin APIs

Seller
   ├── Seller APIs
   └── Cannot access unauthorized Admin APIs

Admin
   └── Admin APIs
```

### Payment Security

The application does not store raw:

* Card number / PAN
* CVV
* Sensitive card information

Payment information is handled through external PCI-DSS-compliant payment gateways using tokenization.

---

# ⚡ Non-Functional Requirements

The project has the following major quality requirements.

| Requirement                 | Target                                   |
| --------------------------- | ---------------------------------------- |
| API Response Time           | ≤ 2 seconds for 95% of relevant requests |
| Availability                | ≥ 99.5% monthly                          |
| Concurrent Sessions         | At least 500                             |
| Performance Degradation     | ≤ 20%                                    |
| Accessibility               | WCAG 2.1 AA                              |
| Lighthouse UX/Accessibility | > 90/100                                 |
| Audit/Data Retention        | Minimum 3 years                          |
| Order Integrity             | ACID transactions                        |

These requirements are defined in the SRS under BOOK-NF-001 through BOOK-NF-006.

---

# 🧪 Testing

Testing will cover:

```mermaid
flowchart TD

    TEST[Testing]

    TEST --> FUNC[Functional Testing]
    TEST --> INT[Integration Testing]
    TEST --> PERF[Performance Testing]
    TEST --> SEC[Security Testing]
    TEST --> UX[Usability & Accessibility]
    TEST --> DB[Database Testing]
    TEST --> REL[Reliability Testing]
    TEST --> REG[Regression Testing]

    FUNC --> AUTH[Authentication]
    FUNC --> CAT[Catalog]
    FUNC --> CART[Cart]
    FUNC --> ORDER[Orders]
    FUNC --> ADMIN[Administration]

    SEC --> TLS[HTTPS / TLS]
    SEC --> JWT[JWT]
    SEC --> RBAC[RBAC]
    SEC --> PASS[Password Security]
    SEC --> PAYMENT[Payment Tokenization]
```

### Testing Tools

| Tool             | Purpose                      |
| ---------------- | ---------------------------- |
| Postman          | API testing                  |
| JMeter           | Load and performance testing |
| Browser DevTools | Frontend and network testing |
| Lighthouse       | Accessibility testing        |
| PostgreSQL Tools | Database validation          |
| Docker           | Environment setup            |
| Git              | Version control              |

---

# 📋 Requirements Traceability

The SRS contains a Requirements Traceability Matrix connecting requirements to test cases.

| Requirement Group | Test Cases              |
| ----------------- | ----------------------- |
| Authentication    | TC-Auth-01 → TC-Auth-05 |
| Catalog           | TC-Cat-01 → TC-Cat-05   |
| Seller            | TC-Sel-01               |
| Orders            | TC-Ord-01 → TC-Ord-05   |
| Administration    | TC-Adm-01 → TC-Adm-04   |
| Performance       | TC-Perf-01 → TC-Perf-02 |
| Reliability       | TC-Rel-01               |
| UI/Accessibility  | TC-UX-01                |
| Data Retention    | TC-Dat-01               |
| Data Integrity    | TC-Int-01               |
| Security          | TC-Sec-01 → TC-Sec-05   |

---

# 📁 Project Structure

A recommended project structure is:

```text
online-bookstore/
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── hooks/
│   │   ├── context/
│   │   └── App.jsx
│   │
│   ├── package.json
│   └── README.md
│
├── backend/
│   ├── manage.py
│   ├── requirements.txt
│   │
│   ├── bookstore/
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   │
│   ├── users/
│   ├── books/
│   ├── cart/
│   ├── orders/
│   ├── payments/
│   ├── reviews/
│   └── sellers/
│
├── tests/
│   ├── functional/
│   ├── integration/
│   ├── security/
│   └── performance/
│
├── docs/
│   ├── SRS/
│   ├── TestPlan/
│   └── diagrams/
│
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

---

# ⚙️ Technology Stack

| Layer                | Technology            |
| -------------------- | --------------------- |
| Frontend             | React                 |
| Backend              | Django REST Framework |
| Programming Language | Python 3.11+          |
| Database             | PostgreSQL 15+        |
| API                  | REST / JSON           |
| Authentication       | JWT                   |
| Payment              | Stripe / Razorpay     |
| Email                | SMTP / SendGrid       |
| Containerization     | Docker                |
| Deployment           | AWS EC2 / Render      |
| Version Control      | Git                   |

The SRS identifies React, Django REST Framework, PostgreSQL, Docker/Linux infrastructure and Python 3.11+ as the main technical environment.

---

# 🚀 Installation and Setup

## 1. Clone the Repository

```bash
git clone <repository-url>
cd online-bookstore
```

---

## 2. Backend Setup

Create a Python virtual environment:

```bash
python3 -m venv venv
```

Activate it:

### Linux / macOS

```bash
source venv/bin/activate
```

### Windows

```bash
venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r backend/requirements.txt
```

---

## 3. Configure Environment Variables

Create a `.env` file using `.env.example`.

Example:

```env
DEBUG=True

SECRET_KEY=your-secret-key

DB_NAME=bookstore
DB_USER=postgres
DB_PASSWORD=your-password
DB_HOST=localhost
DB_PORT=5432

PAYMENT_GATEWAY_KEY=your-payment-key

EMAIL_HOST=smtp.example.com
EMAIL_PORT=587
EMAIL_USER=your-email
EMAIL_PASSWORD=your-password
```

**Do not commit `.env` to Git.**

---

# 🗄️ Database Setup

Create the PostgreSQL database:

```sql
CREATE DATABASE bookstore;
```

Run migrations:

```bash
python backend/manage.py makemigrations
python backend/manage.py migrate
```

Create an admin user:

```bash
python backend/manage.py createsuperuser
```

---

# ▶️ Running the Backend

```bash
python backend/manage.py runserver
```

The backend will normally be available at:

```text
http://127.0.0.1:8000/
```

For production deployment, HTTPS should be enabled as required by the SRS.

---

# ▶️ Running the Frontend

Move to the frontend directory:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

---

# 🐳 Docker Setup

The project can also be run using Docker.

```bash
docker compose build
docker compose up -d
```

Check running containers:

```bash
docker compose ps
```

Stop the application:

```bash
docker compose down
```

---

# 🔑 Basic User Flow

```mermaid
journey
    title Customer Shopping Journey

    section Account
      Register: 5: Customer
      Activate Email: 5: Customer
      Login: 5: Customer

    section Discovery
      Search Books: 5: Customer
      Apply Filters: 4: Customer
      View Book Details: 5: Customer

    section Shopping
      Add to Cart: 5: Customer
      Review Cart: 4: Customer
      Checkout: 5: Customer

    section Payment
      Make Payment: 4: Customer
      Receive Confirmation: 5: Customer

    section Order
      Track Order: 5: Customer
      Receive Book: 5: Customer
      Submit Review: 4: Customer
```

---

# 🏪 Seller Flow

```mermaid
flowchart TD

    A[Seller Login]
    B[Open Seller Section]
    C[Enter Book Details]
    D[Select Condition]
    E[Enter Price and Stock]
    F[Upload Cover Image]
    G[Submit Listing]
    H[Pending Approval]
    I{Admin Review}
    J[Approved]
    K[Rejected / Changes Required]
    L[Published in Catalog]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    I -->|Approve| J
    J --> L
    I -->|Reject| K
```

---

# 🛠️ Admin Flow

```mermaid
flowchart TD

    LOGIN[Admin Login]

    LOGIN --> DASH[Admin Dashboard]

    DASH --> CAT[Catalog Management]
    DASH --> SELLER[Seller Listings]
    DASH --> INV[Inventory]
    DASH --> ORD[Orders]
    DASH --> REV[Reviews]
    DASH --> SALES[Sales Dashboard]

    CAT --> ADD[Add / Edit / Deactivate / Delete]
    SELLER --> APPROVE[Approve Listings]
    INV --> STOCK[Update Stock]
    INV --> ALERT[Low Stock Alert]
    ORD --> STATUS[Update Order Status]
    REV --> MOD[Moderate Reviews]
    SALES --> ANALYZE[Revenue / Orders / Top Titles]
```

---

# 📊 Quality and Performance

The application is expected to satisfy the following major targets:

```text
API Response
     │
     └── 95% ≤ 2 seconds

Availability
     │
     └── ≥ 99.5% monthly

Concurrent Users
     │
     └── ≥ 500 sessions

Accessibility
     │
     └── WCAG 2.1 AA

Lighthouse
     │
     └── > 90/100
```

The performance, availability, concurrency and accessibility targets are specified in the SRS.

---

# 📜 Project Requirements

### Functional Requirements

The system contains **20 functional requirements**, covering:

* Authentication
* Catalog
* Cart
* Wishlist
* Seller listings
* Checkout
* Payment
* Email
* Orders
* Administration
* Inventory
* Reviews
* Sales dashboard

### Non-Functional Requirements

There are **6 non-functional requirements** covering:

* Performance
* Reliability
* Concurrent users
* Accessibility
* Data retention
* Transaction integrity

### Security Requirements

There are **5 security requirements** covering:

* Password security
* HTTPS/TLS
* JWT security
* RBAC
* Payment tokenization

The SRS defines these requirements and maps them to test cases through the RTM.

---

# 🧪 Acceptance Testing

The project uses five major acceptance test suites:

1. **User Authentication and Access**
2. **Catalog and Search**
3. **Checkout and Payment**
4. **Admin and Fulfillment**
5. **Quality and Performance**

These acceptance areas are defined in the SRS.

---

# 📌 Important Security Notes

Never commit sensitive credentials to GitHub.

Do not commit:

```text
.env
*.pem
*.key
database passwords
API secrets
payment gateway secrets
JWT secrets
```

Use environment variables for sensitive configuration.

Example `.gitignore`:

```gitignore
# Environment
.env
.env.*

# Python
__pycache__/
*.pyc
venv/

# Node
node_modules/
dist/

# Django
db.sqlite3
*.log

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db
```

---

# 📚 Documentation

Project documentation should contain:

```text
docs/
│
├── SRS/
│   └── Software Requirements Specification
│
├── TestPlan/
│   └── Software Test Plan
│
└── diagrams/
    ├── architecture
    ├── use-case
    ├── database
    └── system-flow
```

---

# 👨‍💻 Team

| Name                | USN           |
| ------------------- | ------------- |
| PRUTHVIRAJ H        | PES2UG24CS381 |
| PUNEET LINGASHETTAR | PES2UG24CS382 |
| PUNITH K            | PES2UG24CS383 |
| PURVITHA MACHIREDDY | PES2UG24CS384 |

**Team:** Team 4

---

# 📄 Project Documents

| Document             | Description                                |
| -------------------- | ------------------------------------------ |
| SRS                  | Software Requirements Specification        |
| Test Plan            | Software testing strategy and traceability |
| RTM                  | Requirements Traceability Matrix           |
| Use Case Diagram     | User-system interactions                   |
| Architecture Diagram | System components and communication        |
| Database Design      | Data and relationships                     |

---

# 📜 License

This project is developed as an **academic Software Engineering mini-project**.

It is intended for educational and demonstration purposes.

---

# 🙌 Acknowledgement

This project was developed as part of the Software Engineering coursework. The system design, requirements and testing approach are based on the project's Software Requirements Specification.

---

# ⭐ Summary

The **Online Bookstore System** provides a complete platform for buying and selling books through a web application.

```text
                    ONLINE BOOKSTORE
                           │
          ┌────────────────┼────────────────┐
          │                │                │
       CUSTOMER          SELLER           ADMIN
          │                │                │
      Browse Books     List Books       Manage Catalog
      Search           Submit Listing   Manage Inventory
      Cart             View Listing     Manage Orders
      Wishlist         ────────►        Moderate Reviews
      Checkout                          Sales Dashboard
      Payment
      Orders
      Reviews
```

The application combines a **React frontend, Django REST backend and PostgreSQL database**, with external services for payment and transactional email. The system is designed with authentication, role-based authorization, transaction integrity, performance and security as important parts of the overall implementation.

---

**Project:** Online Bookstore System – Buying & Selling Platform
**Version:** 1.0
**Team:** Team 4
**Academic Year:** 2026
