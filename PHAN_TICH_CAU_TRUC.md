# 📊 PHÂN TÍCH CẤU TRÚC SOURCE CODE - HỆ THỐNG ĐẶT TRÀ SỮA

## 🎯 TỔNG QUAN DỰ ÁN

**Tên dự án:** Katuu Milk Tea Ordering System (Boba Bliss)  
**Công nghệ:** React 19 + TypeScript + Vite 6 + Supabase  
**Kiến trúc:** Single Page Application (SPA) với Context API

---

## 📁 CẤU TRÚC THƯ MỤC CHÍNH

```
Katuu-milktea/
├── src/                    # Source code chính
│   ├── config/            # Cấu hình (Supabase)
│   ├── controllers/       # State Management (Context API)
│   ├── models/            # Business Logic & Services
│   ├── views/             # UI Components & Pages
│   ├── routes/            # Route Configuration
│   ├── layouts/           # Layout Components
│   ├── hooks/             # Custom React Hooks
│   └── utils/             # Helper Functions
├── supabase/              # Database Schema
├── scripts/               # Build Scripts
├── server/                # Mock Server (Development)
└── [config files]         # Vite, TypeScript, Tailwind configs
```

---

## 🏗️ KIẾN TRÚC PHÂN TẦNG

### 1️⃣ **PRESENTATION LAYER (views/)**
Chứa tất cả UI components và pages

#### **Pages (views/pages/)** - 23 files
- `OrderingPage.tsx` - Trang đặt hàng chính (Guest)
- `HistoryPage.tsx` - Lịch sử đơn hàng cá nhân
- `LoginPage.tsx` - Trang đăng nhập Staff/Admin
- `DashboardPage.tsx` - Dashboard cho Staff/Admin
- `PendingOrdersPage.tsx` - Quản lý đơn hàng chờ xử lý
- `MergedOrdersPage.tsx` - Lịch sử đơn đã merge
- `AdminPanelPage.tsx` - Quản trị hệ thống (CRUD)
- `SummaryPage.tsx` - Tổng hợp báo cáo

#### **Components (views/components/)** - Reusable UI
- `ProductSelection.tsx` - Hiển thị danh sách sản phẩm
- `CustomizationOptions.tsx` - Tùy chỉnh đồ uống (size, sugar, ice, toppings)
- `OrderSummary.tsx` - Tóm tắt đơn hàng
- `Header.tsx` - Navigation bar
- `Invoice.tsx`, `PrintableInvoice.tsx` - In hóa đơn
- Các components khác...

---

### 2️⃣ **BUSINESS LOGIC LAYER (models/)**

#### **Services - 6 files chính**

##### `types.ts` - Type Definitions
```typescript
- Category, Product, Topping, Size     // Menu items
- Order, OrderItem                     // Order structure  
- PendingOrder, MergedOrder           // Order states
- HistoricOrder                       // Personal history
- AuditEntry                          // Admin audit log
```

##### `authService.ts` - Authentication
```typescript
- login(username, password)           // Đăng nhập
- logout()                           // Đăng xuất
- getCurrentUser()                   // Lấy user hiện tại
- verifyToken()                      // Xác thực token
```
**Roles:** Guest (default), Staff, Admin

##### `menuService.ts` - Menu Management
```typescript
- fetchCategories()                  // Lấy danh mục
- fetchProducts()                    // Lấy sản phẩm
- fetchToppings()                    // Lấy topping
- fetchSizes()                       // Lấy size
```
**Fallback:** Nếu Supabase không khả dụng → dùng `constants.ts`

##### `orderService.ts` - Order Management
```typescript
// Pending Orders (Đơn chờ xử lý)
- fetchPendingOrders()               // Lấy đơn pending
- createPendingOrder(order)          // Tạo đơn mới
- deletePendingOrder(id)             // Xóa đơn

// Merged Orders (Đơn đã gộp)
- fetchMergedOrders()                // Lấy đơn đã merge
- createMergedOrder(orders)          // Gộp nhiều đơn
- mergeOrders(orderIds, staffName)   // Xử lý merge

// Real-time
- subscribePendingOrders(callback)   // Lắng nghe thay đổi
```

