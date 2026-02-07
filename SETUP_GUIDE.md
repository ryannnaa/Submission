# RENTAL INVENTORY MANAGEMENT SYSTEM - COMPLETE SETUP GUIDE

## Project Overview

This is a three-tier web application for managing rental inventory with real-time availability tracking.

**Architecture:**
- **Frontend**: React + Vite (Port 5173)
- **Backend**: Node.js + Express (Port 3001)
- **Database**: PostgreSQL
- **Cache**: Redis (Port 6379) - For performance optimization

## Project Structure

```
rental-inventory-system/
├── backend/              # Node.js API server
│   ├── server.js
│   ├── package.json
│   ├── .env (create this)
│   └── README.md
│
├── frontend/            # React web interface
│   ├── src/
│   │   ├── components/
│   │   │   ├── InventorySearch.jsx
│   │   │   ├── CurrentRentals.jsx
│   │   │   ├── DeliverySchedule.jsx
│   │   │   ├── UpcomingReturns.jsx
│   │   │   ├── LowAvailabilityAlert.jsx
│   │   │   └── ProductManagement.jsx  # CRUD operations
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── package.json
│   ├── vite.config.js
│   └── README.md
│
└── database/           # PostgreSQL schema
    ├── schema.sql
    └── data_sample.sql
```

---

## INSTALLATION STEPS

### Prerequisites

Before you begin, ensure you have:

1. **Node.js** (v18 or higher)
   ```bash
   node --version
   # Should show v18.x.x or higher
   ```

2. **PostgreSQL** (v14 or higher)
   ```bash
   psql --version
   # Should show 14.x or higher
   ```

3. **Redis** (v6 or higher) - For caching
   ```bash
   redis-cli --version
   # Should show v6.x.x or higher
   ```

4. **npm** (comes with Node.js)
   ```bash
   npm --version
   ```

---

### STEP 1: Database Setup

1. **Create the database:**
   ```bash
   createdb rental_inventory
   ```

2. **Load the schema:**
   ```bash
   psql rental_inventory < database/schema.sql
   ```

3. **Verify tables were created:**
   ```bash
   psql rental_inventory -c "\dt"
   ```
   
   You should see tables like: Invoice, Customer, Product, Line_item, etc.

4. **(Optional) Add sample data:**
   
   (REFER TO SECTION AT BOTTOM) You can manually insert test data or use the sample data in database/data_sample.sql

---

### STEP 2: Redis Setup (Caching Layer)

Redis provides a high-performance caching layer that speeds up queries by 25-100x.

1. **Install Redis:**

   **macOS:**
   ```bash
   brew install redis
   brew services start redis
   ```

   **Ubuntu/Debian:**
   ```bash
   sudo apt update
   sudo apt install redis-server
   sudo systemctl start redis
   sudo systemctl enable redis
   ```

   **Windows:**
   ```bash
   # Using Windows Subsystem for Linux (WSL)
   sudo apt install redis-server
   redis-server
   
   # OR using Docker
   docker run -d -p 6379:6379 redis
   ```

2. **Verify Redis is running:**
   ```bash
   redis-cli ping
   ```
   
   Expected response: `PONG`

3. **Test Redis connection:**
   ```bash
   redis-cli
   # In Redis CLI, type:
   SET test "Hello Redis"
   GET test
   # Should return: "Hello Redis"
   exit
   ```

4. **(Optional) Configure Redis for production:**
   
   For development, defaults are fine. For production:
   ```bash
   # Edit Redis configuration
   sudo nano /etc/redis/redis.conf
   
   # Key settings:
   # maxmemory 256mb
   # maxmemory-policy allkeys-lru
   # requirepass your_strong_password
   ```

**Note:** Redis is optional for basic functionality but highly recommended for performance when scaling beyond 100 products.

---

### STEP 3: Backend Setup

1. **Navigate to backend folder:**
   ```bash
   cd backend
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```
   
   This installs:
   - `express` - Web framework
   - `pg` - PostgreSQL client
   - `redis` - Redis client for caching
   - `cors` - Cross-origin resource sharing
   - `dotenv` - Environment variable management

3. **Create environment file:**
   ```bash
   cp .env.example .env
   ```

