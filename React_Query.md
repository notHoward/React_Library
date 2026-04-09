````markdown
# React Query - Complete Beginner's Guide

A comprehensive from-scratch guide to TanStack Query (React Query).

## Table of Contents

1. [What is React Query](#what-is-react-query)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [useQuery - Reading Data](#usequery---reading-data)
5. [useMutation - Changing Data](#usemutation---changing-data)
6. [Query Keys](#query-keys)
7. [Query Functions](#query-functions)
8. [All Query Options](#all-query-options)
9. [All Mutation Options](#all-mutation-options)
10. [Query Invalidation](#query-invalidation)
11. [Parallel Queries](#parallel-queries)
12. [Dependent Queries](#dependent-queries)
13. [Infinite Queries](#infinite-queries)
14. [Complete Examples](#complete-examples)
15. [API Reference](#api-reference)

---

## What is React Query

React Query is a library that handles **fetching, caching, and updating data** in your React app.

**What React Query does for you:**

- Fetches data from APIs
- Caches the data so you don't fetch again
- Automatically refetches when data is stale
- Shows loading and error states
- Retries failed requests
- Updates data in the background

**Without React Query (plain React):**

```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("/api/users")
      .then((res) => res.json())
      .then((data) => {
        setUsers(data);
        setLoading(false);
      })
      .catch((err) => {
        setError(err);
        setLoading(false);
      });
  }, []);

  // Plus you need to handle refetching, caching, etc.
}
```
````

**With React Query:**

```jsx
function Users() {
  const { data, isLoading, error } = useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then((res) => res.json()),
  });
  // That's it! Caching, refetching, retries all handled!
}
```

## Installation

```bash
npm install @tanstack/react-query
```

## Basic Setup

### Step 1: Create QueryClient

```jsx
// main.jsx or App.jsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

// 1. Create a client
const queryClient = new QueryClient();

function App() {
  return (
    // 2. Provide the client to your app
    <QueryClientProvider client={queryClient}>
      <YourApp />
      {/* 3. Optional: Devtools for debugging */}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

### Step 2: Use useQuery in Components

```jsx
import { useQuery } from "@tanstack/react-query";

function UserList() {
  // 4. Use useQuery to fetch data
  const { data, isLoading, error } = useQuery({
    queryKey: ["users"], // Unique key for this query
    queryFn: fetchUsers, // Function that fetches data
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## useQuery - Reading Data

### The Basics

```jsx
import { useQuery } from "@tanstack/react-query";

function Todos() {
  // 1. Define your fetch function
  const fetchTodos = async () => {
    const response = await fetch("/api/todos");
    if (!response.ok) throw new Error("Failed to fetch");
    return response.json();
  };

  // 2. Use useQuery
  const query = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodos,
  });

  // 3. Destructure what you need
  const {
    data, // The actual data from your API
    isLoading, // True on first load
    isError, // True if there was an error
    error, // The error object
    isFetching, // True anytime it's fetching (including background)
    refetch, // Manually refetch data
    isSuccess, // True if data loaded successfully
    isIdle, // True if query hasn't run yet
    status, // 'idle', 'loading', 'error', 'success'
    fetchStatus, // 'idle', 'fetching', 'paused'
    dataUpdatedAt, // Timestamp of last data update
    errorUpdatedAt, // Timestamp of last error
  } = query;

  // 4. Handle different states
  if (isLoading) return <div>Loading todos...</div>;
  if (isError) return <div>Error: {error.message}</div>;

  return (
    <div>
      {isFetching && <div>Updating in background...</div>}
      <ul>
        {data.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>
      <button onClick={() => refetch()}>Refresh</button>
    </div>
  );
}
```

### Understanding Each useQuery Return Value

```jsx
const query = useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
});

// 1. data - The actual response from your API
console.log(query.data); // [{ id: 1, name: 'John' }, ...]

// 2. isLoading - First time loading (no data yet)
console.log(query.isLoading); // true/false

// 3. isFetching - Any fetching (including background updates)
console.log(query.isFetching); // true while fetching

// 4. isError - Something went wrong
console.log(query.isError); // true/false

// 5. error - The error object
console.log(query.error); // Error: Failed to fetch

// 6. isSuccess - Data loaded successfully
console.log(query.isSuccess); // true/false

// 7. refetch - Function to manually fetch again
query.refetch(); // Fetches fresh data

// 8. status - Current state
console.log(query.status); // 'idle', 'loading', 'error', 'success'

// 9. fetchStatus - Fetching status
console.log(query.fetchStatus); // 'idle', 'fetching', 'paused'
```

### Real-world Example with Parameters

```jsx
function UserProfile({ userId }) {
  const {
    data: user,
    isLoading,
    error,
  } = useQuery({
    // queryKey includes userId - different userId = different cache
    queryKey: ["user", userId],

    // queryFn receives the queryKey
    queryFn: async ({ queryKey }) => {
      const [_, id] = queryKey; // ['user', 123] -> id = 123
      const response = await fetch(`/api/users/${id}`);
      if (!response.ok) throw new Error("User not found");
      return response.json();
    },

    // Only run if userId exists
    enabled: !!userId,
  });

  if (!userId) return <div>Select a user</div>;
  if (isLoading) return <div>Loading user...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

---

## useMutation - Changing Data

### The Basics

```jsx
import { useMutation } from "@tanstack/react-query";

function AddTodo() {
  // 1. Define your mutation function
  const addTodo = async (newTodo) => {
    const response = await fetch("/api/todos", {
      method: "POST",
      body: JSON.stringify(newTodo),
      headers: { "Content-Type": "application/json" },
    });
    if (!response.ok) throw new Error("Failed to add");
    return response.json();
  };

  // 2. Use useMutation
  const mutation = useMutation({
    mutationFn: addTodo,
  });

  // 3. Destructure what you need
  const {
    mutate, // Function to call the mutation
    isLoading, // True if mutation is in progress
    isError, // True if mutation failed
    error, // The error object
    isSuccess, // True if mutation succeeded
    data, // Response data from mutation
    reset, // Reset mutation state
    status, // 'idle', 'loading', 'error', 'success'
  } = mutation;

  // 4. Handle submission
  const handleSubmit = (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);
    mutate({
      title: formData.get("title"),
      completed: false,
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="title" placeholder="Todo title" />
      <button type="submit" disabled={isLoading}>
        {isLoading ? "Adding..." : "Add Todo"}
      </button>

      {isSuccess && <div>Todo added successfully!</div>}
      {isError && <div>Error: {error.message}</div>}
    </form>
  );
}
```

### Understanding Each useMutation Return Value

```jsx
const mutation = useMutation({
  mutationFn: updateUser,
});

// 1. mutate - Function to trigger the mutation
mutation.mutate({ id: 1, name: "John" });

// 2. mutateAsync - Promise version of mutate
const result = await mutation.mutateAsync(userData);

// 3. isLoading - Mutation in progress
console.log(mutation.isLoading); // true/false

// 4. isError - Mutation failed
console.log(mutation.isError); // true/false

// 5. error - The error object
console.log(mutation.error); // Error: Failed to update

// 6. isSuccess - Mutation succeeded
console.log(mutation.isSuccess); // true/false

// 7. data - Response data from mutation
console.log(mutation.data); // { id: 1, name: 'John', updated: true }

// 8. reset - Clear mutation state
mutation.reset(); // Resets isError, isSuccess, etc.

// 9. status - Current state
console.log(mutation.status); // 'idle', 'loading', 'error', 'success'
```

### Mutation with Automatic Cache Update

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

function DeleteTodo({ todoId }) {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (id) => fetch(`/api/todos/${id}`, { method: "DELETE" }),

    // Called before mutation
    onMutate: async (deletedId) => {
      // Cancel any outgoing refetches
      await queryClient.cancelQueries({ queryKey: ["todos"] });

      // Snapshot previous value
      const previousTodos = queryClient.getQueryData(["todos"]);

      // Optimistically update cache
      queryClient.setQueryData(["todos"], (old) =>
        old.filter((todo) => todo.id !== deletedId),
      );

      return { previousTodos };
    },

    // If error, rollback
    onError: (err, deletedId, context) => {
      queryClient.setQueryData(["todos"], context.previousTodos);
    },

    // Always refetch after success or error
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  return <button onClick={() => mutation.mutate(todoId)}>Delete</button>;
}
```

---

## Query Keys

Query keys uniquely identify queries for caching.

### Basic Query Keys

```jsx
// Simple key - all todos
useQuery({ queryKey: ["todos"], queryFn: fetchTodos });

// Key with ID - single todo
useQuery({
  queryKey: ["todo", todoId],
  queryFn: () => fetchTodo(todoId),
});

// Key with object - filtered todos
useQuery({
  queryKey: ["todos", { status: "completed", userId: 1 }],
  queryFn: () => fetchTodos({ status: "completed", userId: 1 }),
});

// Nested keys
useQuery({
  queryKey: ["users", userId, "posts"],
  queryFn: () => fetchUserPosts(userId),
});

// Keys with dates
useQuery({
  queryKey: ["analytics", { startDate, endDate }],
  queryFn: () => fetchAnalytics(startDate, endDate),
});
```

### How Query Keys Work

```jsx
// These are different queries (different cache entries)
useQuery({ queryKey: ['todos'] })              // Key: ['todos']
useQuery({ queryKey: ['todos', 'active'] })    // Key: ['todos', 'active']
useQuery({ queryKey: ['todos', { id: 1 }] })   // Key: ['todos', { id: 1 }]
useQuery({ queryKey: ['todo', 1] })            // Key: ['todo', 1]

// Order matters
['todos', 'active']     // Different from
['active', 'todos']     // This is different!

// Objects are compared by keys, not order
{ status: 'active', userId: 1 }   // Same as
{ userId: 1, status: 'active' }   // This is the same!
```

### Invalidating Queries by Key

```jsx
const queryClient = useQueryClient();

// Invalidate all todos queries
queryClient.invalidateQueries({ queryKey: ["todos"] });

// Invalidate specific todo
queryClient.invalidateQueries({ queryKey: ["todo", 1] });

// Invalidate all queries that start with 'todos'
queryClient.invalidateQueries({ queryKey: ["todos"] });
// This invalidates: ['todos'], ['todos', 'active'], ['todos', { id: 1 }]

// Invalidate exact match only
queryClient.invalidateQueries({
  queryKey: ["todos"],
  exact: true, // Only invalidates ['todos'], not ['todos', 'active']
});

// Invalidate multiple keys
queryClient.invalidateQueries({ queryKey: ["users"] });
queryClient.invalidateQueries({ queryKey: ["posts"] });
```

---

## Query Functions

Query functions fetch data and must return a promise.

### Basic Query Functions

```jsx
// Simple fetch
const fetchUsers = async () => {
  const response = await fetch("/api/users");
  if (!response.ok) throw new Error("Network error");
  return response.json();
};

// With parameters from queryKey
const fetchUser = async ({ queryKey }) => {
  const [_, userId] = queryKey; // ['user', 123] -> userId = 123
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
};

// With custom error
const fetchData = async () => {
  try {
    const response = await fetch("/api/data");
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return response.json();
  } catch (error) {
    throw new Error(`Failed to fetch: ${error.message}`);
  }
};

// Using axios
import axios from "axios";

const fetchPosts = async () => {
  const { data } = await axios.get("/api/posts");
  return data;
};
```

### Query Function with AbortController

```jsx
const fetchData = async ({ signal }) => {
  const response = await fetch("/api/data", { signal });
  return response.json();
};

useQuery({
  queryKey: ["data"],
  queryFn: fetchData,
});
// React Query automatically cancels on unmount
```

### Query Function with Delay (Testing)

```jsx
const fetchSlowData = async () => {
  // Simulate slow network
  await new Promise((resolve) => setTimeout(resolve, 2000));

  const response = await fetch("/api/data");
  return response.json();
};
```

---

## All Query Options

### Complete useQuery Options

```jsx
const query = useQuery({
  // Required
  queryKey: ["todos"], // Unique key
  queryFn: fetchTodos, // Function that fetches data

  // Caching
  staleTime: 5 * 60 * 1000, // Data fresh for 5 minutes
  cacheTime: 10 * 60 * 1000, // Cache persists for 10 minutes
  keepPreviousData: true, // Keep old data while fetching new

  // Refetching
  refetchOnWindowFocus: true, // Refetch when window focuses
  refetchOnMount: true, // Refetch when component mounts
  refetchOnReconnect: true, // Refetch when reconnecting
  refetchInterval: 30000, // Refetch every 30 seconds
  refetchIntervalInBackground: false, // Don't refetch in background

  // Retry logic
  retry: 3, // Retry 3 times on failure
  retryDelay: (attempt) => Math.min(1000 * 2 ** attempt, 30000),

  // Conditional fetching
  enabled: true, // Auto-fetch if true

  // Initial data
  initialData: () => {
    return localStorage.getItem("todos");
  },
  initialDataUpdatedAt: () => Date.now(),

  // Selector (transform data)
  select: (data) => data.filter((todo) => todo.completed),

  // Callbacks
  onSuccess: (data) => {
    console.log("Data loaded:", data);
  },
  onError: (error) => {
    console.error("Error:", error);
  },
  onSettled: (data, error) => {
    console.log("Query completed");
  },

  // Suspense
  suspense: false,

  // Use error boundary
  useErrorBoundary: false,
});
```

### Understanding Each Option

```jsx
// 1. staleTime - How long until data is considered "stale"
// Data becomes stale after this time and will refetch in background
staleTime: 5000; // 5 seconds

// 2. cacheTime - How long to keep unused data in cache
// After this time, inactive queries are garbage collected
cacheTime: 300000; // 5 minutes

// 3. keepPreviousData - Keep old data while fetching new
keepPreviousData: true; // Good for pagination

// 4. refetchOnWindowFocus - Refetch when user returns to tab
refetchOnWindowFocus: true; // Keep data fresh

// 5. refetchInterval - Poll for updates
refetchInterval: 10000; // Check for new data every 10 seconds

// 6. retry - Automatic retries on failure
retry: 3; // Try 3 times before showing error

// 7. enabled - Only run query when condition is true
enabled: !!userId; // Only run if userId exists

// 8. select - Transform data (memoized)
select: (data) => data.map((user) => user.name);

// 9. initialData - Start with cached/placeholder data
initialData: () => {
  const cached = localStorage.getItem("data");
  return cached ? JSON.parse(cached) : [];
};
```

---

## All Mutation Options

### Complete useMutation Options

```jsx
const mutation = useMutation({
  // Required
  mutationFn: updateUser, // Function that performs mutation

  // Optional
  mutationKey: ["update-user"], // Key for this mutation

  // Callbacks
  onMutate: async (variables) => {
    // Called before mutation
    // Return context for rollback
    return { previousData };
  },

  onSuccess: (data, variables, context) => {
    // Called on success
    console.log("Success:", data);
  },

  onError: (error, variables, context) => {
    // Called on error
    console.error("Error:", error);
  },

  onSettled: (data, error, variables, context) => {
    // Called after success or error
    console.log("Mutation finished");
  },

  // Retry logic
  retry: 3,
  retryDelay: (attempt) => attempt * 1000,

  // Use error boundary
  useErrorBoundary: false,
});

// Trigger mutation
mutation.mutate(variables);

// Or with promise
try {
  const result = await mutation.mutateAsync(variables);
} catch (error) {
  // Handle error
}
```

---

## Query Invalidation

### Basic Invalidation

```jsx
const queryClient = useQueryClient();

// Invalidate and refetch
queryClient.invalidateQueries({ queryKey: ["todos"] });

// Invalidate multiple
queryClient.invalidateQueries({ queryKey: ["todos"] });
queryClient.invalidateQueries({ queryKey: ["users"] });

// Invalidate all queries
queryClient.invalidateQueries();
```

### Manual Cache Updates

```jsx
const queryClient = useQueryClient();

// Set query data manually
queryClient.setQueryData(["todos"], (oldData) => {
  return [...oldData, newTodo];
});

// Get query data
const todos = queryClient.getQueryData(["todos"]);

// Remove query from cache
queryClient.removeQueries({ queryKey: ["todos"] });

// Reset query to initial state
queryClient.resetQueries({ queryKey: ["todos"] });

// Refetch query
queryClient.refetchQueries({ queryKey: ["todos"] });
```

### Complete Example with CRUD

```jsx
function TodoApp() {
  const queryClient = useQueryClient();

  // Read todos
  const { data: todos } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodos,
  });

  // Create todo
  const createMutation = useMutation({
    mutationFn: createTodo,
    onSuccess: () => {
      // Refetch todos after creation
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  // Update todo
  const updateMutation = useMutation({
    mutationFn: updateTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  // Delete todo
  const deleteMutation = useMutation({
    mutationFn: deleteTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  return (
    <div>
      <button onClick={() => createMutation.mutate({ title: "New Todo" })}>
        Add Todo
      </button>

      {todos?.map((todo) => (
        <div key={todo.id}>
          <span>{todo.title}</span>
          <button onClick={() => updateMutation.mutate(todo)}>Update</button>
          <button onClick={() => deleteMutation.mutate(todo.id)}>Delete</button>
        </div>
      ))}
    </div>
  );
}
```

---

## Parallel Queries

### useQueries for Multiple Queries

```jsx
import { useQueries } from "@tanstack/react-query";

function Dashboard() {
  const results = useQueries({
    queries: [
      {
        queryKey: ["users"],
        queryFn: fetchUsers,
      },
      {
        queryKey: ["posts"],
        queryFn: fetchPosts,
      },
      {
        queryKey: ["comments"],
        queryFn: fetchComments,
      },
    ],
  });

  const [usersQuery, postsQuery, commentsQuery] = results;

  if (usersQuery.isLoading || postsQuery.isLoading || commentsQuery.isLoading) {
    return <div>Loading dashboard...</div>;
  }

  return (
    <div>
      <UsersList users={usersQuery.data} />
      <PostsList posts={postsQuery.data} />
      <CommentsList comments={commentsQuery.data} />
    </div>
  );
}
```

### Dynamic Parallel Queries

```jsx
function UserTodos({ userIds }) {
  const userQueries = useQueries({
    queries: userIds.map((userId) => ({
      queryKey: ["todos", userId],
      queryFn: () => fetchUserTodos(userId),
      enabled: !!userId,
    })),
  });

  const isLoading = userQueries.some((query) => query.isLoading);
  const isError = userQueries.some((query) => query.isError);

  if (isLoading) return <div>Loading todos...</div>;
  if (isError) return <div>Error loading todos</div>;

  const allTodos = userQueries.flatMap((query) => query.data);

  return (
    <div>
      {allTodos.map((todo) => (
        <div key={todo.id}>{todo.title}</div>
      ))}
    </div>
  );
}
```

---

## Dependent Queries

### Query That Depends on Another

```jsx
function UserProfile({ userId }) {
  // First query - get user
  const { data: user, isLoading: userLoading } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetchUser(userId),
    enabled: !!userId,
  });

  // Second query - depends on user
  const { data: posts, isLoading: postsLoading } = useQuery({
    queryKey: ["posts", user?.id],
    queryFn: () => fetchUserPosts(user.id),
    enabled: !!user, // Only run when user exists
  });

  if (userLoading) return <div>Loading user...</div>;
  if (postsLoading) return <div>Loading posts...</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

## Infinite Queries

### useInfiniteQuery for Pagination

```jsx
import { useInfiniteQuery } from "@tanstack/react-query";

function InfinitePosts() {
  const {
    data, // Array of pages
    fetchNextPage, // Load next page
    hasNextPage, // Is there more data?
    isFetchingNextPage, // Loading next page
    isLoading, // First load
    isError, // Error state
    error, // Error object
  } = useInfiniteQuery({
    queryKey: ["posts"],
    queryFn: ({ pageParam = 1 }) => fetchPosts(pageParam),
    getNextPageParam: (lastPage, allPages) => {
      // Return next page number, or undefined if no more
      return lastPage.hasMore ? lastPage.page + 1 : undefined;
    },
    initialPageParam: 1,
  });

  if (isLoading) return <div>Loading posts...</div>;
  if (isError) return <div>Error: {error.message}</div>;

  return (
    <div>
      {data.pages.map((page, i) => (
        <div key={i}>
          {page.posts.map((post) => (
            <div key={post.id}>
              <h3>{post.title}</h3>
              <p>{post.body}</p>
            </div>
          ))}
        </div>
      ))}

      <button
        onClick={() => fetchNextPage()}
        disabled={!hasNextPage || isFetchingNextPage}
      >
        {isFetchingNextPage
          ? "Loading more..."
          : hasNextPage
            ? "Load More"
            : "Nothing more to load"}
      </button>
    </div>
  );
}
```

---

## Complete Examples

### Example 1: Todo App

```jsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

// API functions
const fetchTodos = async () => {
  const res = await fetch("/api/todos");
  return res.json();
};

const addTodo = async (newTodo) => {
  const res = await fetch("/api/todos", {
    method: "POST",
    body: JSON.stringify(newTodo),
    headers: { "Content-Type": "application/json" },
  });
  return res.json();
};

const updateTodo = async ({ id, updates }) => {
  const res = await fetch(`/api/todos/${id}`, {
    method: "PATCH",
    body: JSON.stringify(updates),
    headers: { "Content-Type": "application/json" },
  });
  return res.json();
};

const deleteTodo = async (id) => {
  await fetch(`/api/todos/${id}`, { method: "DELETE" });
};

function TodoApp() {
  const queryClient = useQueryClient();

  // Get todos
  const { data: todos, isLoading } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodos,
  });

  // Add todo
  const addMutation = useMutation({
    mutationFn: addTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  // Update todo
  const updateMutation = useMutation({
    mutationFn: updateTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  // Delete todo
  const deleteMutation = useMutation({
    mutationFn: deleteTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  if (isLoading) return <div>Loading...</div>;

  return (
    <div>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          const formData = new FormData(e.target);
          addMutation.mutate({
            title: formData.get("title"),
            completed: false,
          });
          e.target.reset();
        }}
      >
        <input name="title" placeholder="New todo" />
        <button type="submit">Add</button>
      </form>

      {todos.map((todo) => (
        <div key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() =>
              updateMutation.mutate({
                id: todo.id,
                updates: { completed: !todo.completed },
              })
            }
          />
          <span
            style={{ textDecoration: todo.completed ? "line-through" : "none" }}
          >
            {todo.title}
          </span>
          <button onClick={() => deleteMutation.mutate(todo.id)}>Delete</button>
        </div>
      ))}
    </div>
  );
}
```

### Example 2: Search with Debounce

```jsx
import { useQuery } from "@tanstack/react-query";
import { useState, useDebounce } from "react";

function SearchUsers() {
  const [searchTerm, setSearchTerm] = useState("");
  const debouncedSearch = useDebounce(searchTerm, 500);

  const { data: users, isLoading } = useQuery({
    queryKey: ["users", debouncedSearch],
    queryFn: () =>
      fetch(`/api/users?search=${debouncedSearch}`).then((res) => res.json()),
    enabled: debouncedSearch.length > 0,
  });

  return (
    <div>
      <input
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search users..."
      />

      {isLoading && <div>Searching...</div>}

      {users && (
        <ul>
          {users.map((user) => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

### Example 3: Prefetching Data

```jsx
function UserList() {
  const queryClient = useQueryClient();

  const { data: users } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
  });

  const prefetchUser = (userId) => {
    queryClient.prefetchQuery({
      queryKey: ["user", userId],
      queryFn: () => fetchUser(userId),
      staleTime: 10000,
    });
  };

  return (
    <ul>
      {users?.map((user) => (
        <li key={user.id} onMouseEnter={() => prefetchUser(user.id)}>
          <Link to={`/users/${user.id}`}>{user.name}</Link>
        </li>
      ))}
    </ul>
  );
}
```

---

## API Reference

### useQuery Return Values

| Property       | Description                           | Type     |
| -------------- | ------------------------------------- | -------- |
| data           | Query result data                     | any      |
| isLoading      | First load in progress                | boolean  |
| isFetching     | Any fetch in progress                 | boolean  |
| isError        | Error occurred                        | boolean  |
| error          | Error object                          | Error    |
| isSuccess      | Query succeeded                       | boolean  |
| isIdle         | Query not yet executed                | boolean  |
| status         | 'idle', 'loading', 'error', 'success' | string   |
| fetchStatus    | 'idle', 'fetching', 'paused'          | string   |
| refetch        | Manual refetch function               | function |
| remove         | Remove query from cache               | function |
| dataUpdatedAt  | Timestamp of last data update         | number   |
| errorUpdatedAt | Timestamp of last error               | number   |

### useMutation Return Values

| Property    | Description                           | Type     |
| ----------- | ------------------------------------- | -------- |
| mutate      | Trigger mutation                      | function |
| mutateAsync | Promise version                       | function |
| isLoading   | Mutation in progress                  | boolean  |
| isError     | Error occurred                        | boolean  |
| error       | Error object                          | Error    |
| isSuccess   | Mutation succeeded                    | boolean  |
| isIdle      | Not yet executed                      | boolean  |
| status      | 'idle', 'loading', 'error', 'success' | string   |
| reset       | Reset mutation state                  | function |
| data        | Response data                         | any      |

### QueryClient Methods

| Method              | Description                    |
| ------------------- | ------------------------------ |
| getQueryData()      | Get cached query data          |
| setQueryData()      | Set query data manually        |
| invalidateQueries() | Mark queries as stale          |
| refetchQueries()    | Refetch queries                |
| removeQueries()     | Remove queries from cache      |
| resetQueries()      | Reset queries to initial state |
| prefetchQuery()     | Prefetch data into cache       |
| fetchQuery()        | Fetch and cache data           |
| cancelQueries()     | Cancel ongoing queries         |

## Troubleshooting

Problem 1: Data not updating after mutation

Solution: Invalidate queries

```jsx
onSuccess: () => {
  queryClient.invalidateQueries({ queryKey: ["todos"] });
};
```

Problem 2: Too many network requests

Solution: Increase staleTime

```jsx
staleTime: 5 * 60 * 1000; // 5 minutes
```

Problem 3: Stale data showing

Solution: Reduce staleTime or enable refetchOnWindowFocus

```jsx
staleTime: 0,
refetchOnWindowFocus: true
```

## Useful Resources

- React Query Docs: https://tanstack.com/query/latest
- Examples: https://tanstack.com/query/latest/docs/react/examples/react/auto-refetching
- GitHub: https://github.com/TanStack/query

---

Created for beginners learning React Query
