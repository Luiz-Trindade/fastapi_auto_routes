# ⚡ FastAPI Auto Routes  
> Dynamic CRUD & Auth Generator for SQLModel — minimal setup, maximum automation.

[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=flat&logo=fastapi)](https://fastapi.tiangolo.com/)
[![Python](https://img.shields.io/badge/Python-3.11%2B-blue?logo=python&logoColor=white)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![SQLModel](https://img.shields.io/badge/SQLModel-compatible-success)](https://sqlmodel.tiangolo.com/)
[![DiskCache](https://img.shields.io/badge/diskcache-enabled-orange)](https://grantjenks.com/docs/diskcache/)

---

## 🧠 Overview

**FastAPI Auto Routes** is a **plug-and-play dynamic router generator** that eliminates repetitive CRUD boilerplate.  
It automatically creates full-featured **CRUD endpoints** with:

- ✅ Authentication via Bearer tokens  
- ⚡ Smart caching (with TTL)  
- 🔄 Concurrency control  
- 🧩 Bulk operations  
- 🪪 Auto-generated `/login` and `/logout` routes  

All built on top of **FastAPI + SQLModel + diskcache**.

---

## 🚀 Installation

```bash
# Clone the repo
git clone https://github.com/yourusername/fastapi-auto-routes.git

cd fastapi-auto-routes

# Install dependencies
pip install -r requirements.txt
```

or using **Poetry**:

```bash
poetry add fastapi sqlmodel diskcache
```

---

## ⚙️ Example Usage

```python
from fastapi import FastAPI
from sqlmodel import SQLModel, Field
from app.db.config import engine
from app.utils.auto_routes import crud_router

class User(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    email: str
    password: str

# Create tables
SQLModel.metadata.create_all(engine)

app = FastAPI()

# 🔐 Auth Router (Login / Logout)
app.include_router(
    crud_router(User, login=True, login_fields=["email", "password"]),
    prefix="/auth",
    tags=["Auth"]
)

# ⚙️ CRUD Router (Requires Token)
app.include_router(
    crud_router(User, auth=True, ttl=120, max_concurrent=8),
    prefix="/users",
    tags=["Users"]
)
```

---

## 🧩 Generated Routes

| Route          | Method   | Description               | Auth Required |
| -------------- | -------- | ------------------------- | ------------- |
| `/auth/login`  | `POST`   | Generate session token    | No            |
| `/auth/logout` | `POST`   | Invalidate active session | ✅             |
| `/users/`      | `GET`    | Paginated list of users   | ✅             |
| `/users/{id}`  | `GET`    | Get user by ID            | ✅             |
| `/users/`      | `POST`   | Create user               | ✅             |
| `/users/{id}`  | `PATCH`  | Update user               | ✅             |
| `/users/{id}`  | `DELETE` | Delete user               | ✅             |

---

## 🪪 Authentication Example

```bash
# Login
curl -X POST http://localhost:8000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "123456"}'

# Response
{
  "token": "4b32f19a2e7e89a50cda22e03bdf3e41",
  "user": { "email": "test@example.com" }
}

# Authenticated request
curl -X GET http://localhost:8000/users/ \
  -H "Authorization: Bearer 4b32f19a2e7e89a50cda22e03bdf3e41"
```

---

## ⚡ Parameters

| Parameter        | Type             | Default       | Description                          |
| ---------------- | ---------------- | ------------- | ------------------------------------ |
| `model`          | `Type[SQLModel]` | —             | Your SQLModel class                  |
| `ttl`            | `int \| None`    | `None`        | Cache expiration time (seconds)      |
| `max_concurrent` | `int \| None`    | `cpu_count()` | Max concurrent operations            |
| `login`          | `bool`           | `False`       | Enables `/login` and `/logout`       |
| `login_fields`   | `List[str]`      | `None`        | Fields used for login validation     |
| `login_ttl`      | `int`            | `3600`        | Token lifetime in seconds            |
| `auth`           | `bool`           | `False`       | Requires Bearer token for all routes |

---

## 🧠 How It Works

1. **CRUD Generation**
   `crud_router()` dynamically builds all routes (`GET`, `POST`, `PATCH`, `DELETE`) for the given model.

2. **Authentication Layer**

   * `/login`: validates credentials and creates a token stored in `sessions_cache`.
   * `/logout`: invalidates the token.
   * Protected routes require the header:

     ```
     Authorization: Bearer <token>
     ```

3. **Caching & Concurrency**

   * Uses `diskcache` for persistent caching with optional TTL.
   * Uses `asyncio.Semaphore` for safe concurrency limits per model.

---

## 📂 Project Structure

```
app/
├── db/
│   └── config.py          # Database engine setup
├── utils/
│   └── auto_routes.py     # Core router generator
├── main.py                # FastAPI entrypoint
```

---

## 🧰 Requirements

* Python 3.11+
* FastAPI
* SQLModel
* DiskCache
* Uvicorn (for local testing)

---

## 🧞‍♂️ Philosophy

> **Automation without compromise.**

Instead of repeating CRUD definitions across every model, this tool dynamically builds routers that are **secure**, **scalable**, and **production-ready**.
Your backend becomes **data-driven**, not boilerplate-driven.

---

## 🧩 Roadmap

* [ ] Automatic route registration for all SQLModel models in a module
* [ ] Role-based access control (RBAC)
* [ ] Custom validation hooks
* [ ] Async database sessions

---

## 📜 License

MIT License © 2025 Luiz Gabriel Magalhães Trindade
Free for personal and commercial use.

---

## 🌐 Connect

* 🧠 **Project Author:** Luiz Gabriel Trindade
* 💼 GitHub: [@Luiz-Trindade](https://github.com/Luiz-Trindade)
* 📧 Contact: [Email](mailto:luiz.gabriel.m.trindade@gmail.com)

---

### ⭐ If this project saves you time, give it a star — it’s the currency of open source.

# fastapi_auto_routes