4. **Edit .env with your database credentials:**
   ```bash
   # Open .env in your text editor
   nano .env
   # or
   code .env
   ```
   
   Update the values:
   ```
   # PostgreSQL Configuration
   DB_USER=postgres
   DB_HOST=localhost
   DB_NAME=rental_inventory
   DB_PASSWORD=your_actual_password
   DB_PORT=5432
   
   # Redis Configuration
   REDIS_HOST=localhost
   REDIS_PORT=6379
   REDIS_PASSWORD=
   # Leave REDIS_PASSWORD empty for local development
   # Set a strong password for production
   
   # Server Configuration
   PORT=3001
   NODE_ENV=development
   ```

5. **Start the backend server:**
   ```bash
   npm run dev
   ```
   
   You should see:
   ```
> rental-inventory-backend@2.0.0 dev
> nodemon server.js

    [nodemon] 3.1.11
    [nodemon] to restart at any time, enter `rs`
    [nodemon] watching path(s): *.*
    [nodemon] watching extensions: js,mjs,cjs,json
    [nodemon] starting `node server.js`
    Server running on http://localhost:3001
    Health check: http://localhost:3001/api/health
    Connected to Redis
    Connected to PostgreSQL database
   ```
   
   **If Redis is not running:** You'll see a warning but the server will still work (without caching):
   ```
    Running without Redis cache
   ```

6. **Test the backend:**
   
   Open your browser and go to: http://localhost:3001/api/health
   
   You should see:
   ```json
   {
     "status": "healthy",
     "database": "connected",
     "redis": "connected",
     "timestamp": "2024-02-06T..."
   }
   ```
   
   **Note:** If Redis is not running, you'll see `"redis": "disconnected"` but the API will still function.

**Keep this terminal window open!** The backend needs to keep running.

---

### STEP 4: Frontend Setup

1. **Open a NEW terminal window** (keep backend running in the first)

2. **Navigate to frontend folder:**
   ```bash
   cd frontend
   ```

3. **Install dependencies:**
   ```bash
   npm install
   ```
   
   This will take a minute to download React, Vite, and other packages.

4. **Start the development server:**
   ```bash
   npm run dev
   ```
   
   You should see:
   ```
   VITE v5.0.8  ready in 500 ms

   ➜  Local:   http://localhost:5173/
   ➜  Network: use --host to expose
   ➜  press h to show help
   ```

5. **Open the application:**
   
   Go to: **http://localhost:5173**
   
   You should see the Rental Inventory Management System interface!

---

## VERIFICATION CHECKLIST

### ✅ Redis Running
- [ ] `redis-cli ping` returns PONG
- [ ] Backend shows "Connected to Redis"
- [ ] Health endpoint shows `"redis": "connected"`

### ✅ Backend Running
- [ ] Terminal shows "Server running on http://localhost:3001"
- [ ] http://localhost:3001/api/health returns status "healthy"
- [ ] No database connection errors in console
- [ ] Redis connection confirmed (or graceful fallback shown)

### ✅ Frontend Running
- [ ] Terminal shows "Local: http://localhost:5173/"
- [ ] Browser shows the application interface
- [ ] Navigation buttons work
- [ ] "Manage Products" tab accessible
- [ ] No console errors in browser DevTools

### ✅ Database Connected
- [ ] Can run: `psql rental_inventory -c "SELECT COUNT(*) FROM \"Product\";"`
- [ ] Backend shows "Connected to PostgreSQL database"

### ✅ Cache Performance (Optional Check)
- [ ] Open browser DevTools → Network tab
- [ ] Reload the same page twice
- [ ] Second load should be noticeably faster
- [ ] Server console shows cache HIT/MISS messages

---

## QUICK START COMMANDS

Once everything is set up, use these commands to start the system:

**Terminal 1 - Backend:**
```bash
cd backend
npm run dev
```

**Terminal 2 - Frontend:**
```bash
cd frontend
npm run dev
```

**Browser:**
```
http://localhost:5173
```

---

## TESTING THE APPLICATION

### 1. Product Management (NEW - CRUD Operations)

1. Click "📦 Manage Products"
2. View existing products in the table
3. Click "➕ Add New Product"
   - Fill in product name, category, quantity, description
   - Click "Create Product"
4. Test quantity adjustments using +/- buttons
5. Click "✏️ Edit" to modify a product
6. Try deleting a product

**Expected Result:** Full CRUD operations with instant cache invalidation

