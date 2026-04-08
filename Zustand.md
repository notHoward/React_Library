# Zustand - Complete Beginner's Guide

A comprehensive from-scratch guide to Zustand state management.

## Table of Contents
1. [What is Zustand](#what-is-zustand)
2. [Core Concepts](#core-concepts)
3. [Installation](#installation)
4. [Basic Setup](#basic-setup)
5. [Creating Stores](#creating-stores)
6. [Using Stores in Components](#using-stores-in-components)
7. [Actions Explained](#actions-explained)
8. [Selectors Explained](#selectors-explained)
9. [Async Actions](#async-actions)
10. [Middleware](#middleware)
11. [Store Slices](#store-slices)
12. [Complete Examples](#complete-examples)
13. [API Reference](#api-reference)

---

## What is Zustand

Zustand is a small, fast, and scalable state management library for React.

**What Zustand does:**
- Stores global state in a central store
- Makes state available to any component
- Minimal boilerplate (much less than Redux)
- No provider wrapping needed (optional)

**The Zustand Flow:**
```
Component → calls action → updates state → component re-renders
```

**Zustand vs Redux:**
```jsx
// Redux - lots of boilerplate
const slice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: { increment: (state) => { state.value++ } }
});
// Need Provider, store setup, etc.

// Zustand - simple and direct
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 }))
}));
// That's it! No Provider needed!
```

## Core Concepts

### The 3 Main Parts of Zustand

```jsx
Store           // 1. Container that holds state and actions
create()        // 2. Function that creates the store
useStore()      // 3. Hook to access store in components
```

### Zustand vs Other State Management

| Feature | Zustand | Redux | Context |
|---------|---------|-------|---------|
| Boilerplate | Minimal | Lots | Moderate |
| Performance | Excellent | Good | Poor |
| Bundle size | ~1KB | ~15KB | Built-in |
| Learning curve | Easy | Steep | Easy |
| Provider needed | No | Yes | Yes |

## Installation

```bash
npm install zustand
```

## Basic Setup

### Step 1: Create a Store

```jsx
// stores/counterStore.js
import { create } from 'zustand';

// 1. Create store with state and actions
const useCounterStore = create((set) => ({
  // State
  count: 0,

  // Actions
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}));

export default useCounterStore;
```

### Step 2: Use Store in Component

```jsx
// components/Counter.jsx
import useCounterStore from '../stores/counterStore';

function Counter() {
  // 2. Use the store hook to get state and actions
  const { count, increment, decrement, reset } = useCounterStore();

  return (
    <div>
      <div>Count: {count}</div>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

// No Provider needed! Just use the hook directly!
```

### Step 3: No Provider Required!

```jsx
// App.jsx - No wrapping needed!
import Counter from './components/Counter';

function App() {
  return (
    <div>
      <Counter />  {/* Works directly! */}
      <AnotherComponent />  {/* Also works! */}
    </div>
  );
}
```

---

## Creating Stores

### Basic Store with Multiple Values

```jsx
import { create } from 'zustand';

const useStore = create((set) => ({
  // Multiple state values
  bears: 0,
  fish: 0,
  birds: 0,

  // Actions for each
  addBear: () => set((state) => ({ bears: state.bears + 1 })),
  removeBear: () => set((state) => ({ bears: state.bears - 1 })),
  addFish: () => set((state) => ({ fish: state.fish + 1 })),
  removeFish: () => set((state) => ({ fish: state.fish - 1 })),
  addBird: () => set((state) => ({ birds: state.birds + 1 })),

  // Action that updates multiple values
  reset: () => set({ bears: 0, fish: 0, birds: 0 }),
}));
```

### Store with Objects

```jsx
const useUserStore = create((set) => ({
  // Nested object state
  user: {
    name: '',
    email: '',
    age: 0,
    address: {
      street: '',
      city: '',
      zipCode: ''
    }
  },

  // Update entire user object
  setUser: (userData) => set({ user: userData }),

  // Update partial user (merges with existing)
  updateUser: (userData) => set((state) => ({
    user: { ...state.user, ...userData }
  })),

  // Update nested property
  updateAddress: (addressData) => set((state) => ({
    user: {
      ...state.user,
      address: { ...state.user.address, ...addressData }
    }
  })),

  // Reset user
  clearUser: () => set({
    user: {
      name: '',
      email: '',
      age: 0,
      address: { street: '', city: '', zipCode: '' }
    }
  })
}));
```

### Store with Arrays

```jsx
const useTodoStore = create((set) => ({
  // Array state
  todos: [],

  // Add to array
  addTodo: (text) => set((state) => ({
    todos: [...state.todos, {
      id: Date.now(),
      text,
      completed: false
    }]
  })),

  // Update item in array
  toggleTodo: (id) => set((state) => ({
    todos: state.todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    )
  })),

  // Remove from array
  deleteTodo: (id) => set((state) => ({
    todos: state.todos.filter(todo => todo.id !== id)
  })),

  // Update specific item
  updateTodo: (id, text) => set((state) => ({
    todos: state.todos.map(todo =>
      todo.id === id ? { ...todo, text } : todo
    )
  })),

  // Clear all todos
  clearTodos: () => set({ todos: [] }),

  // Remove completed todos
  clearCompleted: () => set((state) => ({
    todos: state.todos.filter(todo => !todo.completed)
  }))
}));
```

### Store with Computed Values

```jsx
const useStore = create((set, get) => ({
  // State
  todos: [
    { id: 1, text: 'Learn Zustand', completed: true },
    { id: 2, text: 'Build app', completed: false }
  ],
  filter: 'all',

  // Actions
  addTodo: (text) => set((state) => ({
    todos: [...state.todos, { id: Date.now(), text, completed: false }]
  })),

  setFilter: (filter) => set({ filter }),

  // Computed values (accessed via get())
  get filteredTodos: () => {
    const { todos, filter } = get();
    if (filter === 'active') {
      return todos.filter(t => !t.completed);
    }
    if (filter === 'completed') {
      return todos.filter(t => t.completed);
    }
    return todos;
  },

  get stats: () => {
    const todos = get().todos;
    return {
      total: todos.length,
      completed: todos.filter(t => t.completed).length,
      active: todos.filter(t => !t.completed).length
    };
  }
}));
```

---

## Using Stores in Components

### Basic Usage

```jsx
import useCounterStore from './stores/counterStore';

function Counter() {
  // Get entire store
  const store = useCounterStore();

  // Get specific values (recommended - prevents unnecessary re-renders)
  const count = useCounterStore((state) => state.count);
  const increment = useCounterStore((state) => state.increment);

  // Get multiple values
  const { count, increment, decrement } = useCounterStore();

  return (
    <div>
      <div>Count: {count}</div>
      <button onClick={increment}>+</button>
    </div>
  );
}
```

### Preventing Unnecessary Re-renders

```jsx
function UserProfile() {
  // ❌ BAD - Re-renders when ANY state changes
  const store = useUserStore();  // Re-renders on every change

  // ✅ GOOD - Only re-renders when 'user' changes
  const user = useUserStore((state) => state.user);

  // ✅ GOOD - Only re-renders when 'name' changes
  const name = useUserStore((state) => state.user.name);

  // ✅ GOOD - Multiple selectors with shallow comparison
  const { name, email } = useUserStore(
    (state) => ({ name: state.user.name, email: state.user.email }),
    shallow  // Only re-render if name OR email changes
  );

  return <div>{user.name}</div>;
}
```

### Shallow Comparison

```jsx
import { shallow } from 'zustand/shallow';

function TodoList() {
  // Without shallow - re-renders every time (creates new object)
  const { todos, addTodo } = useTodoStore();  // ❌ Bad

  // With shallow - only re-renders when values actually change
  const { todos, addTodo } = useTodoStore(
    (state) => ({ todos: state.todos, addTodo: state.addTodo }),
    shallow  // ✅ Good
  );

  // For arrays
  const todoCount = useTodoStore(
    (state) => state.todos.length,
    shallow
  );

  return <div>{todos.length} todos</div>;
}
```

### Complete Component Example

```jsx
import { shallow } from 'zustand/shallow';
import useTodoStore from './stores/todoStore';

function TodoApp() {
  // Select multiple values with shallow comparison
  const { todos, addTodo, toggleTodo, deleteTodo, filter, setFilter } =
    useTodoStore(
      (state) => ({
        todos: state.todos,
        addTodo: state.addTodo,
        toggleTodo: state.toggleTodo,
        deleteTodo: state.deleteTodo,
        filter: state.filter,
        setFilter: state.setFilter
      }),
      shallow
    );

  // Select derived data
  const stats = useTodoStore((state) => ({
    total: state.todos.length,
    completed: state.todos.filter(t => t.completed).length,
    active: state.todos.filter(t => !t.completed).length
  }));

  // Filtered todos
  const filteredTodos = useTodoStore((state) => {
    const todos = state.todos;
    const filter = state.filter;

    if (filter === 'active') {
      return todos.filter(t => !t.completed);
    }
    if (filter === 'completed') {
      return todos.filter(t => t.completed);
    }
    return todos;
  });

  const handleAddTodo = (text) => {
    if (text.trim()) {
      addTodo(text);
    }
  };

  return (
    <div>
      <h1>Todo App</h1>

      {/* Stats */}
      <div>
        <span>Total: {stats.total}</span>
        <span>Active: {stats.active}</span>
        <span>Completed: {stats.completed}</span>
      </div>

      {/* Add Todo */}
      <input
        onKeyPress={(e) => {
          if (e.key === 'Enter') {
            handleAddTodo(e.target.value);
            e.target.value = '';
          }
        }}
      />

      {/* Filters */}
      <div>
        <button onClick={() => setFilter('all')}>All</button>
        <button onClick={() => setFilter('active')}>Active</button>
        <button onClick={() => setFilter('completed')}>Completed</button>
      </div>

      {/* Todo List */}
      <ul>
        {filteredTodos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{
              textDecoration: todo.completed ? 'line-through' : 'none'
            }}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## Actions Explained

### Basic Actions with set()

```jsx
const useStore = create((set) => ({
  count: 0,

  // Action using function (recommended)
  increment: () => set((state) => ({ count: state.count + 1 })),

  // Action using object
  decrement: () => set({ count: 5 }),  // Sets to exact value

  // Action with payload
  setCount: (value) => set({ count: value }),

  // Action with conditional logic
  incrementIfUnderTen: () => set((state) => ({
    count: state.count < 10 ? state.count + 1 : state.count
  })),

  // Action updating multiple values
  reset: () => set({ count: 0, otherValue: 'default' })
}));
```

### Actions with get() - Accessing Current State

```jsx
const useStore = create((set, get) => ({
  count: 0,
  history: [],

  // Action using get() to read current state
  increment: () => {
    const currentCount = get().count;
    set({ count: currentCount + 1 });
  },

  // Action with side effects
  incrementAndSave: () => {
    const newCount = get().count + 1;
    set({
      count: newCount,
      history: [...get().history, newCount]
    });
  },

  // Action with validation
  setCount: (value) => {
    if (value < 0) return;  // Don't allow negative
    set({ count: value });
  },

  // Complex action using multiple state values
  incrementUntilEven: () => {
    let newCount = get().count;
    do {
      newCount++;
    } while (newCount % 2 !== 0);
    set({ count: newCount });
  }
}));
```

### Async Actions

```jsx
const useStore = create((set, get) => ({
  users: [],
  loading: false,
  error: null,

  // Async action
  fetchUsers: async () => {
    set({ loading: true, error: null });

    try {
      const response = await fetch('/api/users');
      const data = await response.json();
      set({ users: data, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  },

  // Async with current state
  addUser: async (userData) => {
    try {
      const response = await fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(userData),
        headers: { 'Content-Type': 'application/json' }
      });
      const newUser = await response.json();

      // Update state using current users
      set((state) => ({
        users: [...state.users, newUser]
      }));
    } catch (error) {
      set({ error: error.message });
    }
  },

  // Async with optimistic update
  deleteUser: async (userId) => {
    // Save current state for rollback
    const previousUsers = get().users;

    // Optimistic update
    set((state) => ({
      users: state.users.filter(user => user.id !== userId)
    }));

    try {
      await fetch(`/api/users/${userId}`, { method: 'DELETE' });
    } catch (error) {
      // Rollback on error
      set({ users: previousUsers, error: error.message });
    }
  }
}));
```

### Actions with Callbacks

```jsx
const useStore = create((set) => ({
  value: 0,

  // Action with callback
  setValue: (value, callback) => {
    set({ value });
    if (callback) callback();
  },

  // Action that returns a value
  incrementAndGet: () => {
    let newValue;
    set((state) => {
      newValue = state.value + 1;
      return { value: newValue };
    });
    return newValue;
  }
}));

// Usage in component
function MyComponent() {
  const setValue = useStore((state) => state.setValue);

  const handleClick = () => {
    setValue(5, () => {
      console.log('Value was set to 5');
    });
  };

  const incrementAndGet = useStore((state) => state.incrementAndGet);

  const handleIncrement = () => {
    const newValue = incrementAndGet();
    console.log('New value:', newValue);
  };
}
```

---

## Selectors Explained

### Basic Selectors

```jsx
import useStore from './store';

function MyComponent() {
  // Select single value
  const count = useStore((state) => state.count);

  // Select nested value
  const userName = useStore((state) => state.user.name);

  // Select multiple values (object)
  const { count, user } = useStore((state) => ({
    count: state.count,
    user: state.user
  }));

  // Select with calculation
  const doubledCount = useStore((state) => state.count * 2);

  // Select filtered data
  const activeTodos = useStore((state) =>
    state.todos.filter(todo => !todo.completed)
  );

  return <div>{count}</div>;
}
```

### External Selectors (Reusable)

```jsx
// selectors.js
export const selectCount = (state) => state.count;
export const selectUser = (state) => state.user;
export const selectUserName = (state) => state.user.name;
export const selectTodos = (state) => state.todos;

export const selectActiveTodos = (state) =>
  state.todos.filter(todo => !todo.completed);

export const selectTodoStats = (state) => ({
  total: state.todos.length,
  completed: state.todos.filter(t => t.completed).length,
  active: state.todos.filter(t => !t.completed).length
});

// Component
import useStore from './store';
import { selectCount, selectActiveTodos } from './selectors';

function MyComponent() {
  const count = useStore(selectCount);
  const activeTodos = useStore(selectActiveTodos);

  return <div>{count}</div>;
}
```

### Selector with Props

```jsx
// Selector factory
const selectTodoById = (id) => (state) =>
  state.todos.find(todo => todo.id === id);

// Component
function TodoItem({ id }) {
  const todo = useStore(selectTodoById(id));

  if (!todo) return null;
  return <div>{todo.text}</div>;
}

// Multiple instances with different IDs
function TodoList({ ids }) {
  return (
    <div>
      {ids.map(id => (
        <TodoItem key={id} id={id} />
      ))}
    </div>
  );
}
```

### Memoized Selectors

```jsx
import { createSelector } from 'zustand/middleware';

const useStore = create((set) => ({
  todos: [],
  filter: 'all',
  searchTerm: '',

  // ... actions
}));

// Memoized selector (only recalculates when inputs change)
const selectFilteredTodos = createSelector(
  [(state) => state.todos, (state) => state.filter, (state) => state.searchTerm],
  (todos, filter, searchTerm) => {
    let filtered = todos;

    if (filter === 'active') {
      filtered = filtered.filter(t => !t.completed);
    } else if (filter === 'completed') {
      filtered = filtered.filter(t => t.completed);
    }

    if (searchTerm) {
      filtered = filtered.filter(t =>
        t.text.toLowerCase().includes(searchTerm.toLowerCase())
      );
    }

    return filtered;
  }
);

// Usage
function TodoList() {
  const filteredTodos = useStore(selectFilteredTodos);
  // Only re-renders when filteredTodos actually changes
}
```

---

## Async Actions

### Basic Async Action

```jsx
const useUserStore = create((set, get) => ({
  users: [],
  loading: false,
  error: null,

  fetchUsers: async () => {
    // Set loading state
    set({ loading: true, error: null });

    try {
      const response = await fetch('/api/users');
      const data = await response.json();

      // Update on success
      set({ users: data, loading: false });
    } catch (error) {
      // Update on error
      set({ error: error.message, loading: false });
    }
  }
}));

// Component
function UserList() {
  const { users, loading, error, fetchUsers } = useUserStore();

  useEffect(() => {
    fetchUsers();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### Async Action with Parameters

```jsx
const useStore = create((set, get) => ({
  user: null,
  loading: false,

  fetchUserById: async (userId) => {
    set({ loading: true });

    try {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      set({ user: data, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  }
}));

// Component
function UserProfile({ userId }) {
  const { user, loading, fetchUserById } = useStore();

  useEffect(() => {
    fetchUserById(userId);
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  return <div>{user?.name}</div>;
}
```

### Async Action with Loading State

```jsx
const useStore = create((set, get) => ({
  data: null,
  status: 'idle', // 'idle', 'loading', 'success', 'error'
  error: null,

  fetchData: async () => {
    set({ status: 'loading', error: null });

    try {
      const response = await fetch('/api/data');
      const data = await response.json();
      set({ data, status: 'success' });
    } catch (error) {
      set({ error: error.message, status: 'error' });
    }
  },

  reset: () => {
    set({ data: null, status: 'idle', error: null });
  }
}));

// Component
function DataFetcher() {
  const { data, status, error, fetchData, reset } = useStore();

  if (status === 'idle') {
    return <button onClick={fetchData}>Load Data</button>;
  }

  if (status === 'loading') {
    return <div>Loading...</div>;
  }

  if (status === 'error') {
    return (
      <div>
        <div>Error: {error}</div>
        <button onClick={reset}>Try Again</button>
      </div>
    );
  }

  return (
    <div>
      <pre>{JSON.stringify(data, null, 2)}</pre>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### Async Action with AbortController

```jsx
const useStore = create((set, get) => ({
  data: null,
  loading: false,
  abortController: null,

  fetchData: async (query) => {
    // Cancel previous request
    if (get().abortController) {
      get().abortController.abort();
    }

    const abortController = new AbortController();
    set({ loading: true, abortController });

    try {
      const response = await fetch(`/api/search?q=${query}`, {
        signal: abortController.signal
      });
      const data = await response.json();

      set({ data, loading: false, abortController: null });
    } catch (error) {
      if (error.name !== 'AbortError') {
        set({ error: error.message, loading: false });
      }
    }
  },

  cancelFetch: () => {
    const { abortController } = get();
    if (abortController) {
      abortController.abort();
      set({ loading: false, abortController: null });
    }
  }
}));
```

---

## Middleware

### Persist Middleware (Save to localStorage)

```jsx
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';

const useStore = create(
  persist(
    (set, get) => ({
      user: null,
      token: null,
      preferences: { theme: 'light' },

      login: (user, token) => set({ user, token }),
      logout: () => set({ user: null, token: null }),
      setTheme: (theme) => set((state) => ({
        preferences: { ...state.preferences, theme }
      }))
    }),
    {
      name: 'app-storage',           // Key in localStorage
      storage: createJSONStorage(() => localStorage), // or sessionStorage
      partialize: (state) => ({      // Only persist specific fields
        user: state.user,
        token: state.token,
        preferences: state.preferences
      }),
      onRehydrateStorage: () => (state, error) => {
        if (error) {
          console.error('Rehydration error:', error);
        } else {
          console.log('Rehydration successful');
        }
      }
    }
  )
);
```

### DevTools Middleware

```jsx
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';

const useStore = create(
  devtools(
    (set, get) => ({
      count: 0,

      increment: () => set(
        (state) => ({ count: state.count + 1 }),
        false,  // Replace flag
        'counter/increment'  // Action name in devtools
      ),

      decrement: () => set(
        (state) => ({ count: state.count - 1 }),
        false,
        'counter/decrement'
      )
    }),
    { name: 'MyAppStore' }  // Store name in devtools
  )
);
```

### Combining Multiple Middleware

```jsx
import { create } from 'zustand';
import { persist, devtools } from 'zustand/middleware';

const useStore = create(
  devtools(
    persist(
      (set, get) => ({
        user: null,
        token: null,

        login: (user, token) => set({ user, token }),
        logout: () => set({ user: null, token: null })
      }),
      { name: 'auth-storage' }
    ),
    { name: 'AuthStore' }
  )
);
```

### Immer Middleware (Simpler Immutable Updates)

```jsx
import { create } from 'zustand';
import { immer } from 'zustand/middleware/immer';

const useTodoStore = create(
  immer((set) => ({
    todos: [],

    addTodo: (text) => set((state) => {
      // Mutate state directly (Immer handles immutability)
      state.todos.push({
        id: Date.now(),
        text,
        completed: false
      });
    }),

    toggleTodo: (id) => set((state) => {
      const todo = state.todos.find(t => t.id === id);
      if (todo) {
        todo.completed = !todo.completed;
      }
    }),

    updateTodo: (id, text) => set((state) => {
      const todo = state.todos.find(t => t.id === id);
      if (todo) {
        todo.text = text;
      }
    }),

    deleteTodo: (id) => set((state) => {
      const index = state.todos.findIndex(t => t.id === id);
      if (index !== -1) {
        state.todos.splice(index, 1);
      }
    })
  }))
);
```

---

## Store Slices

### Separating Store into Slices

```jsx
// slices/userSlice.js
const createUserSlice = (set, get) => ({
  user: null,
  isLoading: false,

  login: async (email, password) => {
    set({ isLoading: true });
    try {
      const user = await api.login(email, password);
      set({ user, isLoading: false });
    } catch (error) {
      set({ error: error.message, isLoading: false });
    }
  },

  logout: () => set({ user: null })
});

// slices/todoSlice.js
const createTodoSlice = (set, get) => ({
  todos: [],

  addTodo: (text) => set((state) => ({
    todos: [...state.todos, { id: Date.now(), text, completed: false }]
  })),

  toggleTodo: (id) => set((state) => ({
    todos: state.todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    )
  }))
});

// slices/uiSlice.js
const createUISlice = (set) => ({
  theme: 'light',
  sidebarOpen: true,

  toggleTheme: () => set((state) => ({
    theme: state.theme === 'light' ? 'dark' : 'light'
  })),

  toggleSidebar: () => set((state) => ({
    sidebarOpen: !state.sidebarOpen
  }))
});

// store/index.js
import { create } from 'zustand';
import { createUserSlice } from './slices/userSlice';
import { createTodoSlice } from './slices/todoSlice';
import { createUISlice } from './slices/uiSlice';

const useStore = create((set, get) => ({
  ...createUserSlice(set, get),
  ...createTodoSlice(set, get),
  ...createUISlice(set, get)
}));

export default useStore;
```

### Using Slices in Components

```jsx
import useStore from './store';

function App() {
  // Get from different slices
  const user = useStore((state) => state.user);
  const todos = useStore((state) => state.todos);
  const theme = useStore((state) => state.theme);

  const login = useStore((state) => state.login);
  const addTodo = useStore((state) => state.addTodo);
  const toggleTheme = useStore((state) => state.toggleTheme);

  return (
    <div>
      <button onClick={toggleTheme}>Theme: {theme}</button>
      {/* ... */}
    </div>
  );
}
```

---

## Complete Examples

### Example 1: Shopping Cart

```jsx
// stores/cartStore.js
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

const useCartStore = create(
  persist(
    (set, get) => ({
      items: [],
      totalQuantity: 0,
      totalAmount: 0,

      addItem: (product) => {
        const existingItem = get().items.find(item => item.id === product.id);

        if (existingItem) {
          set((state) => ({
            items: state.items.map(item =>
              item.id === product.id
                ? { ...item, quantity: item.quantity + 1, totalPrice: item.totalPrice + product.price }
                : item
            ),
            totalQuantity: state.totalQuantity + 1,
            totalAmount: state.totalAmount + product.price
          }));
        } else {
          set((state) => ({
            items: [...state.items, { ...product, quantity: 1, totalPrice: product.price }],
            totalQuantity: state.totalQuantity + 1,
            totalAmount: state.totalAmount + product.price
          }));
        }
      },

      removeItem: (productId) => {
        const item = get().items.find(item => item.id === productId);

        if (item.quantity === 1) {
          set((state) => ({
            items: state.items.filter(item => item.id !== productId),
            totalQuantity: state.totalQuantity - 1,
            totalAmount: state.totalAmount - item.price
          }));
        } else {
          set((state) => ({
            items: state.items.map(item =>
              item.id === productId
                ? { ...item, quantity: item.quantity - 1, totalPrice: item.totalPrice - item.price }
                : item
            ),
            totalQuantity: state.totalQuantity - 1,
            totalAmount: state.totalAmount - item.price
          }));
        }
      },

      clearCart: () => set({
        items: [],
        totalQuantity: 0,
        totalAmount: 0
      }),

      getCartTotal: () => get().totalAmount,
      getItemCount: () => get().totalQuantity
    }),
    { name: 'shopping-cart' }
  )
);

// components/Cart.jsx
import useCartStore from '../stores/cartStore';

function Cart() {
  const { items, totalAmount, totalQuantity, removeItem, clearCart } = useCartStore();

  if (items.length === 0) {
    return <div>Cart is empty</div>;
  }

  return (
    <div>
      <h2>Shopping Cart ({totalQuantity} items)</h2>

      {items.map(item => (
        <div key={item.id}>
          <span>{item.name}</span>
          <span>${item.price}</span>
          <span>Quantity: {item.quantity}</span>
          <span>Total: ${item.totalPrice}</span>
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}

      <div>Total Amount: ${totalAmount}</div>
      <button onClick={clearCart}>Clear Cart</button>
    </div>
  );
}
```

### Example 2: Authentication Store

```jsx
// stores/authStore.js
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

const useAuthStore = create(
  persist(
    (set, get) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      isLoading: false,
      error: null,

      login: async (email, password) => {
        set({ isLoading: true, error: null });

        try {
          const response = await fetch('/api/login', {
            method: 'POST',
            body: JSON.stringify({ email, password }),
            headers: { 'Content-Type': 'application/json' }
          });

          if (!response.ok) throw new Error('Login failed');

          const { user, token } = await response.json();
          set({ user, token, isAuthenticated: true, isLoading: false });

          return true;
        } catch (error) {
          set({ error: error.message, isLoading: false });
          return false;
        }
      },

      register: async (userData) => {
        set({ isLoading: true, error: null });

        try {
          const response = await fetch('/api/register', {
            method: 'POST',
            body: JSON.stringify(userData),
            headers: { 'Content-Type': 'application/json' }
          });

          if (!response.ok) throw new Error('Registration failed');

          const { user, token } = await response.json();
          set({ user, token, isAuthenticated: true, isLoading: false });

          return true;
        } catch (error) {
          set({ error: error.message, isLoading: false });
          return false;
        }
      },

      logout: () => {
        set({ user: null, token: null, isAuthenticated: false, error: null });
      },

      updateProfile: async (updates) => {
        const { user, token } = get();

        try {
          const response = await fetch(`/api/users/${user.id}`, {
            method: 'PUT',
            body: JSON.stringify(updates),
            headers: {
              'Content-Type': 'application/json',
              'Authorization': `Bearer ${token}`
            }
          });

          const updatedUser = await response.json();
          set({ user: updatedUser });

          return true;
        } catch (error) {
          set({ error: error.message });
          return false;
        }
      },

      clearError: () => set({ error: null })
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ user: state.user, token: state.token })
    }
  )
);

// components/LoginForm.jsx
import { useState } from 'react';
import useAuthStore from '../stores/authStore';

function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const { login, isLoading, error, clearError } = useAuthStore();

  const handleSubmit = async (e) => {
    e.preventDefault();
    const success = await login(email, password);
    if (success) {
      // Redirect to dashboard
      window.location.href = '/dashboard';
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      {error && <div className="error">{error}</div>}
      <button type="submit" disabled={isLoading}>
        {isLoading ? 'Logging in...' : 'Login'}
      </button>
      <button type="button" onClick={clearError}>Clear Error</button>
    </form>
  );
}
```

---

## API Reference

### Core Functions

| Function | Purpose | Example |
|----------|---------|---------|
| create() | Create a store | `create((set) => ({ ... }))` |
| set() | Update state | `set({ count: 5 })` |
| get() | Get current state | `const count = get().count` |

### Middleware

| Middleware | Purpose | Example |
|------------|---------|---------|
| persist | Save to storage | `persist(store, { name: 'key' })` |
| devtools | Debug with Redux DevTools | `devtools(store, { name: 'Store' })` |
| immer | Immutable updates | `immer(store)` |
| subscribeWithSelector | Subscribe with selectors | `subscribeWithSelector(store)` |

### Store Methods

```jsx
const store = useStore();

// Get current state
store.getState();

// Update state
store.setState({ count: 5 });

// Subscribe to changes
const unsubscribe = store.subscribe((state, prevState) => {
  console.log('State changed', state);
});

// Unsubscribe
unsubscribe();

// Destroy store
store.destroy();
```

## Troubleshooting

Problem 1: Component not re-rendering

Solutions:
- Check selector is correct
- Use shallow for objects/arrays
- Ensure state is actually changing

Problem 2: State not persisting

Solutions:
- Add persist middleware
- Check storage is working
- Verify name is unique

Problem 3: TypeScript errors

Solutions:
- Define store interface
- Use type inference
- Add proper generics

Problem 4: Async actions not working

Solutions:
- Use async/await properly
- Handle errors with try/catch
- Update state after async operations

## Useful Resources

- Zustand Docs: https://docs.pmnd.rs/zustand
- GitHub Repository: https://github.com/pmndrs/zustand
- Examples: https://github.com/pmndrs/zustand/tree/main/examples

---

Created for beginners learning Zustand
