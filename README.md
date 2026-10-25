# 🏏 Cricket Auction Management System — MCA College Microproject

A complete, production-grade full-stack web application designed for cricket league auctions (IPL format). Features real-time live bidding, relational database integrity with MySQL InnoDB transactions, role-based access control, purse reconciliation, and responsive dark-charcoal UI.

---

## 🌟 Key System Features

- **Full-Stack Architecture**: React 18 + Vite frontend, Node.js + Express backend, MySQL 8+ database, and Socket.IO for live bidding feeds.
- **ACID Transaction-Safe Auction Engine**:
  - Pessimistic row locking (`SELECT ... FOR UPDATE`) prevents concurrent conflicting bids.
  - Double-sale prevention enforced by database constraints (`UNIQUE(player_id)` on `purchases`).
  - Budget validation and automatic purse deduction upon gavel hammer.
  - Negative purse balances strictly prevented by database `CHECK (remaining_purse >= 0)` constraints.
- **Two Distinct Roles**:
  - **Auctioneer Administrator**: Stage players, open/pause/resume/close bidding, hammer-sell or mark unsold, manage franchises, register players, and configure league rules.
  - **Franchise Representative (Team User)**: Authenticated directly to an assigned team. Can only bid using their franchise's purse up to remaining balance and squad limit.
- **Relational Analytics**:
  - SQL `JOIN`, `GROUP BY`, and aggregate calculations for top spenders, average player prices, and role-wise distribution.
  - Zero mock data or localStorage substitutes. All records persist directly to MySQL.

---

## 🛠️ Technology Stack

| Layer | Technologies |
|---|---|
| **Frontend** | React 18, React Router v6, Axios, Lucide React, Canvas Confetti, Custom CSS |
| **Backend** | Node.js (v18+), Express.js 4, mysql2/promise, bcryptjs, jsonwebtoken, helmet, cors |
| **Database** | MySQL 8.0+ (InnoDB Engine, Foreign Keys, Decimal Financial Precision) |
| **Real-Time** | Socket.IO (with automated polling fallback) |
| **Testing** | Vitest, Supertest |

---

## 📋 Software Prerequisites