**Performance Note:** After creating/updating, the cache automatically clears and rebuilds. Watch the server console for cache invalidation logs.

---

### 2. Search Inventory

1. Click "🔍 Search Inventory"
2. Enter a search term (or leave blank for all products)
3. Select date range (optional)
4. Click "Search"

**Expected Result:** Table showing products with availability counts

**Cache Behavior:** First search queries database (CACHE MISS), subsequent identical searches use cache (CACHE HIT - much faster)

---

### 3. View Current Rentals

1. Click "📋 Current Rentals"

**Expected Result:** Table showing active and upcoming rentals

---

### 4. Delivery Schedule

1. Click "🚚 Deliveries"
2. Select a date

**Expected Result:** Cards showing deliveries scheduled for that date

---

### 5. Upcoming Returns

1. Click "↩️ Upcoming Returns"
2. Adjust the time window (7/14/30 days)

**Expected Result:** Table showing items being returned soon

---

### 6. Low Availability Alert

**Expected Result:** Widget at top of page showing products with low stock

**Cache Note:** This widget refreshes every 10 minutes automatically via cache TTL

---

## TROUBLESHOOTING

### Problem: "Error connecting to database"

**Solution:**
1. Check PostgreSQL is running: `pg_isready`
2. Verify database exists: `psql -l | grep rental_inventory`
3. Check credentials in `backend/.env`
4. Ensure password is correct

---

### Problem: "Redis connection failed" or "ECONNREFUSED"

**Solution:**
1. Check if Redis is running: `redis-cli ping`
2. If not running:
   - **macOS:** `brew services start redis`
   - **Linux:** `sudo systemctl start redis`
   - **Windows/Docker:** `docker start redis` or `redis-server`
3. Check Redis port: `redis-cli -p 6379 ping`
4. Verify `.env` has correct REDIS_HOST and REDIS_PORT

**Note:** App will work without Redis, just slower. You'll see: `⚠️ Running without Redis cache`

---

### Problem: "Port 3001 already in use"

**Solution:**
1. Kill existing process: `lsof -ti:3001 | xargs kill -9`
2. Or change port in `backend/.env`: `PORT=3002`

---

### Problem: "Cannot GET /api/..."

**Solution:**
1. Ensure backend is running
2. Check http://localhost:3001/api/health
3. Look for errors in backend terminal

---

### Problem: Frontend shows blank page

**Solution:**
1. Check browser console for errors (F12)
2. Ensure frontend server is running
3. Clear browser cache and reload

---

### Problem: "Failed to fetch" in browser

**Solution:**
1. Backend must be running first
2. Check CORS is enabled in backend
3. Verify proxy in `frontend/vite.config.js`

---

### Problem: Cache not working / No performance improvement

**Solution:**
1. Verify Redis is connected: Check health endpoint
2. Watch server console for cache HIT/MISS logs
3. Try the same query twice - second should be faster
4. Clear cache manually: `redis-cli FLUSHDB`
5. Restart backend server

---

### Problem: "Out of memory" error from Redis

**Solution:**
1. Check Redis memory: `redis-cli INFO memory`
2. Clear cache: `redis-cli FLUSHDB`
3. Increase maxmemory in redis.conf
4. Set eviction policy: `redis-cli CONFIG SET maxmemory-policy allkeys-lru`

---

### Problem: CRUD operations not reflecting in UI

**Solution:**
1. Check browser console (F12) for errors
2. Verify backend is receiving requests (Network tab)
3. Ensure cache is being invalidated (check server logs for "Invalidated cache")
4. Try manual cache clear: `curl -X POST http://localhost:3001/api/cache/invalidate`
5. Hard refresh browser: Ctrl+Shift+R (or Cmd+Shift+R on Mac)

---

## MONITORING CACHE PERFORMANCE

### View Cache Statistics

Check cache health and performance:
```bash
curl http://localhost:3001/api/cache/stats
```

Response shows:
- Connected status
- Number of cached keys
- Memory usage
- Hit/miss ratio

---

### Watch Cache Activity in Real-Time

Monitor all Redis operations:
```bash
redis-cli monitor
```

You'll see:
- Cache reads (GET operations)
- Cache writes (SET operations)  
- Cache deletions (DEL operations)
- TTL expirations

Press Ctrl+C to stop monitoring.

---

### View Cached Keys