##### `adminService.ts` - Admin CRUD
```typescript
// Product Management
- createProduct(product)
- updateProduct(id, data)
- deleteProduct(id)

// Topping Management  
- createTopping(topping)
- updateTopping(id, data)
- deleteTopping(id)

// Size Management
- createSize(size)
- updateSize(id, data)  
- deleteSize(id)

// Category Management
- createCategory(category)
- updateCategory(id, data)
- deleteCategory(id)

// Audit Logging
- logAudit(entry)                    // Ghi log hành động
- fetchAuditLogs()                   // Xem log
```

##### `constants.ts` - Static Data
Chứa dữ liệu mặc định khi offline:
- `PRODUCTS` - 8 sản phẩm mẫu
- `TOPPINGS` - 6 loại topping
- `SIZES` - 3 size (S/M/L)
- `CATEGORIES` - 4 danh mục
- `SUGAR_LEVELS` - 5 mức đường
- `ICE_LEVELS` - 4 mức đá

---

### 3️⃣ **STATE MANAGEMENT LAYER (controllers/)**

Sử dụng **React Context API** thay vì Redux

#### **AuthContext.tsx** - Authentication State
```typescript
interface AuthContextType {
  user: User | null;                 // User hiện tại
  login(username, password)          // Đăng nhập
  logout()                          // Đăng xuất
  isAuthenticated: boolean          // Trạng thái đăng nhập
  isAdmin()                         // Kiểm tra quyền admin
  isLoading: boolean                // Loading state
}
```
**Features:**
- Lưu token vào localStorage
- Auto-restore session khi reload
- Dispatch custom events: `katuu:authChanged`

#### **OrderSessionContext.tsx** - Order State
Quản lý giỏ hàng và đơn hàng của user

#### **AuditContext.tsx** - Audit Logging
Ghi lại mọi hành động của Admin

#### **ThemeContext.tsx** - Dark/Light Mode
Quản lý theme của ứng dụng

---

### 4️⃣ **ROUTING LAYER (routes/)**

#### `routes/index.tsx` - Route Configuration
```typescript
Router Structure:
├── / (RootLayout)
│   ├── /                      → OrderingPage (Public)
│   ├── /login                 → LoginPage
│   ├── /history               → HistoryPage (Public)
│   ├── /dashboard             → DashboardPage (Staff/Admin)
│   ├── /pending               → PendingOrdersPage (Staff/Admin)
│   ├── /merged                → MergedOrdersPage (Staff/Admin)  
│   ├── /summary               → SummaryPage (Staff/Admin)
│   └── /admin                 → AdminPanelPage (Admin Only)
```

#### `routes/ProtectedRoute.tsx` - Route Guard
```typescript
Props:
- requireStaff?: boolean       // Yêu cầu đăng nhập Staff
- requireAdmin?: boolean       // Yêu cầu quyền Admin
- children: ReactNode          // Component cần protect
```

---

### 5️⃣ **DATA LAYER (Supabase)**

#### Database Schema (`supabase/schema.sql`)

**Bảng chính:**

1. **pending_orders** - Đơn hàng chờ xử lý
```sql
Columns:
- id (TEXT)                    → Primary key
- customer_name (TEXT)         → Tên khách
- items (JSONB)               → Chi tiết món (array)
- total_price (INTEGER)       → Tổng tiền (VNĐ)
- created_at (TIMESTAMPTZ)    → Thời gian tạo
- status (TEXT)               → 'pending' | 'merged'

Indexes:
- idx_pending_orders_status
- idx_pending_orders_created_at
```

