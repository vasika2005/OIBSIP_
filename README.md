🍕 Pizza Hub — Full-Stack Pizza Delivery App
A complete online pizza ordering platform with user & admin sides, real-time order tracking, Razorpay test payment integration, and stock management with low-stock email alerts.

Built with: Next.js 16 · TypeScript · Tailwind CSS 4 · Prisma ORM · Zustand · Razorpay · shadcn/ui

✨ Features
👤 User Side
Feature
Description
🔐 Authentication	Register / Login with HTTP-only cookie sessions (SHA-256 hashed passwords, 7-day tokens)
🍕 Pizza Builder	Choose size (Small / Medium / Large / X-Large), base (5), sauce (5), cheese (5), vegetables (10)
🛒 Cart	Quantity controls, duplicate merge, persistent (localStorage), tax & delivery fee calculation
💳 Razorpay Payment	Test-mode payment with server-side HMAC SHA256 signature verification
📦 Order Tracking	5-stage progress bar: Pending → Confirmed → Preparing → Out for Delivery → Delivered
📜 Order History	View all past orders with full status history

🛠️ Admin Side
Feature
Description
📊 Dashboard	Stats cards (total/active/delivered orders, revenue, low-stock count, ingredient count)
📦 Stock Management	Inline-editable table of all ingredients (stock, threshold, price, availability)
📋 Order Management	All orders list with filter, one-click status update
🚨 Low-Stock Alerts	Auto email notification when ingredient stock ≤ threshold (using z-ai-web-dev-sdk)

🧱 Tech Stack
Layer
Technology
Framework	Next.js 16 (App Router)
Language	TypeScript 5
Styling	Tailwind CSS 4 + shadcn/ui (New York)
Database	Prisma ORM + SQLite
State	Zustand (cart + auth, persisted)
Animations	Framer Motion
Icons	Lucide React
Payments	Razorpay (test mode)
AI Assist	z-ai-web-dev-sdk (low-stock alert formatting)

📁 Project Structure
text

pizza-hub/
├── prisma/
│   └── schema.prisma              # Database schema
├── scripts/
│   └── seed.ts                    # Seed data (admin + ingredients)
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   ├── auth/              # register, login, logout, me
│   │   │   ├── menu/              # GET menu (sizes + ingredients)
│   │   │   ├── orders/            # CRUD + track
│   │   │   ├── payment/          # create + verify Razorpay
│   │   │   └── admin/            # ingredients, orders, dashboard
│   │   ├── layout.tsx
│   │   ├── page.tsx               # Main SPA shell
│   │   └── globals.css
│   ├── components/
│   │   ├── ui/                    # shadcn/ui components
│   │   └── pizza/                 # App components
│   │       ├── header.tsx
│   │       ├── footer.tsx
│   │       ├── landing-view.tsx
│   │       ├── auth-view.tsx
│   │       ├── builder.tsx
│   │       ├── cart-view.tsx
│   │       ├── orders-view.tsx
│   │       ├── admin-view.tsx
│   │       ├── hooks.ts
│   │       └── types.ts
│   └── lib/
│       ├── auth.ts                # Password hashing + token
│       ├── db.ts                  # Prisma client
│       ├── email.ts               # Low-stock email + AI assist
│       ├── razorpay.ts            # Razorpay integration
│       ├── session.ts             # Session helper
│       ├── store.ts               # Zustand stores
│       └── utils.ts
├── public/                        # Static assets
├── .env.example                   # Environment template
├── .gitignore
└── package.json
🚀 Quick Start
Prerequisites
Node.js v18+ — Download
Bun (recommended) — Install
Git — Download
Installation (5 minutes)
bash

# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/pizza-hub.git
cd pizza-hub

# 2. Install dependencies
bun install

# 3. Set up environment
cp .env.example .env

# 4. Create database tables
bun run db:push

# 5. Seed demo data (admin + customer + 25 ingredients)
bun run scripts/seed.ts

# 6. Start dev server
bun run dev
Open http://localhost:3000 in your browser. 🎉

🔑 Demo Logins
Role
Email
Password
👤 Customer	customer@demo.com	demo123
🛠️ Admin	admin@pizza.com	admin123

You can also register a new customer account via the UI.

💳 Payment Modes
The app supports 2 payment modes — automatically selected based on environment variables:

Mode 1: Mock Mode (Default)
When: RAZORPAY_KEY_ID and RAZORPAY_KEY_SECRET not set in .env
Behavior: Simulates successful payment after 1.2-second delay
Use case: Local development without real Razorpay account
Mode 2: Real Razorpay Test Mode
When: Set RAZORPAY_KEY_ID and RAZORPAY_KEY_SECRET in .env
Behavior: Opens real Razorpay checkout popup
Use case: Production-like testing
Enable Real Razorpay Test Mode
Create free account at https://razorpay.com
Dashboard → API Keys → Generate Test Keys
Update .env:
env

RAZORPAY_KEY_ID="rzp_test_YOUR_KEY"
RAZORPAY_KEY_SECRET="YOUR_SECRET"
Restart server: bun run dev
Test Card Details (for Real Razorpay Test Mode)
text

Card Number: 4111 1111 1111 1111
CVV:         123
Expiry:      12/28
Name:        Any name
⚠️ No real money will be deducted — Razorpay test environment only.

📖 API Reference
Authentication
Method
Endpoint
Description
POST	/api/auth/register	Register new customer
POST	/api/auth/login	Login user
POST	/api/auth/logout	Logout (clear cookie)
GET	/api/auth/me	Get current session user