List all cached data:
```bash
redis-cli KEYS '*'
```

Example output:
```
1) "inventory:search::2024-01-01:2024-12-31"
2) "products:list:all"
3) "low-stock:2024-02-06:2024-03-06:2"
4) "rentals:current:2024-02-06"
```

---

### Manually Clear Cache

Clear all cached data:
```bash
# Clear everything
redis-cli FLUSHDB

# Or use API endpoint
curl -X POST http://localhost:3001/api/cache/invalidate \
  -H "Content-Type: application/json" \
  -d '{"pattern": "*"}'
```

Clear specific cache patterns:
```bash
# Clear only inventory caches
curl -X POST http://localhost:3001/api/cache/invalidate \
  -H "Content-Type: application/json" \
  -d '{"pattern": "inventory:*"}'
```

---

### Understanding Cache TTL (Time To Live)

Different data types have different cache durations:

| Cache Type | TTL | Reason |
|------------|-----|--------|
| Product catalog | 1 hour | Rarely changes |
| Inventory availability | 5 minutes | Moderate freshness needed |
| Current rentals | 5 minutes | Users expect recent data |
| Low stock alerts | 10 minutes | Not time-critical |
| Delivery schedule | 5 minutes | Daily planning |
| Upcoming returns | 2 hours | Planning horizon |

After TTL expires, next query refreshes from database.

---

### Server Console Cache Logs

Watch your backend terminal for these messages:

```
Cache HIT: products:list:all           ← Fast! Served from Redis
Cache MISS: inventory:search:chair...  ← Queried database, cached result
Invalidated 5 cache keys: inventory:* ← Cleared after product update
```

- **HIT** = Data served from cache (1-5ms response)
- **MISS** = Queried database, result cached (10-50ms response)
- **Invalidated** = Cache cleared due to data changes

---

## ADDING SAMPLE DATA

To test the application with data, you can add sample records:

```sql
-- Connect to database
psql rental_inventory

-- Run sample data file
psql -f database/data_sample.sql

-- Add a category
INSERT INTO "Category" (id, name) 
VALUES (gen_random_uuid(), 'Chairs');

-- Add a product
INSERT INTO "Product" (sku, cat, name, quantity, desc) 
VALUES (
  gen_random_uuid(), 
  (SELECT id FROM "Category" WHERE name = 'Chairs'), 
  'Chiavari Chair Gold', 
  50, 
  'Gold chiavari chairs for events'
);

-- Add a customer
INSERT INTO "Customer" (id, name, email, contact, payment_method) 
VALUES (
  gen_random_uuid(),
  'John Doe',
  'john@example.com',
  '555-0100',
  'credit'
);

-- Add an employee
INSERT INTO "Employee" (id, name, email, contact, position) 
VALUES (
  gen_random_uuid(),
  'Jane Smith',
  'jane@companyabc.com',
  12345678,
  'Sales Manager'
);
```

---

## SUPPORT & DOCUMENTATION

- **Backend API Docs**: See `backend/README.md`
- **Frontend Docs**: See `frontend/README.md`
- **Full Documentation**: See `APPLICATION_DOCUMENTATION.md`

---

## STOPPING THE APPLICATION

When done:

1. **Stop Frontend:** Press `Ctrl+C` in frontend terminal
2. **Stop Backend:** Press `Ctrl+C` in backend terminal
3. **Database:** Runs as a service, no need to stop

---

## QUICK REFERENCE

### Start the Application
```bash
# Terminal 1 - Start Redis (if not running as service)
redis-server

# Terminal 2 - Start Backend
cd backend
npm start

# Terminal 3 - Start Frontend  
cd frontend
npm run dev
```

### Access Points
- **Application:** http://localhost:5173
- **API Health:** http://localhost:3001/api/health
- **Cache Stats:** http://localhost:3001/api/cache/stats

### Useful Commands
```bash
# Check Redis
redis-cli ping
redis-cli KEYS '*'
redis-cli FLUSHDB

# Check Database
psql rental_inventory -c "SELECT COUNT(*) FROM \"Product\";"

# Clear Cache via API
curl -X POST http://localhost:3001/api/cache/invalidate -H "Content-Type: application/json" -d '{"pattern":"*"}'

# View Logs
# Backend: Check terminal running npm start
# Redis: redis-cli monitor
```