2. **merged_orders** - Đơn đã gộp (finalized)
```sql
Columns:
- id (TEXT)
- pending_order_ids (TEXT[])  → IDs đơn đã merge
- customer_names (TEXT[])     → Danh sách khách
- total_items (INTEGER)       → Số món
- total_price (INTEGER)       
- merged_by (TEXT)            → Staff xử lý
- merged_at (TIMESTAMPTZ)     
- items (JSONB)               → Tất cả món
```

3. **order_history** - Lịch sử cá nhân (Guest)
```sql
- Lưu đơn hàng của từng khách
- Dùng để tracking cá nhân
```

4. **products** - Sản phẩm
```sql
- id, name, price, image, category
```

5. **toppings** - Topping
```sql
- id, name, price
```

6. **sizes** - Kích thước
```sql
- id, name, price_modifier
```

7. **categories** - Danh mục
```sql
- id, name
```

8. **users** - Tài khoản
```sql
- id, username, password_hash, role, full_name
- Roles: admin, staff, guest
```

9. **audit_logs** - Nhật ký hành động Admin
```sql
- Tracking mọi CRUD của admin
```

#### **Row Level Security (RLS)**
- Public read: products, toppings, sizes, categories
- Authenticated write: pending_orders, merged_orders
- Admin only: users, audit_logs

#### **Real-time Subscriptions**
```typescript
// Lắng nghe thay đổi pending_orders
supabase
  .channel('pending_orders')
  .on('postgres_changes', 
    { event: '*', schema: 'public', table: 'pending_orders' },
    (payload) => {
      // Update UI real-time
    }
  )
  .subscribe();
```

---

### 6️⃣ **CONFIGURATION LAYER**

#### `config/supabase.ts` - Database Config
```typescript
- Khởi tạo Supabase client
- Kiểm tra VITE_SUPABASE_URL, VITE_SUPABASE_ANON_KEY
- Fallback mode nếu không config
```

#### `vite.config.ts` - Build Configuration
```typescript
Features:
- Dev server: localhost:3000
- Path alias: @ → ./src
- React plugin
- Environment variables
```

#### `tailwind.config.js` - UI Framework
Cấu hình Tailwind CSS cho styling

#### `tsconfig.json` - TypeScript Config
Strict mode, path mapping

---

## 🔄 LUỒNG XỬ LÝ CHÍNH

### 🛒 **Flow 1: Guest Order (Khách đặt hàng)**

```
1. OrderingPage (views/pages/)
   ↓
2. Chọn product → ProductSelection component
   ↓
3. Tùy chỉnh → CustomizationOptions component  
   (size, sugar, ice, toppings)
   ↓
4. Xem summary → OrderSummary component
   ↓
5. Xác nhận đặt → orderService.createPendingOrder()
   ↓
6. Lưu vào Supabase: pending_orders table
   ↓
7. Real-time update → Staff dashboard nhận notification
   ↓
8. Lưu history → localStorage + order_history table
```

### 👨‍💼 **Flow 2: Staff Process (Nhân viên xử lý)**

```
1. Login → AuthContext.login()
   ↓
2. DashboardPage → Hiển thị tổng quan
   ↓
3. PendingOrdersPage → useRealtimeOrders hook
   ↓
4. Nhận real-time updates từ Supabase
   ↓
5. Chọn nhiều đơn → Merge orders
   ↓
6. orderService.mergeOrders()
   ↓
7. Tạo merged_order → Supabase
   ↓
8. Cập nhật status pending_orders → 'merged'
   ↓
9. In hóa đơn → PrintableInvoice component
   ↓
10. View history → MergedOrdersPage
```

### 👑 **Flow 3: Admin Management (Quản trị viên)**

```
1. Login as Admin → AuthContext (role: admin)
   ↓
2. AdminPanelPage → Full CRUD interface
   ↓
3. Tabs: Products | Toppings | Sizes | Categories
   ↓
4. Create/Update/Delete → adminService
   ↓
5. Ghi audit log → AuditContext
   ↓
6. Update Supabase → Real-time sync
   ↓
7. All clients auto-refresh menu
```