Menu & Orders
Method
Endpoint
Description
GET	/api/menu	Fetch all sizes + available ingredients
GET	/api/orders	List current user's orders
POST	/api/orders	Place new order (decrements stock)
GET	/api/orders/track?orderNumber=PZ12345678	Track order by number

Payment
Method
Endpoint
Description
POST	/api/payment/create	Create Razorpay order
POST	/api/payment/verify	Verify signature + mark order CONFIRMED

Admin (requires ADMIN role)
Method
Endpoint
Description
GET	/api/admin/ingredients	List all ingredients
POST	/api/admin/ingredients	Create new ingredient
PATCH	/api/admin/ingredients	Update ingredient (stock/threshold/price)
DELETE	/api/admin/ingredients?id=XXX	Delete ingredient
GET	/api/admin/orders	List all orders
PATCH	/api/admin/orders?id=XXX	Update order status
GET	/api/admin/dashboard	Get dashboard stats

🗄️ Database Schema
Models
User — id, email, name, phone, password, role (CUSTOMER/ADMIN), address
Ingredient — id, name, category (BASE/SAUCE/CHEESE/VEGETABLE), price, stock, threshold, available
PizzaSize — id, name, basePrice, diameter
Order — id, orderNumber, userId, customerName/Phone/Address, totalAmount, status, paymentStatus, paymentId, razorpayOrderId
OrderItem — id, orderId, pizzaSize, base, sauce, cheese, vegetables, quantity, unitPrice, total
StatusHistory — id, orderId, status, note, createdAt
Order Status Flow
text

PENDING → CONFIRMED → PREPARING → OUT_FOR_DELIVERY → DELIVERED
                                                       ↓
                                                  CANCELLED (anytime)
🛠️ Available Scripts
Command
Description
bun run dev	Start dev server on http://localhost:3000
bun run lint	Run ESLint to check code quality
bun run db:push	Push Prisma schema to database
bun run db:generate	Regenerate Prisma client
bun run db:reset	Reset database (delete all data)
bun run scripts/seed.ts	Seed demo data

🎯 User Flow (End-to-End)
Customer Journey
Register / Login with customer credentials
Build Pizza — select size, base, sauce, cheese, vegetables
Add to Cart — adjust quantities
Checkout — enter delivery details (pre-filled from account)
Pay via Razorpay (mock or real test mode)
Get Order Number (e.g., PZ20105647)
Track Order — view 5-stage progress + status history
Admin Journey
Login with admin credentials
Dashboard — view stats, recent orders, low-stock alerts
Stock Tab — edit ingredient stock/threshold/price inline
Orders Tab — view all orders, update status (Confirmed → Preparing → Delivered)
Email Alert — automatically sent when any ingredient hits low-stock
🔒 Security Features
✅ Passwords hashed with SHA-256 + salt
✅ HTTP-only cookies (XSS protection)
✅ Server-side session verification on every API call
✅ Role-based access control (CUSTOMER vs ADMIN)
✅ Razorpay HMAC SHA256 signature verification
✅ Zod input validation on all API routes
✅ Stock availability check before order creation
✅ Transactional order creation + stock decrement
📦 Deployment
Deploy to Vercel (Recommended)
Push your code to GitHub
Go to https://vercel.com → New Project
Import your GitHub repo
Add Environment Variables:
DATABASE_URL (use PostgreSQL/MySQL for production)
RAZORPAY_KEY_ID
RAZORPAY_KEY_SECRET
Deploy → Get live URL! 🚀
Deploy to Railway / Render
Push to GitHub
Create new project on Railway/Render
Connect GitHub repo
Add env vars + start command: bun run start
Deploy
🧪 Testing the App
Test Customer Flow
bash

1. Login as customer@demo.com / demo123
2. Customize 1 pizza → Add to Cart
3. Go to Cart → Proceed to Checkout
4. Enter/confirm delivery details
5. Click "Pay ₹354" → Mock payment succeeds
6. Get order number (e.g., PZ20105647)
7. Click "Track Your Order"
Test Admin Flow
bash

1. Logout → Login as admin@pizza.com / admin123
2. Dashboard → view stats
3. Stock tab → edit any ingredient's stock to below threshold
   → Low-stock email logged in terminal
4. Orders tab → click PREPARING → DELIVERED
Test Order Tracking
text

1. As customer, place an order → get order number
2. Logout
3. Go to "Track an Order"
4. Enter order number → see live status
🐛 Troubleshooting
Common Issues
Issue
Fix
bun: command not found	Install Bun: https://bun.sh
Port 3000 already in use	lsof -i :3000 → kill process, or use --port 3001
Cannot find module	Run bun install again
Prisma client not generated	Run bun run db:generate
Database corrupt	rm db/custom.db → bun run db:push → bun run scripts/seed.ts
Payment popup blocked	Disable browser popup blocker for localhost
Razorpay 401 error	Verify keys in .env, restart server

📝 Environment Variables
Create .env file in project root (use .env.example as template):

env

# Database (SQLite for dev, PostgreSQL for prod)
DATABASE_URL="file:./db/custom.db"

# Razorpay (leave empty for mock mode)
RAZORPAY_KEY_ID=""
RAZORPAY_KEY_SECRET=""
⚠️ Never commit .env to git! It's in .gitignore.

📜 License
MIT License — feel free to use, modify, and distribute.

🙏 Acknowledgments
Next.js — The React framework
Tailwind CSS — Utility-first CSS
shadcn/ui — Component library
Prisma — Type-safe ORM
Zustand — State management
Razorpay — Payment gateway
Lucide — Icon library
Framer Motion — Animations
📧 Contact
Built with ❤️ as a demo full-stack project.

Issues / Questions? Open an issue on GitHub.

🍕 Happy Pizza Ordering!
