# React Query Complete Control Guide

A comprehensive guide to controlling every aspect of TanStack Query (React Query).

## Table of Contents
1. [Core Concepts](#core-concepts)
2. [Basic Setup](#basic-setup)
3. [useQuery](#usequery)
4. [useMutation](#usemutation)
5. [Query Keys](#query-keys)
6. [Query Functions](#query-functions)
7. [Dependent Queries](#dependent-queries)
8. [Parallel Queries](#parallel-queries)
9. [Pagination](#pagination)
10. [Infinite Queries](#infinite-queries)
11. [Caching](#caching)
12. [Background Updates](#background-updates)
13. [Error Handling](#error-handling)
14. [Devtools](#devtools)
15. [Common Patterns](#common-patterns)
16. [Troubleshooting](#troubleshooting)

---

## Core Concepts

React Query has 5 main parts:

```jsx
QueryClient           // 1. Manages cache and queries
QueryClientProvider   // 2. Provides client to app
useQuery              // 3. Hook for fetching data
useMutation           // 4. Hook for creating/updating/deleting
Query Keys            // 5. Unique identifiers for queries
```

React Query Flow:

```
Component → useQuery → Fetch Data → Cache → Re-render
                ↓
         Background Refetch
```

---

## Basic Setup

Step 1: Install React Query

```bash
npm install @tanstack/react-query
```

Step 2: Create Query Client

```jsx
// main.jsx or index.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import App from './App';

// Create a client
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 60 * 1000, // 1 minute
      cacheTime: 5 * 60 * 1000, // 5 minutes
      retry: 1,
      refetchOnWindowFocus: false,
    },
  },
});

ReactDOM.createRoot(document.getElementById('root')).render(
  <QueryClientProvider client={queryClient}>
    <App />
    <ReactQueryDevtools initialIsOpen={false} />
  </QueryClientProvider>
);
```

---

## useQuery

The useQuery hook is for fetching and caching data.

### Basic Query

```jsx
import { useQuery } from '@tanstack/react-query';
import { fetchUsers } from './api';

function UsersList() {
  const {
    data,
    isLoading,
    isError,
    error,
    isFetching,
    refetch
  } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers
  });

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error: {error.message}</div>;

  return (
    <div>
      <button onClick={() => refetch()}>Refetch</button>
      {isFetching && <div>Updating...</div>}
      <ul>
        {data.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Query with Parameters

```jsx
function UserDetail({ userId }) {
  const { data, isLoading, error } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUserById(userId),
    enabled: !!userId // Only run if userId exists
  });

  if (isLoading) return <div>Loading user...</div>;
  if (error) return <div>Error loading user</div>;

  return <div>{data.name}</div>;
}
```

### Query Configuration Options

```jsx
const { data } = useQuery({
  queryKey: ['posts'],
  queryFn: fetchPosts,

  // Caching
  staleTime: 5 * 60 * 1000,     // Data fresh for 5 minutes
  cacheTime: 10 * 60 * 1000,    // Cache persists for 10 minutes
  keepPreviousData: true,        // Keep old data while fetching new

  // Refetching
  refetchOnWindowFocus: true,    // Refetch when window focuses
  refetchOnMount: true,           // Refetch when component mounts
  refetchOnReconnect: true,       // Refetch when reconnecting
  refetchInterval: 30 * 1000,     // Refetch every 30 seconds
  refetchIntervalInBackground: false, // Don't refetch in background

  // Retry logic
  retry: 3,                       // Retry 3 times on failure
  retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),

  // Conditional fetching
  enabled: true,                  // Auto-fetch if true

  // Initial data
  initialData: () => {
    return { id: 1, name: 'Default' };
  },

  // Callbacks
  onSuccess: (data) => {
    console.log('Data fetched successfully', data);
  },
  onError: (error) => {
    console.error('Error fetching data', error);
  },
  onSettled: (data, error) => {
    console.log('Fetch completed', { data, error });
  },

  // Selector
  select: (data) => data.filter(item => item.active),
});
```

---

## useMutation

The useMutation hook is for creating, updating, and deleting data.

### Basic Mutation

```jsx
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { createUser } from './api';

function CreateUserForm() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: createUser,
    onSuccess: (data) => {
      // Invalidate and refetch users list
      queryClient.invalidateQueries({ queryKey: ['users'] });
      console.log('User created:', data);
    },
    onError: (error) => {
      console.error('Error creating user:', error);
    },
  });

  const handleSubmit = (userData) => {
    mutation.mutate(userData);
  };

  return (
    <div>
      <form onSubmit={(e) => {
        e.preventDefault();
        handleSubmit({ name: e.target.name.value });
      }}>
        <input name="name" />
        <button type="submit" disabled={mutation.isLoading}>
          {mutation.isLoading ? 'Creating...' : 'Create User'}
        </button>
      </form>
      {mutation.isError && <div>Error: {mutation.error.message}</div>}
      {mutation.isSuccess && <div>User created successfully!</div>}
    </div>
  );
}
```

### Mutation with Optimistic Updates

```jsx
function TodoItem({ todo }) {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (updatedTodo) => updateTodo(updatedTodo),

    // Optimistic update
    onMutate: async (updatedTodo) => {
      // Cancel any outgoing refetches
      await queryClient.cancelQueries({ queryKey: ['todos'] });

      // Snapshot previous value
      const previousTodos = queryClient.getQueryData(['todos']);

      // Optimistically update cache
      queryClient.setQueryData(['todos'], (old) =>
        old.map(t => t.id === updatedTodo.id ? updatedTodo : t)
      );

      // Return context with snapshot
      return { previousTodos };
    },

    // If mutation fails, rollback
    onError: (err, updatedTodo, context) => {
      queryClient.setQueryData(['todos'], context.previousTodos);
    },

    // Always refetch after error or success
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });

  const handleToggle = () => {
    mutation.mutate({ ...todo, completed: !todo.completed });
  };

  return (
    <div>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={handleToggle}
      />
      <span>{todo.text}</span>
    </div>
  );
}
```

### Mutation with Success/Error Side Effects

```jsx
function LoginForm() {
  const navigate = useNavigate();

  const loginMutation = useMutation({
    mutationFn: (credentials) => api.login(credentials),
    onSuccess: (data) => {
      // Store token
      localStorage.setItem('token', data.token);
      // Redirect to dashboard
      navigate('/dashboard');
      // Show success toast
      toast.success('Login successful!');
    },
    onError: (error) => {
      // Show error message
      toast.error(error.response?.data?.message || 'Login failed');
    },
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);
    loginMutation.mutate({
      email: formData.get('email'),
      password: formData.get('password'),
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" type="email" required />
      <input name="password" type="password" required />
      <button type="submit" disabled={loginMutation.isLoading}>
        {loginMutation.isLoading ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}
```

---

## Query Keys

Query keys uniquely identify queries for caching and refetching.

### Basic Query Keys

```jsx
// Simple key
useQuery({ queryKey: ['todos'], queryFn: fetchTodos })

// Key with parameters
useQuery({
  queryKey: ['todo', todoId],
  queryFn: () => fetchTodoById(todoId)
})

// Multiple parameters
useQuery({
  queryKey: ['posts', { author: userId, status: 'published' }],
  queryFn: () => fetchPostsByAuthor(userId, 'published')
})

// Hierarchical keys
useQuery({
  queryKey: ['users', userId, 'posts'],
  queryFn: () => fetchUserPosts(userId)
})
```

### Invalidating Queries

```jsx
const queryClient = useQueryClient();

// Invalidate single query
queryClient.invalidateQueries({ queryKey: ['users'] });

// Invalidate all queries with 'users' prefix
queryClient.invalidateQueries({ queryKey: ['users'] });

// Invalidate exact match only
queryClient.invalidateQueries({
  queryKey: ['users', userId],
  exact: true
});

// Invalidate multiple queries
queryClient.invalidateQueries({
  queryKey: ['users'],
  refetchType: 'active' // Only refetch active queries
});

// Invalidate all queries
queryClient.invalidateQueries();
```

### Removing Queries from Cache

```jsx
// Remove single query
queryClient.removeQueries({ queryKey: ['users'] });

// Remove all queries
queryClient.removeQueries();

// Reset query data
queryClient.resetQueries({ queryKey: ['users'] });
```

---

## Query Functions

Query functions fetch data and must return a promise.

### Basic Query Function

```jsx
// Simple fetch
const fetchUsers = async () => {
  const response = await fetch('/api/users');
  if (!response.ok) throw new Error('Network response was not ok');
  return response.json();
};

// With error handling
const fetchUser = async ({ queryKey }) => {
  const [_, userId] = queryKey;
  try {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
    return await response.json();
  } catch (error) {
    throw new Error(`Failed to fetch user: ${error.message}`);
  }
};

// Using axios
import axios from 'axios';

const fetchPosts = async () => {
  const { data } = await axios.get('/api/posts');
  return data;
};
```

### Query Function with Parameters

```jsx
// Destructure queryKey
const fetchTodo = async ({ queryKey }) => {
  const [_, id] = queryKey;
  const response = await fetch(`/api/todos/${id}`);
  return response.json();
};

// With multiple params
const fetchFilteredTodos = async ({ queryKey }) => {
  const [_, filters] = queryKey;
  const queryString = new URLSearchParams(filters).toString();
  const response = await fetch(`/api/todos?${queryString}`);
  return response.json();
};

// Usage
useQuery({
  queryKey: ['todos', { status: 'active', priority: 'high' }],
  queryFn: fetchFilteredTodos
});
```

### Query Function with Cancelation

```jsx
const fetchData = async ({ signal }) => {
  const response = await fetch('/api/data', { signal });
  return response.json();
};

useQuery({
  queryKey: ['data'],
  queryFn: fetchData
});
```

---

## Dependent Queries

Dependent queries run after previous queries complete.

### Simple Dependency

```jsx
function UserProfile({ userId }) {
  // First query - get user
  const { data: user, isLoading: userLoading } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
  });

  // Second query - depends on user data
  const { data: posts, isLoading: postsLoading } = useQuery({
    queryKey: ['posts', user?.id],
    queryFn: () => fetchUserPosts(user.id),
    enabled: !!user, // Only run when user exists
  });

  if (userLoading) return <div>Loading user...</div>;
  if (postsLoading) return <div>Loading posts...</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Multiple Dependencies

```jsx
function Dashboard() {
  const [selectedUser, setSelectedUser] = useState(null);
  const [dateRange, setDateRange] = useState({ start: null, end: null });

  const { data: users } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers
  });

  const { data: userPosts } = useQuery({
    queryKey: ['posts', selectedUser?.id, dateRange],
    queryFn: () => fetchPostsByUser(selectedUser.id, dateRange),
    enabled: !!selectedUser && !!dateRange.start && !!dateRange.end
  });

  const { data: stats } = useQuery({
    queryKey: ['stats', userPosts],
    queryFn: () => calculateStats(userPosts),
    enabled: !!userPosts
  });

  return <div>{/* Component JSX */}</div>;
}
```

---

## Parallel Queries

Run multiple queries at the same time.

### useQueries Hook

```jsx
import { useQueries } from '@tanstack/react-query';

function Dashboard() {
  // Run multiple independent queries
  const results = useQueries({
    queries: [
      {
        queryKey: ['users'],
        queryFn: fetchUsers
      },
      {
        queryKey: ['posts'],
        queryFn: fetchPosts
      },
      {
        queryKey: ['comments'],
        queryFn: fetchComments
      }
    ]
  });

  const [usersQuery, postsQuery, commentsQuery] = results;

  if (usersQuery.isLoading || postsQuery.isLoading || commentsQuery.isLoading) {
    return <div>Loading dashboard...</div>;
  }

  if (usersQuery.isError || postsQuery.isError || commentsQuery.isError) {
    return <div>Error loading data</div>;
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
  // Run a query for each user ID
  const userQueries = useQueries({
    queries: userIds.map(userId => ({
      queryKey: ['todos', userId],
      queryFn: () => fetchUserTodos(userId),
      enabled: !!userId
    }))
  });

  const isLoading = userQueries.some(query => query.isLoading);
  const isError = userQueries.some(query => query.isError);

  if (isLoading) return <div>Loading todos...</div>;
  if (isError) return <div>Error loading todos</div>;

  const allTodos = userQueries.flatMap(query => query.data);

  return (
    <div>
      {allTodos.map(todo => (
        <div key={todo.id}>{todo.title}</div>
      ))}
    </div>
  );
}
```

---

## Pagination

Handle paginated data efficiently.

### Basic Pagination

```jsx
function PaginatedPosts() {
  const [page, setPage] = useState(1);

  const { data, isLoading, isFetching, isPreviousData } = useQuery({
    queryKey: ['posts', page],
    queryFn: () => fetchPosts(page),
    keepPreviousData: true // Keep old data while fetching new
  });

  return (
    <div>
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <>
          <ul>
            {data.posts.map(post => (
              <li key={post.id}>{post.title}</li>
            ))}
          </ul>

          <div>
            <button
              onClick={() => setPage(old => Math.max(old - 1, 1))}
              disabled={page === 1}
            >
              Previous
            </button>
            <span>Page {page} of {data.totalPages}</span>
            <button
              onClick={() => {
                if (!isPreviousData && page < data.totalPages) {
                  setPage(old => old + 1);
                }
              }}
              disabled={isPreviousData || page === data.totalPages}
            >
              Next
            </button>
          </div>

          {isFetching && <div>Fetching new page...</div>}
        </>
      )}
    </div>
  );
}
```

### Cursor-based Pagination

```jsx
function CursorPaginatedList() {
  const [cursor, setCursor] = useState(null);

  const { data, isLoading, isFetching } = useQuery({
    queryKey: ['items', cursor],
    queryFn: () => fetchItems(cursor),
    keepPreviousData: true
  });

  const loadMore = () => {
    if (data?.nextCursor) {
      setCursor(data.nextCursor);
    }
  };

  return (
    <div>
      <ul>
        {data?.items.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>

      {data?.hasMore && (
        <button onClick={loadMore} disabled={isFetching}>
          {isFetching ? 'Loading...' : 'Load More'}
        </button>
      )}
    </div>
  );
}
```

---

## Infinite Queries

Handle infinite scrolling data.

### Basic Infinite Query

```jsx
import { useInfiniteQuery } from '@tanstack/react-query';

function InfinitePosts() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
    isLoading,
    isError
  } = useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: ({ pageParam = 1 }) => fetchPosts(pageParam),
    getNextPageParam: (lastPage, allPages) => {
      // Return next page number or undefined
      return lastPage.hasMore ? lastPage.page + 1 : undefined;
    },
    initialPageParam: 1
  });

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error loading posts</div>;

  return (
    <div>
      {data.pages.map((page, i) => (
        <div key={i}>
          {page.posts.map(post => (
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
          ? 'Loading more...'
          : hasNextPage
          ? 'Load More'
          : 'Nothing more to load'}
      </button>
    </div>
  );
}
```

### Infinite Scroll with Intersection Observer

```jsx
import { useInfiniteQuery } from '@tanstack/react-query';
import { useRef, useEffect } from 'react';

function InfiniteScrollList() {
  const loadMoreRef = useRef();

  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
    isLoading
  } = useInfiniteQuery({
    queryKey: ['items'],
    queryFn: ({ pageParam = 0 }) => fetchItems(pageParam),
    getNextPageParam: (lastPage) => lastPage.nextCursor,
    initialPageParam: 0
  });

  // Intersection Observer for infinite scroll
  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && hasNextPage && !isFetchingNextPage) {
          fetchNextPage();
        }
      },
      { threshold: 0.1 }
    );

    if (loadMoreRef.current) {
      observer.observe(loadMoreRef.current);
    }

    return () => observer.disconnect();
  }, [hasNextPage, isFetchingNextPage, fetchNextPage]);

  if (isLoading) return <div>Loading...</div>;

  return (
    <div>
      {data.pages.map((page, i) => (
        <div key={i}>
          {page.items.map(item => (
            <div key={item.id}>{item.name}</div>
          ))}
        </div>
      ))}

      <div ref={loadMoreRef}>
        {isFetchingNextPage && <div>Loading more...</div>}
        {!hasNextPage && <div>No more items</div>}
      </div>
    </div>
  );
}
```

---

## Caching

Understanding and controlling the cache.

### Cache Configuration

```jsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,  // Data fresh for 5 minutes
      cacheTime: 10 * 60 * 1000,  // Cache persists for 10 minutes
      refetchOnWindowFocus: true,
      refetchOnMount: true,
      refetchOnReconnect: true,
    },
  },
});
```

### Manual Cache Updates

```jsx
function updateCache() {
  const queryClient = useQueryClient();

  // Set query data manually
  queryClient.setQueryData(['users'], (oldData) => {
    return [...oldData, newUser];
  });

  // Get query data
  const users = queryClient.getQueryData(['users']);

  // Set query data with exact match
  queryClient.setQueryData(['user', userId], userData);

  // Reset query data to undefined
  queryClient.setQueryData(['users'], undefined);

  // Get all queries
  const queries = queryClient.getQueriesData();

  // Get queries matching key
  const userQueries = queryClient.getQueriesData({ queryKey: ['users'] });
}
```

### Prefetching Data

```jsx
function UserList() {
  const queryClient = useQueryClient();

  // Prefetch user details when hovering over user
  const prefetchUser = (userId) => {
    queryClient.prefetchQuery({
      queryKey: ['user', userId],
      queryFn: () => fetchUser(userId),
      staleTime: 10 * 1000 // 10 seconds
    });
  };

  const { data: users } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers
  });

  return (
    <ul>
      {users?.map(user => (
        <li
          key={user.id}
          onMouseEnter={() => prefetchUser(user.id)}
        >
          {user.name}
        </li>
      ))}
    </ul>
  );
}
```

---

## Background Updates

Keep data fresh in the background.

### Automatic Refetching

```jsx
function AutoRefetchComponent() {
  const { data, isFetching, refetch } = useQuery({
    queryKey: ['liveData'],
    queryFn: fetchLiveData,
    refetchInterval: 5000,        // Refetch every 5 seconds
    refetchIntervalInBackground: true, // Refetch even when tab inactive
    refetchOnWindowFocus: true,   // Refetch when window focuses
    refetchOnMount: true,         // Refetch when component mounts
    refetchOnReconnect: true,     // Refetch when reconnecting
  });

  return (
    <div>
      {isFetching && <div>Updating data...</div>}
      <pre>{JSON.stringify(data, null, 2)}</pre>
      <button onClick={() => refetch()}>Manual Refetch</button>
    </div>
  );
}
```

### Conditional Background Refetch

```jsx
function ConditionalRefetch() {
  const [isActive, setIsActive] = useState(true);

  const { data } = useQuery({
    queryKey: ['realtimeData'],
    queryFn: fetchRealtimeData,
    refetchInterval: isActive ? 3000 : false, // Only refetch if active
    enabled: isActive
  });

  return (
    <div>
      <button onClick={() => setIsActive(!isActive)}>
        {isActive ? 'Pause Updates' : 'Resume Updates'}
      </button>
      <div>{data}</div>
    </div>
  );
}
```

---

## Error Handling

Handle errors gracefully.

### Basic Error Handling

```jsx
function ErrorHandlingExample() {
  const { data, error, isError, isLoading, refetch } = useQuery({
    queryKey: ['riskyData'],
    queryFn: fetchRiskyData,
    retry: 2, // Retry twice before showing error
    retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),
  });

  if (isLoading) return <LoadingSpinner />;

  if (isError) {
    return (
      <div>
        <h3>Error: {error.message}</h3>
        <button onClick={() => refetch()}>Try Again</button>
        <details>
          <summary>Error Details</summary>
          <pre>{error.stack}</pre>
        </details>
      </div>
    );
  }

  return <div>{data}</div>;
}
```

### Global Error Handling

```jsx
// Setup global error handler
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      onError: (error) => {
        console.error('Global query error:', error);
        toast.error(error.message);
      },
    },
    mutations: {
      onError: (error) => {
        console.error('Global mutation error:', error);
        toast.error(error.message);
      },
    },
  },
});

// Error boundary integration
import { ErrorBoundary } from 'react-error-boundary';

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div>
      <h2>Something went wrong</h2>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

function App() {
  return (
    <ErrorBoundary FallbackComponent={ErrorFallback}>
      <MyComponent />
    </ErrorBoundary>
  );
}
```

---

## Devtools

React Query Devtools for debugging.

### Setup

```jsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <YourApp />
      <ReactQueryDevtools
        initialIsOpen={false}
        buttonPosition="bottom-right"
        position="bottom-right"
      />
    </QueryClientProvider>
  );
}
```

### Devtools Configuration

```jsx
<ReactQueryDevtools
  initialIsOpen={false}        // Start closed
  panelProps={{}}              // Props for panel div
  closeButtonProps={{}}        // Props for close button
  toggleButtonProps={{}}       // Props for toggle button
  position="bottom-right"      // Position: 'top-left', 'top-right', 'bottom-left', 'bottom-right'
/>
```

---

## Common Patterns

### Pattern 1: Custom Query Hook

```jsx
// hooks/useUsers.js
import { useQuery } from '@tanstack/react-query';

export function useUsers(filters = {}) {
  return useQuery({
    queryKey: ['users', filters],
    queryFn: () => fetchUsers(filters),
    staleTime: 5 * 60 * 1000,
    select: (data) => data.map(user => ({
      ...user,
      fullName: `${user.firstName} ${user.lastName}`
    }))
  });
}

// Usage in component
function UsersPage() {
  const { data: users, isLoading, error } = useUsers({ active: true });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return <UserList users={users} />;
}
```

### Pattern 2: Request Cancellation

```jsx
function SearchComponent() {
  const [searchTerm, setSearchTerm] = useState('');

  const { data, isLoading, isFetching } = useQuery({
    queryKey: ['search', searchTerm],
    queryFn: async ({ signal }) => {
      const response = await fetch(`/api/search?q=${searchTerm}`, { signal });
      return response.json();
    },
    enabled: searchTerm.length > 2, // Only search if term length > 2
  });

  return (
    <div>
      <input
        type="text"
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search..."
      />
      {isFetching && <div>Searching...</div>}
      {data && <SearchResults results={data} />}
    </div>
  );
}
```

### Pattern 3: Debounced Search

```jsx
import { useDebounce } from './hooks/useDebounce';

function DebouncedSearch() {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearch = useDebounce(searchTerm, 500);

  const { data, isLoading } = useQuery({
    queryKey: ['search', debouncedSearch],
    queryFn: () => searchAPI(debouncedSearch),
    enabled: debouncedSearch.length > 0
  });

  return (
    <div>
      <input
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search..."
      />
      {isLoading && <div>Loading...</div>}
      {data && <Results data={data} />}
    </div>
  );
}
```

### Pattern 4: Suspense Mode

```jsx
// Enable suspense in query client
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      suspense: true,
    },
  },
});

// Component using suspense
function UserProfile({ userId }) {
  // This will suspend until data is ready
  const { data } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
  });

  return <div>{data.name}</div>;
}

// Parent component with Suspense boundary
function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <UserProfile userId={123} />
    </Suspense>
  );
}
```

---

## Troubleshooting

Problem 1: Data not updating

Solutions:
- Check staleTime configuration
- Manually invalidate queries after mutations
- Force refetch with refetch() method

```jsx
// Force refetch
const { refetch } = useQuery({ queryKey: ['data'], queryFn: fetchData });
refetch();

// Invalidate cache
queryClient.invalidateQueries({ queryKey: ['data'] });
```

Problem 2: Stale data showing

Solutions:
- Reduce staleTime
- Enable refetchOnWindowFocus
- Use refetchInterval for realtime data

```jsx
useQuery({
  queryKey: ['data'],
  queryFn: fetchData,
  staleTime: 0, // Always stale
  refetchOnWindowFocus: true,
  refetchInterval: 10000, // Refetch every 10 seconds
});
```

Problem 3: Memory leaks

Solutions:
- Cancel queries on unmount
- Use AbortController properly
- Avoid infinite refetch loops

```jsx
useQuery({
  queryKey: ['data'],
  queryFn: ({ signal }) => fetchData({ signal }),
});
```

Problem 4: Error handling not working

Solutions:
- Always throw errors in queryFn
- Use error boundaries
- Check network tab for actual errors

```jsx
const queryFn = async () => {
  const response = await fetch('/api/data');
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  return response.json();
};
```

Problem 5: Performance issues

Solutions:
- Use select to extract only needed data
- Enable keepPreviousData for pagination
- Use staleTime to reduce refetches

```jsx
// Select only needed fields
const { data } = useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers,
  select: (data) => data.map(user => ({ id: user.id, name: user.name }))
});

// Keep previous data while fetching
useQuery({
  queryKey: ['posts', page],
  queryFn: () => fetchPosts(page),
  keepPreviousData: true
});
```

---

## Quick Reference

useQuery Return Values

| Property | Description |
|----------|-------------|
| data | Query result data |
| isLoading | First load in progress |
| isFetching | Any fetch in progress |
| isError | Error occurred |
| error | Error object |
| refetch | Manual refetch function |
| isSuccess | Query succeeded |
| isIdle | Query not yet executed |

useMutation Return Values

| Property | Description |
|----------|-------------|
| mutate | Trigger mutation |
| mutateAsync | Promise-based mutate |
| isLoading | Mutation in progress |
| isError | Error occurred |
| error | Error object |
| reset | Reset mutation state |

Query Key Patterns

| Pattern | Example |
|---------|---------|
| Scalar | ['users'] |
| Array | ['users', userId] |
| Object | ['users', { role: 'admin' }] |
| Nested | ['users', userId, 'posts'] |

---

## Useful Resources

- TanStack Query Documentation: https://tanstack.com/query/latest
- React Query Examples: https://tanstack.com/query/latest/docs/react/examples/react/auto-refetching
- GitHub Repository: https://github.com/TanStack/query

---

Created for React developers using TanStack Query