---

## 🎨 UI/UX COMPONENTS BREAKDOWN

### **Atomic Design Pattern**

#### **Atoms (Nhỏ nhất)**
- Button, Input, Badge, Icon
- Không tách file riêng, inline trong components

#### **Molecules (Nhóm atoms)**
- `OptionSelector.tsx` - Radio/Checkbox group
- Card component cho products

#### **Organisms (Nhóm molecules)**
- `ProductSelection.tsx` - Grid sản phẩm + filters
- `CustomizationOptions.tsx` - Form tùy chỉnh đầy đủ
- `OrderSummary.tsx` - Cart + Checkout
- `Header.tsx` - Navigation bar

#### **Templates (Layout)**
- `RootLayout.tsx` - Main layout wrapper

#### **Pages (Full pages)**
- 8 pages như đã liệt kê ở trên

---

## 🔌 CUSTOM HOOKS

### `useRealtimeOrders.ts`
```typescript
// Real-time subscription cho pending orders
export function useRealtimeOrders() {
  const [orders, setOrders] = useState([]);
  
  useEffect(() => {
    // Subscribe to changes
    const subscription = supabase
      .channel('pending_orders')
      .on('postgres_changes', ...)
      .subscribe();
      
    return () => subscription.unsubscribe();
  }, []);
  
  return orders;
}
```

---

## 🛠️ UTILITIES

### `utils/formatting.ts`
```typescript
// Format giá tiền
export function formatPrice(price: number): string {
  return `${price.toLocaleString('vi-VN')}đ`;
}

// Format ngày giờ
export function formatDate(date: string): string {
  return new Date(date).toLocaleString('vi-VN');
}
```

---

## 🔐 AUTHENTICATION FLOW

```
Login Process:
1. User nhập username/password
2. authService.login() gọi Supabase Auth
3. Verify credentials
4. Generate JWT token
5. Store token → localStorage
6. Store user object → AuthContext
7. Dispatch 'katuu:authChanged' event
8. Redirect based on role:
   - admin → /admin
   - staff → /dashboard  
   - guest → /

Logout Process:
1. authService.logout()
2. Clear localStorage
3. Clear AuthContext
4. Dispatch 'katuu:authChanged'
5. Redirect → /login
```

---

## 📦 DEPENDENCIES

### **Production Dependencies**
```json
{
  "@supabase/supabase-js": "^2.81.1",  // Database client
  "html2canvas": "^1.4.1",              // Screenshot for invoice
  "jspdf": "^3.0.3",                    // PDF generation
  "jwt-decode": "^3.1.2",               // Token parsing
  "qrcode.react": "^4.2.0",             // QR code cho orders
  "react": "^19.2.0",                   // Framework
  "react-dom": "^19.2.0",
  "react-router-dom": "^7.1.1"          // Routing
}
```

### **Dev Dependencies**
```json
{
  "@vitejs/plugin-react": "^5.0.0",    // Vite React plugin
  "typescript": "~5.8.2",              // Type checking
  "vite": "^6.2.0"                     // Build tool
}
```

---

## 🚀 BUILD & DEPLOYMENT

### **Build Process**
```bash
npm run dev       # Vite dev server (port 3000)
npm run build     # Production build → dist/
npm run preview   # Preview production build
```

### **Build Output** (dist/)
```
dist/
├── index.html              # Entry point
├── assets/
│   ├── index-[hash].js    // Bundle JS (611 KB)
│   └── index-[hash].css   // Bundle CSS
└── vite.svg               // Favicon
```

### **Vercel Deployment**
- Đã cấu hình `vercel.json`
- Auto-detect Vite project
- Environment variables qua Vercel dashboard
- Deploy time: ~1 phút

---

## 🎯 DESIGN PATTERNS