1. **Node.js**: Version 18.x or 20.x+ ([Download Node.js](https://nodejs.org/))
2. **npm**: Version 9.x+ (bundled with Node.js)
3. **MySQL Server**: MySQL 8.0+ or MariaDB 10.5+ ([Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/)) or via **XAMPP** / **WampServer**.

---

## 🚀 Step-by-Step Installation & Setup

### 1. Clone or Open the Project
```bash
cd "C:\Users\jissc\OneDrive\Desktop\JISS THINKS!\microprojectog"
```

---

### 2. Configure the MySQL Database

#### Step 2.1: Start MySQL Server
Ensure MySQL is running on your machine (e.g. through Windows Services, XAMPP Control Panel, or command line).

#### Step 2.2: Create the Database & User
Open your MySQL terminal or phpMyAdmin / MySQL Workbench:
```sql
CREATE DATABASE IF NOT EXISTS cricket_auction_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;
```

#### Step 2.3: Execute Schema
Run the schema script to generate the 8 normalized InnoDB tables and constraints:
```bash
# Using MySQL Command Line Client:
mysql -u root -p cricket_auction_db < database/schema.sql

# OR using the provided npm command:
npm run db:init
```

---

### 3. Configure Environment Variables

#### Backend (`server/.env`):
Create or edit `server/.env` with your local database credentials:
```env
PORT=5000
NODE_ENV=development

# MySQL Credentials
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_mysql_password_here
DB_NAME=cricket_auction_db
DB_CONNECTION_LIMIT=10

# Security & JWT
JWT_SECRET=cricket_auction_secret_key_change_in_production_mca_2026
JWT_EXPIRES_IN=24h

# Frontend Client URL
CLIENT_URL=http://localhost:5173
```

#### Frontend (`client/.env`):
Verify `client/.env`:
```env
VITE_API_URL=/api
VITE_SOCKET_URL=http://localhost:5000
```

---

### 4. Install Dependencies

Install root, backend, and frontend packages:
```bash
# Root & Backend dependencies
cd server
npm install

# Frontend dependencies
cd ../client
npm install

# Return to root
cd ..
```

---

### 5. Populate Development Seed Data

Populate the database with 8 official franchises, 48 realistic players, default purses (₹100.00 Cr each), and pre-hashed user credentials:
```bash
npm run seed
```
*(This executes `server/src/config/seed.js`, hashing all passwords with Bcrypt and ensuring exact initial budget reconciliation).*

---

### 6. Starting the Application

#### Option A: Run Both Together (from Project Root)
Open two terminal windows:

**Terminal 1 (Backend Express & Socket.IO Server):**
```bash
npm run server
# Server starts at http://localhost:5000
```

**Terminal 2 (Frontend React + Vite Client):**
```bash
npm run client
# Client starts at http://localhost:5173
```

#### Option B: Production Build
```bash
cd client
npm run build
# Dist output generated in client/dist
```

---

## 🔑 Default Login Credentials

All passwords are automatically salted and hashed via `bcryptjs`:

| Role | Username | Password | Assigned Franchise | Access Level |
|---|---|---|---|---|
| **System Administrator** | `admin` | `Admin@123` | *None (All-access)* | Full Auctioneer & System Management |
| **Team Owner (CSK)** | `csk_owner` | `Team@123` | Chennai Super Kings | Bidding & Squad for CSK |
| **Team Owner (MI)** | `mi_owner` | `Team@123` | Mumbai Indians | Bidding & Squad for MI |
| **Team Owner (RCB)** | `rcb_owner` | `Team@123` | Royal Challengers Bengaluru | Bidding & Squad for RCB |
| **Team Owner (KKR)** | `kkr_owner` | `Team@123` | Kolkata Knight Riders | Bidding & Squad for KKR |
| **Team Owner (RR)** | `rr_owner` | `Team@123` | Rajasthan Royals | Bidding & Squad for RR |
| **Team Owner (SRH)** | `srh_owner` | `Team@123` | Sunrisers Hyderabad | Bidding & Squad for SRH |
| **Team Owner (DC)** | `dc_owner` | `Team@123` | Delhi Capitals | Bidding & Squad for DC |
| **Team Owner (GT)** | `gt_owner` | `Team@123` | Gujarat Titans | Bidding & Squad for GT |

*Quick-fill credentials buttons are provided directly on the Login page for convenient testing and college demonstration.*

---

## 🌐 Application URLs & Health Check

- **Frontend Client**: `http://localhost:5173`
- **Backend API Server**: `http://localhost:5000`
- **API Health Check**: `http://localhost:5000/api/health`
- **Socket.IO Live Feed**: `ws://localhost:5000`

---

## 🧪 Running Automated Tests

Run backend unit and integrity tests using Vitest:
```bash
cd server
npm test
```
The test suite validates:
- Indian currency formatting utilities (`formatIndianCurrency`)
- Auction state machine transition invariants
- Minimum bid calculations and step increments
- Team purse sufficiency and negative balance rejections
- Squad ceiling restrictions (max 25 players)
- Single-sale duplicate protection
- JWT authentication and role-based access middleware (`requireAdmin`, `requireTeamAccess`)

---

## ⚠️ Troubleshooting Guide

### 1. `ECONNREFUSED 127.0.0.1:3306` (Database Connection Error)
- Ensure your MySQL service is started. In Windows, press `Win + R`, type `services.msc`, locate **MySQL** or **MySQL80**, and click **Start**.
- Verify `DB_USER` and `DB_PASSWORD` in `server/.env`.
- If using XAMPP, open XAMPP Control Panel and start the **MySQL** module.

### 2. Port 5000 or 5173 already in use
- Change `PORT=5001` in `server/.env` and update the proxy in `client/vite.config.js`.
- Or terminate the existing process in PowerShell:
  ```powershell
  Get-Process -Id (Get-NetTCPConnection -LocalPort 5000).OwningProcess | Stop-Process
  ```

### 3. Re-seeding / Resetting Development Data Safely
To reset the development database back to a clean state:
```bash
npm run seed
```
*Note: This clears existing bids, purchases, and audit records, and re-initializes the 8 franchises with full ₹100 Cr purse allocations.*

---

## 📂 Project Structure

```
cricket-auction-management/
├── client/                     # React 18 + Vite Frontend
│   ├── src/
│   │   ├── components/         # Reusable UI components
│   │   │   ├── auction/        # AuctionPlayerPanel, BidForm, BidHistoryTable, AdminControls
│   │   │   ├── common/         # Sidebar, Topbar, SummaryCard, ConfirmationModal, etc.
│   │   │   ├── player/         # PlayerCard
│   │   │   └── team/           # TeamCard
│   │   ├── context/            # AuthContext, ToastContext, AuctionContext
│   │   ├── layouts/            # MainLayout (Sidebar + Topbar + Outlet)
│   │   ├── pages/              # 12 Functional Pages (Dashboard, Arena, Players, etc.)
│   │   ├── services/           # Axios API modules (auction, auth, player, team, reports, etc.)
│   │   ├── styles/             # IPL Dark Theme CSS Variables & Custom Styles
│   │   ├── utils/              # Currency and DateTime formatters
│   │   ├── App.jsx             # React Router Route Definitions
│   │   └── main.jsx            # React Root Entry Point
│   ├── vite.config.js          # Vite config with API & WebSocket proxy
│   └── package.json
│
├── server/                     # Node.js + Express Backend
│   ├── src/
│   │   ├── config/             # Database connection pool (mysql2) & seeders
│   │   ├── controllers/        # Business logic (auction, auth, team, player, etc.)
│   │   ├── middleware/         # Auth guards, role checks, validator, audit logger
│   │   ├── routes/             # REST API routers
│   │   ├── sockets/            # Socket.IO live auction broadcast events
│   │   ├── utils/              # Currency helpers & standardized JSON responses
│   │   ├── app.js              # Express app setup
│   │   └── server.js           # HTTP & Socket.IO server startup
│   ├── tests/                  # Vitest test suite
│   └── package.json
│
├── database/
│   ├── schema.sql              # Normalized DDL script (InnoDB, constraints, indexes)
│   └── seed.sql                # Initial 8 franchises, 48 players, settings
│
├── docs/
│   ├── database-design.md      # Full ERD and relational specification
│   └── api-documentation.md    # Complete REST and Socket.IO API reference
│
└── README.md                   # Beginner-friendly setup guide
```
