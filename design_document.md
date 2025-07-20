# Personal Expense Manager – Architecture Design Document

## 1. Objective

The objective of this project is to develop a personal expense management system tailored for individual use, which consolidates and categorizes expenses originating from various UPI platforms (GPay, Paytm, PhonePe). The system will leverage SMS parsing through a mobile application, coupled with a Node.js backend and a React frontend utilizing the Material Tailwind Dashboard template.

## 2. High-Level Architecture

```plaintext
             +-----------------------------+
             |   React Frontend (Web)      |
             | Material Tailwind Template  |
             +-------------+---------------+
                           |
                           v
               +-----------+-----------+
               |     Node.js Backend   |
               |   Express + MongoDB   |
               +-----------+-----------+
                           |
        +------------------+------------------+
        |                                     |
        v                                     v
+---------------+                   +-------------------+
| MongoDB (User)|                   | MongoDB (Expenses)|
+---------------+                   +-------------------+
                           ^
                           |
          +-------------------------------+
          |  React Native Mobile App      |
          |  - Reads UPI SMS messages     |
          |  - Parses & sends to backend  |
          +-------------------------------+
```

## 3. Component Overview

### Frontend (React + Tailwind CSS)

* Dashboard: Overview of spending patterns.
* Add Expense: Manual entry form.
* Transaction History: Filterable and sortable listing.
* Uploads: Optional support for PDF/CSV uploads.
* Charts: Visual representation via Chart.js or Recharts.
* Authentication: JWT-based login/registration.

### Mobile Application (React Native)

* Target: Android only (due to SMS access restrictions on iOS).
* Responsibilities:

  * Request and manage `READ_SMS` permissions.
  * Parse messages from pre-identified UPI sources.
  * Extract and format relevant data fields.
  * Dispatch structured data to the backend via secure API.

### Backend (Node.js + Express)

**API Endpoints:**

* `POST /api/auth/register`
* `POST /api/auth/login`
* `GET /api/expenses`
* `POST /api/expenses`
* `POST /api/expenses/sms`
* `GET /api/summary`
* `POST /api/upload/pdf`
* `GET /api/categories`

**Middleware:**

* JWT-based authentication.
* Rate limiting and input validation.

### Database Design (MongoDB)

**users Collection:**

```json
{
  _id: ObjectId,
  name: "John Doe",
  email: "john@example.com",
  passwordHash: "...",
  createdAt: ISODate
}
```

**expenses Collection:**

```json
{
  _id: ObjectId,
  userId: ObjectId,
  amount: 125.50,
  type: "upi",
  category: "Food",
  to: "paytm@upi",
  source: "UPI",
  date: ISODate,
  rawMessage: "Rs.125 debited from...",
  createdAt: ISODate
}
```

**categories Collection (Optional):**

```json
{
  _id: ObjectId,
  name: "Food",
  icon: "🍕"
}
```

## 4. Authentication & Security

* JWT-based token system.
* bcrypt for secure password hashing.
* Route protection with middleware.
* SMS payloads authenticated via token.

## 5. SMS Parsing Workflow

1. SMS arrives from UPI service.
2. Mobile app identifies and parses the SMS.
3. Data is extracted using regular expressions.
4. Formatted data is transmitted to backend.
5. Backend stores the record and associates it with a user.

## 6. Technology Stack

| Layer    | Technology                                  |
| -------- | ------------------------------------------- |
| Frontend | React + Tailwind CSS                        |
| Mobile   | React Native (Android only)                 |
| Backend  | Node.js + Express                           |
| Database | MongoDB                                     |
| Auth     | JWT                                         |
| Charts   | Chart.js / Recharts                         |
| Hosting  | Vercel (frontend), Render/Railway (backend) |

## 7. Suggested Folder Structure

### Frontend

```
/src
  /components
  /pages
  /services
  /contexts
  /utils
```

### Backend

```
/src
  /routes
  /controllers
  /models
  /middleware
  /utils
```

### Mobile App

```
/src
  /screens
  /components
  /services
  /utils
```

## 8. Future Enhancements

* Monthly budgets and alerts.
* Expense export (CSV, Excel).
* AI-based auto-categorization.
* Integration with Gmail or bank feeds.
* Push notifications for recurring expenses.

## 9. Testing & Monitoring

* Postman collections for endpoint validation.
* Jest + Supertest for backend unit testing.
* Firebase Crashlytics (optional for mobile).
* LogRocket/Sentry for frontend observability.

## 10. Development Roadmap

### Phase 1

* Manual entry via web.
* JWT authentication.
* SMS ingestion via mobile app.
* Expense listing and dashboard summaries.

### Phase 2

* File upload and parsing.
* Charts and data visualization.
* Category management.
* Exporting features.

### Phase 3

* Automated categorization.
* Email parsing.
* Budget tracking and alerting.
* Multi-user support and shared accounts (optional).

---

This document provides the structural foundation required to implement a modular, scalable, and secure personal expense management system. Adjustments may be made during development to accommodate edge cases and platform constraints.