### 1. **Context Provider Pattern**
```typescript
// Wrap toàn app với providers
<AuthProvider>
  <AuditProvider>
    <OrderSessionProvider>
      <RouterProvider />
    </OrderSessionProvider>
  </AuditProvider>
</AuthProvider>
```

### 2. **Service Layer Pattern**
```typescript
// Tách biệt business logic khỏi UI
// models/orderService.ts
export async function createOrder(...) {
  // Business logic here
}

// views/pages/OrderingPage.tsx  
import * as orderService from '@/models/orderService';
orderService.createOrder(order);
```

### 3. **Repository Pattern**
```typescript
// Abstraction layer cho database
// orderService.ts acts as repository
- Fallback to localStorage if Supabase unavailable
- Consistent API regardless of data source
```

### 4. **Observer Pattern**
```typescript
// Real-time subscriptions
supabase
  .channel('orders')
  .on('postgres_changes', callback)
  .subscribe();
```

### 5. **Protected Route Pattern**
```typescript
<ProtectedRoute requireAdmin>
  <AdminPanel />
</ProtectedRoute>
```

---

## 📊 DATA FLOW DIAGRAM

```
┌─────────────┐
│   Browser   │
│  (React UI) │
└──────┬──────┘
       │
       ├─── AuthContext ────────┐
       │                        │
       ├─── OrderContext ───────┤
       │                        │
       └─── AuditContext ───────┤
                                │
┌───────────────────────────────▼────────┐
│         Services (models/)              │
├─────────────────────────────────────────┤
│  authService   menuService   orderService│
│  adminService                           │
└──────────┬──────────────────────────────┘
           │
           ├─── Supabase Client ───┐
           │                       │
           └─── localStorage ──────┘
                                   │
                    ┌──────────────▼───────────┐
                    │   Supabase (PostgreSQL)  │
                    │   - pending_orders       │
                    │   - merged_orders        │
                    │   - products, etc.       │
                    │   - Real-time enabled    │
                    └──────────────────────────┘
```

---

## 🎨 STYLING APPROACH

### **Tailwind CSS Utility-First**
```typescript
// Inline Tailwind classes
<div className="bg-white rounded-lg shadow-md p-4 hover:shadow-lg transition-shadow">
  <h2 className="text-2xl font-bold text-gray-800">Title</h2>
</div>
```

### **Responsive Design**
```typescript
// Mobile-first breakpoints
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {/* Auto-responsive grid */}
</div>
```

### **Dark Mode Support**
- ThemeContext quản lý
- Tailwind dark: variants

---

## 🧪 TESTING CONSIDERATIONS

### **Manual Testing Checklist**
1. ✅ Guest đặt hàng end-to-end
2. ✅ Staff merge orders
3. ✅ Admin CRUD products
4. ✅ Real-time updates giữa tabs
5. ✅ Offline mode (localStorage fallback)
6. ✅ Login/logout flow
7. ✅ Role-based access control

### **Test Accounts** (mặc định)
```
Admin:  admin  / admin123
Staff:  staff  / staff123
Guest:  Không cần login
```

---

## 🔍 CODE QUALITY METRICS

### **TypeScript Coverage**
- ✅ 100% TypeScript (no .js files in src/)
- ✅ Strict type checking enabled
- ✅ Interface definitions cho tất cả data types

### **Component Structure**
- ✅ Functional components + Hooks
- ✅ Props interface cho mỗi component
- ✅ Comments JSDoc cho functions

### **File Organization**
- ✅ Clear separation of concerns
- ✅ Barrel exports (index.ts)
- ✅ Consistent naming conventions

---

## 🚨 POTENTIAL IMPROVEMENTS

### **Performance**
- [ ] Code splitting (React.lazy)
- [ ] Image optimization (WebP format)
- [ ] Virtual scrolling cho long lists
- [ ] Memoization cho expensive computations

### **Features**
- [ ] Payment integration (VNPay, Momo)
- [ ] Push notifications (PWA)
- [ ] Analytics tracking
- [ ] Multi-language (i18n)
- [ ] Customer reviews/ratings

