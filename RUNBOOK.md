
Below is a **production‑grade runbook** for installing, configuring, running, and troubleshooting this specific FastAPI course application.

---

# **📘 FastAPI Course Application — Full Runbook (Legacy Stack, Python 3.10)**

This runbook documents the **exact operational steps** required to install, configure, run, and maintain the FastAPI course application on Ubuntu with PostgreSQL.

---

## **1. System Requirements**
- Ubuntu 20.04 / 22.04 / 24.04  
- Python **3.10** (required due to legacy dependencies)  
- PostgreSQL (local or Docker)  
- GCC toolchain + dev headers  

---

## **2. Install System Dependencies**

```
sudo apt update
sudo apt install -y \
  python3.10 python3.10-venv python3.10-dev \
  build-essential libffi-dev libpq-dev \
  libxml2-dev libxslt1-dev zlib1g-dev
```

---

## **3. Clone the Project**

```
git clone https://github.com/Sanjeev-Thiyagarajan/fastapi-course
cd fastapi-course
```

---

## **4. Create Python 3.10 Virtual Environment**

```
python3.10 -m venv .venv
source .venv/bin/activate
```

---

## **5. Install Python Build Toolchain (Legacy-Compatible)**

```
pip install --upgrade pip
pip install "setuptools<60" "wheel<0.38"
```

---

## **6. Install Application Dependencies**

```
pip install -r requirements.txt
```

If any C‑extensions fail, install them manually:

```
pip install --no-build-isolation cffi psycopg2 ujson
```

---

## **7. Configure PostgreSQL**

### **Option A — Use Local PostgreSQL (default)**

```
sudo -u postgres psql
```

Inside psql:

```
CREATE DATABASE fastapi;
CREATE USER fastapi WITH PASSWORD 'fastapi';
GRANT ALL PRIVILEGES ON DATABASE fastapi TO fastapi;

\c fastapi
GRANT ALL ON SCHEMA public TO fastapi;
ALTER SCHEMA public OWNER TO fastapi;

\q
```

---

## **8. Create `.env` File**

Create `fastapi-course/.env`:

```
database_hostname=localhost
database_port=5432
database_username=fastapi
database_password=fastapi
database_name=fastapi

secret_key=supersecret
algorithm=HS256
access_token_expire_minutes=30
```

---

## **9. Run Database Migrations**

```
alembic upgrade head
```

Expected output:

```
Running upgrade ... create posts table
Running upgrade ... add user table
Running upgrade ... add foreign-key
...
```

---

## **10. Start the FastAPI Application**

```
uvicorn app.main:app --reload
```

Server runs at:

```
http://127.0.0.1:8000
```

Swagger UI:

```
http://127.0.0.1:8000/docs
```

---

# **11. Operational Tasks**

### **Create a User**
Use `/users/` endpoint in Swagger.

### **Authenticate**
Use `/login` to obtain JWT.

### **Create Posts**
Use `/posts/` with Authorization: Bearer <token>.

### **Vote**
Use `/vote/`.

---

# **12. Troubleshooting Guide**

### **Port 5432 already in use**
Local PostgreSQL is running:

```
sudo lsof -i :5432
```

Use local DB or change Docker port.

---

### **Permission denied for schema public**
Fix by granting privileges:

```
sudo -u postgres psql
GRANT ALL ON SCHEMA public TO fastapi;
ALTER SCHEMA public OWNER TO fastapi;
```

---

### **Python.h missing**
Install Python dev headers:

```
sudo apt install python3.10-dev
```

---

### **libffi missing**
```
sudo apt install libffi-dev
```

---

### **psycopg2 build failure**
Install PostgreSQL headers:

```
sudo apt install libpq-dev
```

---

### **ujson build failure**
Install:

```
sudo apt install libxml2-dev libxslt1-dev zlib1g-dev
```

---

# **13. Maintenance**

### **Upgrade dependencies (legacy-safe)**
```
pip install --upgrade pip
pip install "setuptools<60" "wheel<0.38"
```

### **Restart app**
```
pkill uvicorn
uvicorn app.main:app --reload
```

---

# **14. Optional Enhancements**

- Replace `psycopg2` with `psycopg2-binary`  
- Replace `ujson` with `orjson`  
- Add Docker Compose  
- Upgrade to FastAPI 0.115+ and SQLAlchemy 2.x  

