````markdown
# React Router - Complete Beginner's Guide

A comprehensive from-scratch guide to React Router v6.

## Table of Contents

1. [What is React Router](#what-is-react-router)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [Route Components](#route-components)
5. [Navigation Links](#navigation-links)
6. [URL Parameters](#url-parameters)
7. [Nested Routes](#nested-routes)
8. [Programmatic Navigation](#programmatic-navigation)
9. [Query Parameters](#query-parameters)
10. [Protected Routes](#protected-routes)
11. [Route Layouts](#route-layouts)
12. [Lazy Loading](#lazy-loading)
13. [Complete Examples](#complete-examples)
14. [API Reference](#api-reference)

---

## What is React Router

React Router is a library that enables **navigation between different pages** in your React app without refreshing the browser.

**What React Router does:**

- Changes the URL when you click links
- Renders different components based on the URL
- Maintains browser history (back/forward buttons work)
- Passes data through URLs (parameters, query strings)

**Without React Router (multi-page app):**

```html
<!-- Each link causes full page refresh -->
<a href="/about">About</a>
<!-- Browser refreshes -->
<a href="/contact">Contact</a>
<!-- Browser refreshes -->
```
````

**With React Router (single-page app):**

```jsx
// No page refresh, just component changes
<Link to="/about">About</Link>  // URL changes, content updates
<Link to="/contact">Contact</Link>  // Smooth, fast navigation
```

## Installation

```bash
npm install react-router-dom
```

## Basic Setup

### Step 1: Wrap App with BrowserRouter

```jsx
// main.jsx or index.jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    {" "}
    {/* 1. Wrap your app */}
    <App />
  </BrowserRouter>,
);
```

### Step 2: Define Routes

```jsx
// App.jsx
import { Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import About from "./pages/About";
import Contact from "./pages/Contact";

function App() {
  return (
    <div>
      {/* 2. Define your routes */}
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </div>
  );
}
```

### Step 3: Add Navigation Links

```jsx
// components/Navigation.jsx
import { Link } from "react-router-dom";

function Navigation() {
  return (
    <nav>
      {/* 3. Use Link for navigation */}
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/contact">Contact</Link>
    </nav>
  );
}
```

---

## Route Components

### Understanding Routes and Route

```jsx
import { Routes, Route } from "react-router-dom";

function App() {
  return (
    <Routes>
      {" "}
      {/* Routes - container for all routes */}
      {/* Route - maps a URL path to a component */}
      <Route path="/" element={<Home />} />
      {/* Path with parameter */}
      <Route path="/users/:id" element={<UserProfile />} />
      {/* Index route (renders at parent path) */}
      <Route path="/dashboard" element={<DashboardLayout />}>
        <Route index element={<Overview />} /> {/* Renders at /dashboard */}
        <Route path="settings" element={<Settings />} />{" "}
        {/* /dashboard/settings */}
      </Route>
      {/* Catch-all route (404) */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

### Route Props Explained

```jsx
<Route
  path="/about"           // URL path to match
  element={<About />}     // Component to render
  index                   // Index route (no path needed)
  caseSensitive={true}    // Match case exactly (/About not /about)
/>

// Path patterns
<Route path="/" element={<Home />} />                    // Exact match
<Route path="/about" element={<About />} />              // /about
<Route path="/users/:id" element={<User />} />           // /users/123
<Route path="/products/*" element={<Products />} />      // /products/anything
<Route path="*" element={<NotFound />} />                // Catch all
```

---

## Navigation Links

### Link Component - Basic Navigation

```jsx
import { Link } from "react-router-dom";

function Navigation() {
  return (
    <nav>
      {/* Basic link */}
      <Link to="/">Home</Link>

      {/* Link with state (data passed to next page) */}
      <Link to="/dashboard" state={{ fromHome: true }}>
        Dashboard
      </Link>

      {/* Replace history (can't go back) */}
      <Link to="/login" replace>
        Login
      </Link>

      {/* Link with custom styling */}
      <Link
        to="/products"
        style={{ color: "blue", textDecoration: "none" }}
        className="nav-link"
      >
        Products
      </Link>
    </nav>
  );
}
```

### NavLink Component - Active Links

```jsx
import { NavLink } from "react-router-dom";

function Navigation() {
  return (
    <nav>
      {/* NavLink adds 'active' class when current route matches */}
      <NavLink to="/">Home</NavLink>
      {/* Renders: <a href="/" class="active">Home</a> when on home page */}

      {/* Custom active class name */}
      <NavLink
        to="/about"
        className={({ isActive }) => (isActive ? "active-link" : "normal-link")}
      >
        About
      </NavLink>

      {/* Custom active style */}
      <NavLink
        to="/contact"
        style={({ isActive }) => ({
          color: isActive ? "red" : "blue",
          fontWeight: isActive ? "bold" : "normal",
        })}
      >
        Contact
      </NavLink>

      {/* End prop - only active on exact path */}
      <NavLink to="/products" end>
        Products
      </NavLink>
      {/* Active only on /products, not /products/123 */}
    </nav>
  );
}
```

### Navigate Component - Redirects

```jsx
import { Navigate } from "react-router-dom";

function ProtectedRoute({ isLoggedIn, children }) {
  // Redirect to login if not authenticated
  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }
  return children;
}

function LoginPage() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  if (isLoggedIn) {
    // Redirect to dashboard after login
    return <Navigate to="/dashboard" />;
  }

  return <form onSubmit={() => setIsLoggedIn(true)}>...</form>;
}
```

---

## URL Parameters

### useParams - Read URL Parameters

```jsx
import { useParams } from 'react-router-dom';

// Route definition
<Route path="/users/:userId" element={<UserProfile />} />
<Route path="/products/:category/:productId" element={<ProductDetail />} />

function UserProfile() {
  // Get parameters from URL
  const { userId } = useParams();
  // If URL is /users/123, userId = '123'

  return <div>User ID: {userId}</div>;
}

function ProductDetail() {
  // Multiple parameters
  const { category, productId } = useParams();
  // URL: /products/electronics/456
  // category = 'electronics', productId = '456'

  return (
    <div>
      <p>Category: {category}</p>
      <p>Product ID: {productId}</p>
    </div>
  );
}
```

### useParams with Data Fetching

```jsx
import { useParams } from "react-router-dom";
import { useQuery } from "@tanstack/react-query";

function UserProfile() {
  const { userId } = useParams();

  const { data: user, isLoading } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetchUser(userId),
  });

  if (isLoading) return <div>Loading user {userId}...</div>;

  return <div>{user.name}</div>;
}
```

### Optional Parameters

```jsx
// Route with optional parameter
<Route path="/products/:productId?" element={<Products />} />;
// Matches both /products and /products/123

function Products() {
  const { productId } = useParams();

  if (productId) {
    return <ProductDetail id={productId} />;
  }
  return <ProductList />;
}
```

---

## Nested Routes

### Basic Nested Routes

```jsx
// App.jsx
import { Routes, Route } from "react-router-dom";
import DashboardLayout from "./layouts/DashboardLayout";
import Overview from "./pages/Overview";
import Settings from "./pages/Settings";
import Profile from "./pages/Profile";

function App() {
  return (
    <Routes>
      {/* Parent route with layout */}
      <Route path="/dashboard" element={<DashboardLayout />}>
        {/* Child routes - render inside DashboardLayout */}
        <Route index element={<Overview />} /> // /dashboard
        <Route path="settings" element={<Settings />} /> // /dashboard/settings
        <Route path="profile" element={<Profile />} /> // /dashboard/profile
      </Route>
    </Routes>
  );
}
```

### Layout Component with Outlet

```jsx
// layouts/DashboardLayout.jsx
import { Outlet, NavLink } from "react-router-dom";

function DashboardLayout() {
  return (
    <div>
      {/* Sidebar navigation */}
      <nav>
        <NavLink to="/dashboard">Overview</NavLink>
        <NavLink to="/dashboard/settings">Settings</NavLink>
        <NavLink to="/dashboard/profile">Profile</NavLink>
      </nav>

      {/* Main content - child routes render here */}
      <main>
        <Outlet /> {/* This is where child components go */}
      </main>
    </div>
  );
}
```

### Deep Nesting

```jsx
// App.jsx
<Routes>
  <Route path="/shop" element={<ShopLayout />}>
    <Route index element={<ShopHome />} />

    <Route path="products" element={<ProductsLayout />}>
      <Route index element={<ProductList />} />
      <Route path=":productId" element={<ProductDetail />}>
        <Route path="reviews" element={<ProductReviews />} />
        <Route path="specs" element={<ProductSpecs />} />
      </Route>
    </Route>

    <Route path="cart" element={<Cart />} />
  </Route>
</Routes>;

// ShopLayout.jsx
function ShopLayout() {
  return (
    <div>
      <header>Shop Header</header>
      <Outlet /> // Renders ShopHome, ProductsLayout, or Cart
    </div>
  );
}

// ProductsLayout.jsx
function ProductsLayout() {
  return (
    <div>
      <aside>Product Filters</aside>
      <main>
        <Outlet /> // Renders ProductList or ProductDetail
      </main>
    </div>
  );
}

// ProductDetail.jsx
function ProductDetail() {
  const { productId } = useParams();

  return (
    <div>
      <h1>Product {productId}</h1>
      <nav>
        <NavLink to="reviews">Reviews</NavLink>
        <NavLink to="specs">Specs</NavLink>
      </nav>
      <Outlet /> // Renders ProductReviews or ProductSpecs
    </div>
  );
}
```

---

## Programmatic Navigation

### useNavigate - Navigate Programmatically

```jsx
import { useNavigate } from "react-router-dom";

function LoginForm() {
  const navigate = useNavigate();

  // Basic navigation
  const goToHome = () => {
    navigate("/");
  };

  // Go back/forward
  const goBack = () => {
    navigate(-1); // Go back one page
  };

  const goForward = () => {
    navigate(1); // Go forward one page
  };

  // Replace current history (can't go back)
  const replacePage = () => {
    navigate("/welcome", { replace: true });
  };

  // With state (data passed to next page)
  const navigateWithState = () => {
    navigate("/success", {
      state: {
        message: "Login successful!",
        userId: 123,
      },
    });
  };

  // Relative navigation
  const viewDetails = (id) => {
    navigate(`details/${id}`); // Goes to /products/details/123
  };

  // Form submission example
  const handleSubmit = async (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);

    try {
      await login(formData);
      navigate("/dashboard"); // Redirect after success
    } catch (error) {
      console.error("Login failed");
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" />
      <input name="password" />
      <button type="submit">Login</button>
      <button type="button" onClick={goBack}>
        Back
      </button>
    </form>
  );
}
```

### useNavigate with Query Parameters

```jsx
function SearchForm() {
  const navigate = useNavigate();

  const handleSearch = (searchTerm) => {
    // Navigate with query parameter
    navigate(`/products?search=${searchTerm}`);
  };

  const handleFilter = (category, sort) => {
    // Multiple query parameters
    navigate(`/products?category=${category}&sort=${sort}`);
  };

  return (
    <div>
      <input onChange={(e) => handleSearch(e.target.value)} />
      <select onChange={(e) => handleFilter(e.target.value, "asc")}>
        <option value="electronics">Electronics</option>
        <option value="clothing">Clothing</option>
      </select>
    </div>
  );
}
```

---

## Query Parameters

### useSearchParams - Read/Write Query Strings

```jsx
import { useSearchParams } from "react-router-dom";

function ProductsPage() {
  const [searchParams, setSearchParams] = useSearchParams();

  // Reading query parameters
  const category = searchParams.get("category"); // ?category=electronics
  const sort = searchParams.get("sort"); // ?sort=asc
  const page = searchParams.get("page") || "1"; // ?page=2

  // Check if parameter exists
  const hasFilter = searchParams.has("filter");

  // Get all parameters as object
  const allParams = Object.fromEntries(searchParams);
  // { category: 'electronics', sort: 'asc', page: '2' }

  // Setting query parameters
  const setCategory = (cat) => {
    setSearchParams({ category: cat });
  };

  // Merge with existing
  const setPage = (newPage) => {
    setSearchParams((prev) => {
      prev.set("page", newPage);
      return prev;
    });
  };

  // Remove parameter
  const clearFilter = () => {
    setSearchParams((prev) => {
      prev.delete("category");
      return prev;
    });
  };

  // Reset all parameters
  const resetFilters = () => {
    setSearchParams({});
  };

  return (
    <div>
      <div>Current category: {category || "All"}</div>
      <div>Current page: {page}</div>

      <button onClick={() => setCategory("electronics")}>Electronics</button>
      <button onClick={() => setCategory("clothing")}>Clothing</button>
      <button onClick={clearFilter}>Clear Category</button>
      <button onClick={() => setPage(Number(page) + 1)}>Next Page</button>
      <button onClick={resetFilters}>Reset All</button>
    </div>
  );
}
```

### Real-world Filter Component

```jsx
function ProductFilters() {
  const [searchParams, setSearchParams] = useSearchParams();

  const filters = {
    category: searchParams.get("category") || "",
    minPrice: searchParams.get("minPrice") || "",
    maxPrice: searchParams.get("maxPrice") || "",
    sort: searchParams.get("sort") || "name",
    inStock: searchParams.get("inStock") === "true",
  };

  const updateFilter = (key, value) => {
    setSearchParams((prev) => {
      if (value) {
        prev.set(key, value);
      } else {
        prev.delete(key);
      }
      return prev;
    });
  };

  const applyFilters = () => {
    // All filters are already in URL
    // The component reading them will react to changes
  };

  return (
    <div>
      <select
        value={filters.category}
        onChange={(e) => updateFilter("category", e.target.value)}
      >
        <option value="">All Categories</option>
        <option value="electronics">Electronics</option>
        <option value="clothing">Clothing</option>
      </select>

      <input
        type="number"
        placeholder="Min Price"
        value={filters.minPrice}
        onChange={(e) => updateFilter("minPrice", e.target.value)}
      />

      <input
        type="number"
        placeholder="Max Price"
        value={filters.maxPrice}
        onChange={(e) => updateFilter("maxPrice", e.target.value)}
      />

      <label>
        <input
          type="checkbox"
          checked={filters.inStock}
          onChange={(e) => updateFilter("inStock", e.target.checked)}
        />
        In Stock Only
      </label>

      <button onClick={() => setSearchParams({})}>Clear All Filters</button>
    </div>
  );
}
```

---

## Protected Routes

### Basic Protected Route

```jsx
import { Navigate } from "react-router-dom";

function ProtectedRoute({ children, isAuthenticated }) {
  if (!isAuthenticated) {
    // Redirect to login if not authenticated
    return <Navigate to="/login" replace />;
  }

  return children;
}

// Usage
function App() {
  const { isAuthenticated } = useAuth();

  return (
    <Routes>
      <Route path="/login" element={<Login />} />

      <Route
        path="/dashboard"
        element={
          <ProtectedRoute isAuthenticated={isAuthenticated}>
            <Dashboard />
          </ProtectedRoute>
        }
      />

      <Route
        path="/profile"
        element={
          <ProtectedRoute isAuthenticated={isAuthenticated}>
            <Profile />
          </ProtectedRoute>
        }
      />
    </Routes>
  );
}
```

### Protected Route with Role Check

```jsx
function ProtectedRoute({ children, isAuthenticated, userRole, allowedRoles }) {
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  if (allowedRoles && !allowedRoles.includes(userRole)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return children;
}

// Usage
<Route
  path="/admin"
  element={
    <ProtectedRoute
      isAuthenticated={isAuthenticated}
      userRole={userRole}
      allowedRoles={["admin", "superadmin"]}
    >
      <AdminPanel />
    </ProtectedRoute>
  }
/>;
```

### Protected Route with Layout

```jsx
function App() {
  const { isAuthenticated } = useAuth();

  return (
    <Routes>
      {/* Public routes */}
      <Route path="/login" element={<Login />} />
      <Route path="/register" element={<Register />} />

      {/* Protected routes wrapper */}
      <Route element={<ProtectedLayout isAuthenticated={isAuthenticated} />}>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Route>

      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}

function ProtectedLayout({ isAuthenticated }) {
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return (
    <div>
      <nav>Protected Navigation</nav>
      <Outlet /> {/* Child routes render here */}
    </div>
  );
}
```

### Save Intended Destination

```jsx
import { useLocation, Navigate } from "react-router-dom";

function ProtectedRoute({ children, isAuthenticated }) {
  const location = useLocation();

  if (!isAuthenticated) {
    // Save the location they tried to access
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
}

function LoginPage() {
  const location = useLocation();
  const navigate = useNavigate();

  // Get the page they were trying to visit
  const from = location.state?.from?.pathname || "/dashboard";

  const handleLogin = async () => {
    await login();
    // Redirect them back to their intended destination
    navigate(from, { replace: true });
  };

  return (
    <form onSubmit={handleLogin}>
      <button type="submit">Login</button>
      <p>Please login to view: {from}</p>
    </form>
  );
}
```

---

## Route Layouts

### Layout Component Pattern

```jsx
// layouts/MainLayout.jsx
import { Outlet } from "react-router-dom";
import Header from "../components/Header";
import Footer from "../components/Footer";

function MainLayout() {
  return (
    <div>
      <Header />
      <main>
        <Outlet /> {/* Page content goes here */}
      </main>
      <Footer />
    </div>
  );
}

// layouts/AuthLayout.jsx
function AuthLayout() {
  return (
    <div className="auth-layout">
      <div className="auth-container">
        <Outlet />
      </div>
    </div>
  );
}

// App.jsx
function App() {
  return (
    <Routes>
      {/* Public routes with main layout */}
      <Route element={<MainLayout />}>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Route>

      {/* Auth routes with auth layout */}
      <Route element={<AuthLayout />}>
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
      </Route>

      {/* Protected routes with main layout */}
      <Route element={<ProtectedLayout />}>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
      </Route>
    </Routes>
  );
}
```

---

## Lazy Loading

### Route-based Code Splitting

```jsx
import { lazy, Suspense } from "react";
import { Routes, Route } from "react-router-dom";

// Lazy load components - loaded only when needed
const Home = lazy(() => import("./pages/Home"));
const About = lazy(() => import("./pages/About"));
const Dashboard = lazy(() => import("./pages/Dashboard"));
const Settings = lazy(() => import("./pages/Settings"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}
```

### Lazy Loading with Layouts

```jsx
const MainLayout = lazy(() => import("./layouts/MainLayout"));
const DashboardLayout = lazy(() => import("./layouts/DashboardLayout"));
const Home = lazy(() => import("./pages/Home"));
const About = lazy(() => import("./pages/About"));
const Dashboard = lazy(() => import("./pages/Dashboard"));

function App() {
  return (
    <Suspense fallback={<GlobalLoadingSpinner />}>
      <Routes>
        <Route element={<MainLayout />}>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
        </Route>

        <Route element={<DashboardLayout />}>
          <Route path="/dashboard" element={<Dashboard />} />
        </Route>
      </Routes>
    </Suspense>
  );
}
```

---

## Complete Examples

### Example 1: Blog with Nested Routes

```jsx
// App.jsx
import { Routes, Route } from "react-router-dom";
import BlogLayout from "./layouts/BlogLayout";
import PostLayout from "./layouts/PostLayout";
import Home from "./pages/Home";
import Posts from "./pages/Posts";
import PostDetail from "./pages/PostDetail";
import PostComments from "./pages/PostComments";
import PostAuthor from "./pages/PostAuthor";

function App() {
  return (
    <Routes>
      <Route element={<BlogLayout />}>
        <Route path="/" element={<Home />} />

        <Route path="posts" element={<Posts />} />

        <Route path="posts/:postId" element={<PostLayout />}>
          <Route index element={<PostDetail />} />
          <Route path="comments" element={<PostComments />} />
          <Route path="author" element={<PostAuthor />} />
        </Route>
      </Route>
    </Routes>
  );
}

// layouts/BlogLayout.jsx
import { Outlet, Link } from "react-router-dom";

function BlogLayout() {
  return (
    <div>
      <header>
        <Link to="/">My Blog</Link>
        <nav>
          <Link to="/posts">Posts</Link>
        </nav>
      </header>
      <Outlet />
      <footer>© 2024 My Blog</footer>
    </div>
  );
}

// layouts/PostLayout.jsx
import { Outlet, NavLink, useParams } from "react-router-dom";

function PostLayout() {
  const { postId } = useParams();

  return (
    <div>
      <nav>
        <NavLink to={`/posts/${postId}`}>Content</NavLink>
        <NavLink to={`/posts/${postId}/comments`}>Comments</NavLink>
        <NavLink to={`/posts/${postId}/author`}>Author</NavLink>
      </nav>
      <Outlet />
    </div>
  );
}
```

### Example 2: E-commerce with Filters

```jsx
// App.jsx
import { Routes, Route } from "react-router-dom";
import Products from "./pages/Products";
import ProductDetail from "./pages/ProductDetail";
import Cart from "./pages/Cart";
import Checkout from "./pages/Checkout";

function App() {
  return (
    <Routes>
      <Route path="/products" element={<Products />} />
      <Route path="/products/:id" element={<ProductDetail />} />
      <Route path="/cart" element={<Cart />} />
      <Route path="/checkout" element={<Checkout />} />
    </Routes>
  );
}

// pages/Products.jsx
import { useSearchParams, Link } from "react-router-dom";
import { useQuery } from "@tanstack/react-query";

function Products() {
  const [searchParams] = useSearchParams();

  const category = searchParams.get("category");
  const minPrice = searchParams.get("minPrice");
  const maxPrice = searchParams.get("maxPrice");
  const sort = searchParams.get("sort");

  const { data: products, isLoading } = useQuery({
    queryKey: ["products", { category, minPrice, maxPrice, sort }],
    queryFn: () => fetchProducts({ category, minPrice, maxPrice, sort }),
  });

  if (isLoading) return <div>Loading...</div>;

  return (
    <div>
      <ProductFilters />

      <div className="products-grid">
        {products.map((product) => (
          <Link to={`/products/${product.id}`} key={product.id}>
            <h3>{product.name}</h3>
            <p>${product.price}</p>
          </Link>
        ))}
      </div>
    </div>
  );
}
```

### Example 3: Authentication Flow

```jsx
// App.jsx
import { Routes, Route, Navigate } from "react-router-dom";
import { useAuth } from "./hooks/useAuth";
import Login from "./pages/Login";
import Register from "./pages/Register";
import Dashboard from "./pages/Dashboard";
import Profile from "./pages/Profile";
import Settings from "./pages/Settings";

function App() {
  const { isAuthenticated, user } = useAuth();

  return (
    <Routes>
      {/* Public routes */}
      <Route
        path="/login"
        element={!isAuthenticated ? <Login /> : <Navigate to="/dashboard" />}
      />
      <Route
        path="/register"
        element={!isAuthenticated ? <Register /> : <Navigate to="/dashboard" />}
      />

      {/* Protected routes */}
      <Route element={<ProtectedRoute isAuthenticated={isAuthenticated} />}>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Route>

      {/* Role-based routes */}
      <Route
        element={
          <ProtectedRoute
            isAuthenticated={isAuthenticated}
            allowedRoles={["admin"]}
            userRole={user?.role}
          />
        }
      >
        <Route path="/admin" element={<AdminPanel />} />
        <Route path="/admin/users" element={<UserManagement />} />
      </Route>

      {/* 404 */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

---

## API Reference

### Components

| Component     | Purpose                | Example                                  |
| ------------- | ---------------------- | ---------------------------------------- |
| BrowserRouter | Enables routing        | `<BrowserRouter><App /></BrowserRouter>` |
| Routes        | Container for routes   | `<Routes>...</Routes>`                   |
| Route         | Defines a route        | `<Route path="/" element={<Home />} />`  |
| Link          | Navigation link        | `<Link to="/about">About</Link>`         |
| NavLink       | Link with active state | `<NavLink to="/">Home</NavLink>`         |
| Navigate      | Redirect               | `<Navigate to="/login" />`               |
| Outlet        | Nested route renderer  | `<Outlet />`                             |

### Hooks

| Hook            | Purpose                 | Returns                           |
| --------------- | ----------------------- | --------------------------------- |
| useParams       | Get URL parameters      | `{ id: '123' }`                   |
| useSearchParams | Get/set query string    | `[searchParams, setSearchParams]` |
| useNavigate     | Programmatic navigation | `navigate()` function             |
| useLocation     | Get current location    | `{ pathname, search, state }`     |
| useMatch        | Match current route     | `match` object or null            |

### useLocation Details

```jsx
const location = useLocation();
console.log(location);
// {
//   pathname: '/products/123',
//   search: '?category=electronics',
//   hash: '#reviews',
//   state: { fromHome: true },
//   key: 'abc123'
// }
```

### useNavigate Options

```jsx
navigate("/dashboard"); // Go to dashboard
navigate(-1); // Go back
navigate(1); // Go forward
navigate("/login", { replace: true }); // Replace history
navigate("/success", { state: { data } }); // Pass state
```

## Troubleshooting

Problem 1: Routes not working

Solutions:

- Ensure BrowserRouter is wrapping your app
- Check Routes is used (not Switch in v6)
- Verify route paths are correct

Problem 2: Links not changing URL

Solutions:

- Check Link is inside Router context
- Ensure no other click handlers interfering
- Verify 'to' prop is correct

Problem 3: Nested routes not rendering

Solutions:

- Add Outlet component in parent route
- Check child routes are nested correctly
- Verify parent route renders Outlet

Problem 4: useParams returning undefined

Solutions:

- Check route definition has parameter
- Verify component is rendered by Route
- Ensure parameter name matches

## Useful Resources

- React Router Docs: https://reactrouter.com/
- GitHub Repository: https://github.com/remix-run/react-router
- Examples: https://reactrouter.com/en/main/start/examples

---

Created for beginners learning React Router

```

```