### **UI/UX**
- [x] ✅ **FIXED 28/11/2025**: Header responsive mobile - scroll ngang
  - Icons và buttons giờ responsive với breakpoints
  - Logout button có text ngắn trên mobile
  - Global overflow-x-hidden
  - Details: `FIX_HEADER_MOBILE_SCROLL.md`

### **Testing**
- [ ] Unit tests (Jest + React Testing Library)
- [ ] E2E tests (Playwright)
- [ ] API mocking (MSW)

### **DevOps**
- [ ] CI/CD pipeline (GitHub Actions)
- [ ] Automated testing in pipeline
- [ ] Environment staging

---

## 📝 NAMING CONVENTIONS

### **Files**
- Components: PascalCase (e.g., `OrderingPage.tsx`)
- Services: camelCase (e.g., `orderService.ts`)
- Hooks: camelCase with `use` prefix (e.g., `useRealtimeOrders.ts`)

### **Variables**
- React components: PascalCase
- Functions: camelCase
- Constants: UPPER_SNAKE_CASE
- Interfaces: PascalCase

### **Database**
- Tables: snake_case (e.g., `pending_orders`)
- Columns: snake_case (e.g., `customer_name`)

---

## 🎓 KEY LEARNINGS

### **Architectural Decisions**

1. **Tại sao Context API thay vì Redux?**
   - App nhỏ/vừa, không cần Redux overhead
   - Context đủ cho state management đơn giản
   - Ít boilerplate code

2. **Tại sao Supabase?**
   - Backend-as-a-Service → nhanh chóng
   - Built-in auth, real-time, storage
   - PostgreSQL powerful
   - RLS bảo mật tốt

3. **Tại sao Vite thay vì CRA?**
   - Build nhanh hơn (HMR instant)
   - Bundle size nhỏ hơn
   - Native ES modules
   - Better DX

4. **Fallback to localStorage**
   - Offline-first approach
   - Development không cần Supabase
   - Resilient architecture

---

## 🔗 MODULE RELATIONSHIPS

```
App.tsx (Root)
  ↓
Providers (controllers/)
  ├── AuthContext
  ├── AuditContext  
  └── OrderSessionContext
       ↓
RouterProvider (routes/)
  ├── RootLayout
  │     ↓
  │   Header + Outlet
  │     ↓
  └── Pages (views/pages/)
        ↓
      Components (views/components/)
        ↓
      Services (models/)
        ↓
      Supabase Client (config/)
```

---

## 📈 SCALABILITY CONSIDERATIONS

### **Current Limitations**
- Single Supabase instance (có thể scale vertical)
- No caching layer (Redis)
- No CDN cho static assets
- No load balancer

### **How to Scale**
1. Add Redis caching
2. Implement CDN (Cloudflare)
3. Database read replicas
4. Microservices cho heavy logic
5. Queue system (RabbitMQ) cho async tasks

---

## 🎉 CONCLUSION

Đây là một **well-structured React application** với:

✅ **Clean Architecture** - Phân tầng rõ ràng (Presentation → Business → Data)  
✅ **Type Safety** - Full TypeScript coverage  
✅ **Real-time** - Supabase subscriptions  
✅ **Scalable** - Dễ mở rộng thêm features  
✅ **Maintainable** - Code dễ đọc, dễ bảo trì  
✅ **Production Ready** - Đã deploy Vercel  

**Điểm mạnh:**
- Context API đơn giản, hiệu quả
- Service layer tách biệt tốt
- Real-time updates mượt mà
- Responsive design
- Fallback mechanisms

**Điểm cần cải thiện:**
- Thiếu unit tests
- Chưa có error boundary
- Performance optimization
- Accessibility (a11y)

---

**📅 Ngày phân tích:** 28/11/2025  
**👨‍💻 Phân tích bởi:** GitHub Copilot  
**🔖 Version:** 1.0

