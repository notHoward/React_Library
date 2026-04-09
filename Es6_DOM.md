# React Error Boundary - Complete Beginner's Guide

A comprehensive from-scratch guide to error handling in React using react-error-boundary.

## Table of Contents

1. [What are Error Boundaries](#what-are-error-boundaries)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [ErrorBoundary Component](#errorboundary-component)
5. [Fallback Components](#fallback-components)
6. [Reset Functionality](#reset-functionality)
7. [useErrorBoundary Hook](#useerrorboundary-hook)
8. [Error Boundary with React Query](#error-boundary-with-react-query)
9. [Error Boundary with Suspense](#error-boundary-with-suspense)
10. [Logging Errors](#logging-errors)
11. [Complete Examples](#complete-examples)
12. [API Reference](#api-reference)

---

## What are Error Boundaries

Error Boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of crashing the whole app.

**What Error Boundaries catch:**

- ✅ Render errors
- ✅ Lifecycle method errors
- ✅ Constructor errors

**What Error Boundaries DON'T catch:**

- ❌ Event handlers (use try/catch)
- ❌ Asynchronous code (setTimeout, fetch)
- ❌ Server-side rendering errors
- ❌ Errors thrown in the error boundary itself

**Without Error Boundary:**

```jsx
function BuggyComponent() {
  throw new Error("💥 Component crashed!");
  return <div>Hello</div>;
}

function App() {
  return (
    <div>
      <BuggyComponent /> // 💥 Entire app crashes, white screen!
      <p>This won't render</p>
    </div>
  );
}
```

**With Error Boundary:**

```jsx
function BuggyComponent() {
  throw new Error("💥 Component crashed!");
  return <div>Hello</div>;
}

function App() {
  return (
    <ErrorBoundary fallback={<div>Something went wrong</div>}>
      <BuggyComponent /> // ❌ Only this component crashes
      <p>This still renders ✅</p>
    </ErrorBoundary>
  );
}
```

---

## Installation

### Basic Installation

```bash
npm install react-error-boundary
```

### For TypeScript Projects

```bash
npm install react-error-boundary
# Types are included in the package
```

### For Yarn

```bash
yarn add react-error-boundary
```

### For pnpm

```bash
pnpm add react-error-boundary
```

---

## Basic Setup

### Simplest Usage

```jsx
import { ErrorBoundary } from "react-error-boundary";

function BuggyCounter() {
  const [count, setCount] = useState(0);

  if (count === 5) {
    throw new Error("💥 Counter crashed at 5!");
  }

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}

function App() {
  return (
    <ErrorBoundary fallback={<div>Something went wrong</div>}>
      <BuggyCounter />
    </ErrorBoundary>
  );
}
```

### With Custom Fallback Component

```jsx
import { ErrorBoundary } from "react-error-boundary";

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="error-container">
      <h2>Something went wrong</h2>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => {
        // Reset app state here
        console.log("Resetting error boundary");
      }}
    >
      <BuggyComponent />
    </ErrorBoundary>
  );
}
```

---

## ErrorBoundary Component

### Props Overview

```jsx
<ErrorBoundary
  // REQUIRED: Fallback UI to show when error occurs
  fallback={<div>Error occurred</div>}
  // OR custom component
  FallbackComponent={ErrorFallback}
  // OR custom render function
  fallbackRender={({ error, resetErrorBoundary }) => (
    <div>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>Reset</button>
    </div>
  )}
  // OPTIONAL: Called when error is caught
  onError={(error, info) => {
    console.error("Error caught:", error);
    console.error("Component stack:", info.componentStack);
    // Send to logging service
    logErrorToService(error, info);
  }}
  // OPTIONAL: Called before reset
  onReset={(details) => {
    console.log("Resetting error boundary", details);
    // Reset app state
    resetAppState();
  }}
  // OPTIONAL: Keys to trigger reset
  resetKeys={[someState, anotherState]}
>
  {children}
</ErrorBoundary>
```

### Example with All Props

```jsx
import { ErrorBoundary } from "react-error-boundary";

function DetailedErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="error-boundary-fallback">
      <div className="error-icon">⚠️</div>
      <h2>Application Error</h2>
      <p>{error.message}</p>
      {error.stack && (
        <details>
          <summary>Technical details</summary>
          <pre>{error.stack}</pre>
        </details>
      )}
      <div className="error-actions">
        <button onClick={resetErrorBoundary}>Try Again</button>
        <button onClick={() => window.location.reload()}>Reload Page</button>
      </div>
    </div>
  );
}

function App() {
  const [userId, setUserId] = useState(1);

  const handleError = (error, info) => {
    // Log to error tracking service
    console.error("Error caught by boundary:", {
      error: error.message,
      stack: error.stack,
      componentStack: info.componentStack,
      userId,
      timestamp: new Date().toISOString(),
    });

    // Send to your error tracking service
    fetch("/api/log-error", {
      method: "POST",
      body: JSON.stringify({
        error: error.message,
        componentStack: info.componentStack,
        userId,
      }),
    });
  };

  const handleReset = () => {
    console.log("Error boundary reset");
    // Reset any app state that might have caused the error
    setUserId(1);
  };

  return (
    <ErrorBoundary
      FallbackComponent={DetailedErrorFallback}
      onError={handleError}
      onReset={handleReset}
      resetKeys={[userId]} // Resets when userId changes
    >
      <UserProfile userId={userId} />
      <button onClick={() => setUserId((prev) => prev + 1)}>Next User</button>
    </ErrorBoundary>
  );
}
```

---

## Fallback Components

### Basic Fallback Component

```jsx
function BasicFallback({ error, resetErrorBoundary }) {
  return (
    <div style={{ padding: "20px", textAlign: "center" }}>
      <h3>Something went wrong</h3>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

<ErrorBoundary FallbackComponent={BasicFallback}>
  <BuggyComponent />
</ErrorBoundary>;
```

### Professional Error Fallback

```jsx
import { useState } from "react";

function ProfessionalErrorFallback({ error, resetErrorBoundary }) {
  const [showDetails, setShowDetails] = useState(false);

  return (
    <div className="error-fallback-container">
      <div className="error-icon">🔧</div>
      <h2>Application Error</h2>
      <p>We're sorry, but something went wrong.</p>

      <div className="error-actions">
        <button onClick={resetErrorBoundary} className="primary-button">
          Try Again
        </button>
        <button
          onClick={() => (window.location.href = "/")}
          className="secondary-button"
        >
          Go to Homepage
        </button>
        <button
          onClick={() => setShowDetails(!showDetails)}
          className="text-button"
        >
          {showDetails ? "Hide" : "Show"} Technical Details
        </button>
      </div>

      {showDetails && (
        <div className="error-details">
          <p>
            <strong>Error:</strong> {error.message}
          </p>
          {error.stack && (
            <details>
              <summary>Stack Trace</summary>
              <pre>{error.stack}</pre>
            </details>
          )}
          <p className="support-message">
            If this problem persists, please contact support.
          </p>
        </div>
      )}
    </div>
  );
}
```

### Fallback Render Function

```jsx
<ErrorBoundary
  fallbackRender={({ error, resetErrorBoundary }) => (
    <div className="inline-error">
      <span className="error-emoji">⚠️</span>
      <span className="error-message">{error.message}</span>
      <button onClick={resetErrorBoundary} className="retry-button">
        Retry
      </button>
    </div>
  )}
>
  <Widget />
</ErrorBoundary>
```

---

## Reset Functionality

### Basic Reset

```jsx
import { ErrorBoundary } from "react-error-boundary";

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div>
      <p>Error: {error.message}</p>
      <button onClick={resetErrorBoundary}>Reset and Try Again</button>
    </div>
  );
}

function Counter() {
  const [count, setCount] = useState(0);

  if (count === 5) {
    throw new Error("Count reached 5!");
  }

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}

function App() {
  const [key, setKey] = useState(0);

  const handleReset = () => {
    // Increment key to force remount of children
    setKey((prev) => prev + 1);
  };

  return (
    <ErrorBoundary
      key={key} // Key change remounts children
      FallbackComponent={ErrorFallback}
      onReset={handleReset}
    >
      <Counter />
    </ErrorBoundary>
  );
}
```

### Reset Keys Pattern

```jsx
function UserDashboard({ userId }) {
  const [user, setUser] = useState(null);
  const [refreshKey, setRefreshKey] = useState(0);

  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);

  return (
    <ErrorBoundary
      key={`${userId}-${refreshKey}`}
      fallbackRender={({ resetErrorBoundary }) => (
        <div>
          <p>Failed to load user data</p>
          <button onClick={resetErrorBoundary}>Retry</button>
        </div>
      )}
      onReset={() => {
        setRefreshKey((prev) => prev + 1);
      }}
      resetKeys={[userId]} // Resets when userId changes
    >
      <UserProfile user={user} />
    </ErrorBoundary>
  );
}
```

---

## useErrorBoundary Hook

### Basic Usage

```jsx
import { useErrorBoundary } from "react-error-boundary";

function AsyncComponent() {
  const { showBoundary } = useErrorBoundary();

  const handleClick = async () => {
    try {
      const response = await fetch("/api/data");
      if (!response.ok) throw new Error("Failed to fetch");
      const data = await response.json();
      // Process data...
    } catch (error) {
      // Send error to the nearest error boundary
      showBoundary(error);
    }
  };

  return <button onClick={handleClick}>Fetch Data</button>;
}

function App() {
  return (
    <ErrorBoundary fallback={<div>Error occurred!</div>}>
      <AsyncComponent />
    </ErrorBoundary>
  );
}
```

### With Multiple Error Types

```jsx
import { useErrorBoundary } from "react-error-boundary";

function DataFetcher() {
  const { showBoundary } = useErrorBoundary();
  const [data, setData] = useState(null);

  const fetchData = async () => {
    try {
      const response = await fetch("/api/data");

      if (response.status === 404) {
        throw new Error("Data not found");
      }

      if (response.status === 403) {
        throw new Error("You don't have permission");
      }

      if (!response.ok) {
        throw new Error("Something went wrong");
      }

      const result = await response.json();
      setData(result);
    } catch (error) {
      showBoundary(error);
    }
  };

  useEffect(() => {
    fetchData();
  }, []);

  return <div>{data}</div>;
}
```

### Reset Function from Hook

```jsx
import { useErrorBoundary } from "react-error-boundary";

function ErrorAwareComponent() {
  const { showBoundary, resetBoundary } = useErrorBoundary();
  const [retryCount, setRetryCount] = useState(0);

  const handleAction = async () => {
    try {
      await riskyOperation();
    } catch (error) {
      showBoundary(error);
    }
  };

  const handleReset = () => {
    setRetryCount((prev) => prev + 1);
    resetBoundary();
  };

  return (
    <div>
      <button onClick={handleAction}>Do Action</button>
      {retryCount > 0 && <p>Retry count: {retryCount}</p>}
      <button onClick={handleReset}>Manual Reset</button>
    </div>
  );
}
```

---

## Error Boundary with React Query

### Setup

```bash
npm install @tanstack/react-query react-error-boundary
```

### Integration Example

```jsx
import {
  useQuery,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
import { ErrorBoundary } from "react-error-boundary";

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      suspense: true, // Enable Suspense for queries
      useErrorBoundary: true, // Send errors to error boundary
    },
  },
});

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div>
      <h3>Failed to load data</h3>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>Retry</button>
    </div>
  );
}

function UserList() {
  const { data } = useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then((res) => res.json()),
  });

  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

function App() {
  const [queryKey, setQueryKey] = useState(0);

  return (
    <QueryClientProvider client={queryClient}>
      <ErrorBoundary
        key={queryKey}
        FallbackComponent={ErrorFallback}
        onReset={() => {
          queryClient.refetchQueries(["users"]);
          setQueryKey((prev) => prev + 1);
        }}
      >
        <Suspense fallback={<div>Loading users...</div>}>
          <UserList />
        </Suspense>
      </ErrorBoundary>
    </QueryClientProvider>
  );
}
```

---

## Error Boundary with Suspense

### Combined Usage

```jsx
import { ErrorBoundary } from "react-error-boundary";
import { Suspense } from "react";

function Resource({ resource }) {
  const data = resource.read();
  return <div>{data}</div>;
}

function App() {
  const [resource, setResource] = useState(null);

  const loadData = () => {
    // Create resource that can throw a promise or error
    const newResource = createResource(fetchData());
    setResource(newResource);
  };

  return (
    <div>
      <button onClick={loadData}>Load Data</button>

      <ErrorBoundary fallback={<div>Failed to load data</div>}>
        <Suspense fallback={<div>Loading...</div>}>
          {resource && <Resource resource={resource} />}
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}

// Simple resource implementation
function createResource(promise) {
  let status = "pending";
  let result;

  const suspender = promise.then(
    (r) => {
      status = "success";
      result = r;
    },
    (e) => {
      status = "error";
      result = e;
    },
  );

  return {
    read() {
      if (status === "pending") throw suspender;
      if (status === "error") throw result;
      return result;
    },
  };
}
```

---

## Logging Errors

### Basic Error Logging

```jsx
import { ErrorBoundary } from "react-error-boundary";

function logError(error, info) {
  // Console logging
  console.error("Error caught by boundary:", error);
  console.error("Component stack:", info.componentStack);

  // Send to your error tracking service
  fetch("/api/log-error", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      error: error.message,
      stack: error.stack,
      componentStack: info.componentStack,
      url: window.location.href,
      userAgent: navigator.userAgent,
      timestamp: new Date().toISOString(),
    }),
  });
}

function App() {
  return (
    <ErrorBoundary
      fallback={<div>Something went wrong</div>}
      onError={logError}
    >
      <MyApp />
    </ErrorBoundary>
  );
}
```

### Integration with Error Tracking Services

```jsx
// Sentry integration
import * as Sentry from "@sentry/react";

function logErrorWithSentry(error, info) {
  Sentry.captureException(error, {
    contexts: {
      react: {
        componentStack: info.componentStack,
      },
    },
  });
}

<ErrorBoundary fallback={ErrorFallback} onError={logErrorWithSentry}>
  {children}
</ErrorBoundary>;
```

```jsx
// LogRocket integration
import LogRocket from "logrocket";

function logErrorWithLogRocket(error, info) {
  LogRocket.captureException(error, {
    extra: { componentStack: info.componentStack },
  });
}

<ErrorBoundary fallback={ErrorFallback} onError={logErrorWithLogRocket}>
  {children}
</ErrorBoundary>;
```

```jsx
// Custom logging service
const logErrorToService = async (error, info) => {
  try {
    await fetch("https://your-logging-service.com/api/errors", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        name: error.name,
        message: error.message,
        stack: error.stack,
        componentStack: info.componentStack,
        timestamp: Date.now(),
        url: window.location.href,
        user: getCurrentUser(), // Get from your auth context
      }),
    });
  } catch (e) {
    console.error("Failed to log error:", e);
  }
};
```

---

## Complete Examples

### Example 1: App with Global Error Boundary

```jsx
// App.jsx
import { ErrorBoundary } from "react-error-boundary";
import { BrowserRouter, Routes, Route } from "react-router-dom";

function GlobalErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="global-error-container">
      <div className="error-content">
        <h1>⚠️ Application Error</h1>
        <p>We're sorry, but something went wrong.</p>
        <div className="error-code">Error: {error.message}</div>
        <div className="error-actions">
          <button onClick={resetErrorBoundary} className="btn-primary">
            Try Again
          </button>
          <button
            onClick={() => window.location.reload()}
            className="btn-secondary"
          >
            Reload Page
          </button>
          <button
            onClick={() => (window.location.href = "/")}
            className="btn-link"
          >
            Go to Homepage
          </button>
        </div>
        <details className="error-details">
          <summary>Technical Details</summary>
          <pre>{error.stack}</pre>
        </details>
      </div>
    </div>
  );
}

function App() {
  const handleError = (error, info) => {
    console.error("Global error caught:", error, info);
    // Send to error tracking service
    sendToErrorTracking(error, info);
  };

  const handleReset = () => {
    console.log("Error boundary reset");
    // Reset any global state
    clearCache();
    resetStore();
  };

  return (
    <ErrorBoundary
      FallbackComponent={GlobalErrorFallback}
      onError={handleError}
      onReset={handleReset}
    >
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
        </Routes>
      </BrowserRouter>
    </ErrorBoundary>
  );
}
```

### Example 2: Feature-Specific Error Boundaries

```jsx
// Dashboard.jsx
import { ErrorBoundary } from "react-error-boundary";

function WidgetErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="widget-error">
      <span className="widget-error-icon">📊</span>
      <p>Widget failed to load</p>
      <button onClick={resetErrorBoundary}>Retry</button>
    </div>
  );
}

function ChartWidget() {
  // This component might throw errors
  const { data } = useChartData();
  return <Chart data={data} />;
}

function StatsWidget() {
  // This component might throw errors
  const { stats } = useStats();
  return <StatsDisplay stats={stats} />;
}

function Dashboard() {
  const [refreshKey, setRefreshKey] = useState(0);

  return (
    <div className="dashboard">
      <h1>Dashboard</h1>

      <div className="dashboard-grid">
        {/* Chart Widget with its own error boundary */}
        <ErrorBoundary
          key={`chart-${refreshKey}`}
          FallbackComponent={WidgetErrorFallback}
          onReset={() => setRefreshKey((prev) => prev + 1)}
        >
          <ChartWidget />
        </ErrorBoundary>

        {/* Stats Widget with its own error boundary */}
        <ErrorBoundary
          key={`stats-${refreshKey}`}
          FallbackComponent={WidgetErrorFallback}
          onReset={() => setRefreshKey((prev) => prev + 1)}
        >
          <StatsWidget />
        </ErrorBoundary>

        {/* News Widget with its own error boundary */}
        <ErrorBoundary fallback={<div>News feed unavailable</div>}>
          <NewsWidget />
        </ErrorBoundary>
      </div>
    </div>
  );
}
```

### Example 3: Form with Error Boundary

```jsx
// UserForm.jsx
import { ErrorBoundary } from "react-error-boundary";
import { useErrorBoundary } from "react-error-boundary";

function FormErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="form-error">
      <h4>Form Error</h4>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>Reset Form</button>
    </div>
  );
}

function UserForm() {
  const { showBoundary } = useErrorBoundary();
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    age: "",
  });
  const [isSubmitting, setIsSubmitting] = useState(false);

  const validateForm = (data) => {
    if (!data.name) throw new Error("Name is required");
    if (!data.email) throw new Error("Email is required");
    if (!data.email.includes("@")) throw new Error("Invalid email format");
    if (data.age && (data.age < 0 || data.age > 150)) {
      throw new Error("Age must be between 0 and 150");
    }
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsSubmitting(true);

    try {
      // Validate form data (throws errors)
      validateForm(formData);

      // Submit to API
      const response = await fetch("/api/users", {
        method: "POST",
        body: JSON.stringify(formData),
        headers: { "Content-Type": "application/json" },
      });

      if (!response.ok) {
        throw new Error("Failed to create user");
      }

      // Success
      alert("User created successfully!");
      setFormData({ name: "", email: "", age: "" });
    } catch (error) {
      // Send error to error boundary
      showBoundary(error);
    } finally {
      setIsSubmitting(false);
    }
  };

  const handleChange = (e) => {
    setFormData((prev) => ({
      ...prev,
      [e.target.name]: e.target.value,
    }));
  };

  return (
    <form onSubmit={handleSubmit}>
      <div className="form-group">
        <label>Name *</label>
        <input
          name="name"
          value={formData.name}
          onChange={handleChange}
          required
        />
      </div>

      <div className="form-group">
        <label>Email *</label>
        <input
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          required
        />
      </div>

      <div className="form-group">
        <label>Age</label>
        <input
          name="age"
          type="number"
          value={formData.age}
          onChange={handleChange}
        />
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Creating..." : "Create User"}
      </button>
    </form>
  );
}

function UserFormWithBoundary() {
  const [formKey, setFormKey] = useState(0);

  const handleReset = () => {
    setFormKey((prev) => prev + 1);
  };

  return (
    <ErrorBoundary
      key={formKey}
      FallbackComponent={FormErrorFallback}
      onReset={handleReset}
    >
      <UserForm />
    </ErrorBoundary>
  );
}
```

### Example 4: Async Data Loading with Error Boundary

```jsx
// AsyncDataLoader.jsx
import { ErrorBoundary, useErrorBoundary } from "react-error-boundary";
import { Suspense } from "react";

function AsyncErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="async-error">
      <h3>Failed to load data</h3>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>Retry</button>
    </div>
  );
}

function LoadingSpinner() {
  return <div className="spinner">Loading...</div>;
}

// Custom hook for async data with error handling
function useAsyncData(fetchFn, deps = []) {
  const { showBoundary } = useErrorBoundary();
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let mounted = true;

    const loadData = async () => {
      try {
        setLoading(true);
        const result = await fetchFn();
        if (mounted) {
          setData(result);
          setLoading(false);
        }
      } catch (error) {
        if (mounted) {
          showBoundary(error);
        }
      }
    };

    loadData();

    return () => {
      mounted = false;
    };
  }, deps);

  return { data, loading };
}

function UserProfile({ userId }) {
  const { data: user, loading } = useAsyncData(
    () => fetch(`/api/users/${userId}`).then((res) => res.json()),
    [userId],
  );

  if (loading) return <LoadingSpinner />;

  return (
    <div className="user-profile">
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Member since: {new Date(user.createdAt).toLocaleDateString()}</p>
    </div>
  );
}

function UserDashboard() {
  const [userId, setUserId] = useState(1);
  const [refreshKey, setRefreshKey] = useState(0);

  return (
    <div className="user-dashboard">
      <div className="controls">
        <button onClick={() => setUserId((prev) => Math.max(1, prev - 1))}>
          Previous
        </button>
        <span>User ID: {userId}</span>
        <button onClick={() => setUserId((prev) => prev + 1)}>Next</button>
        <button onClick={() => setRefreshKey((prev) => prev + 1)}>
          Refresh
        </button>
      </div>

      <ErrorBoundary
        key={`${userId}-${refreshKey}`}
        FallbackComponent={AsyncErrorFallback}
        onReset={() => {
          console.log("Resetting for user:", userId);
        }}
        resetKeys={[userId]}
      >
        <Suspense fallback={<LoadingSpinner />}>
          <UserProfile userId={userId} />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

---

## API Reference

### ErrorBoundary Component Props

| Prop                | Type          | Description                 |
| ------------------- | ------------- | --------------------------- |
| `fallback`          | ReactNode     | Fallback UI to render       |
| `FallbackComponent` | ComponentType | Custom fallback component   |
| `fallbackRender`    | Function      | Render prop function        |
| `onError`           | Function      | Called when error is caught |
| `onReset`           | Function      | Called before reset         |
| `resetKeys`         | Array         | Values that trigger reset   |

### useErrorBoundary Hook Returns

| Property        | Type     | Description                     |
| --------------- | -------- | ------------------------------- |
| `showBoundary`  | Function | Manually trigger error boundary |
| `resetBoundary` | Function | Manually reset boundary         |

### Fallback Component Props

| Prop                 | Type     | Description             |
| -------------------- | -------- | ----------------------- |
| `error`              | Error    | The caught error object |
| `resetErrorBoundary` | Function | Reset the boundary      |

---

## Troubleshooting

**Problem 1:** Error boundary not catching errors

**Solutions:**

- Error boundaries only catch render errors
- Use try/catch for event handlers
- Use `showBoundary` from `useErrorBoundary` for async errors

**Problem 2:** Component not resetting

**Solutions:**

- Ensure `resetErrorBoundary` is called
- Use `key` prop to force remount
- Check `onReset` implementation

**Problem 3:** Multiple error boundaries interfering

**Solutions:**

- Place boundaries at appropriate levels
- Use different keys for different boundaries
- Reset only the affected boundary

---

## Useful Resources

- React Error Boundary Docs: https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary
- react-error-boundary GitHub: https://github.com/bvaughn/react-error-boundary
- React Query Error Handling: https://tanstack.com/query/latest/docs/react/guides/error-handling

---

Created for beginners learning React Error Boundaries
